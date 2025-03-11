---
title: ["[KalmarCTF2025 - web] DNXSS-over-HTTPS"]
date: 2025-03-10 09:00:00 +0900
categories: [writeup, ctf]
tags: [web, xss]
---
<a href="/assets/bin/ctf/dnxss/dnxss-over-https.zip" download>dnxss-over-https.zip Download</a>  

## Descryption
```
Do you like DNS-over-HTTPS? Well, I'm proxying https://dns.google/!
Would be cool if you can find an XSS!

Report to admin locally:
curl http://localhost:8008/report -H "Content-Type: application/json" -d '{"url":"http://proxy/"}'

Report to admin for the real flag:
curl https://dnxss.chal-kalmarc.tf/report -H "Content-Type: application/json" -d '{"url":"http://proxy/"}'
```

## 코드 리뷰
### nginx.conf
```apacheconf
server {
    listen 80;

    location / {
      proxy_pass https://dns.google;
      add_header Content-Type text/html always;
    }

    location /report {
      proxy_pass http://adminbot:3000;
    }
}
```
- 기본 URL(`/`) 요청이 구글의 DNS-over-HTTPS 서비스(`https://dns.google`)로 프록시된다.
- 응답 헤더가 항상 강제로 `/text/html`로 설정된다. (크리티컬한 취약점이 존재하는 부분)
즉, DNS-over-HTTPS의 정상적인 JSON응답이 브라우저에 의해 HTML로 해석될 수 있는 취약점이 발생한다.

## 취약점 원리
![](assets/img/writeup/ctf/dnxss/unicode_escape.png)  
일반적으로 DNS-over-HTTPS는 JSON형식으로 응답을 반환한다. JSON 응답의 특성상 `<`, `>` 같은 문자는 `\u003c`, `\u003e` 등으로 이스케이프 되어 XSS 공격이 어렵게 된다.  
하지만 `ct=application/dns-message`를 사용하면 이를 우회할 수 있다. (참조: [DoH JSON API](https://developers.google.com/speed/public-dns/docs/doh/json))  
 - `ct=application/dns-message` 파라미터는 DNS 응답을 JSON 대신 바이너리(DNS Wire Format) 형태로 받기 위해 쓰는 옵션이다.
 - DNS 서버가 유효하지 않은 도메인 질의를 받으면 HTML 형식으로 에러 페이지를 반환하고, 입력한 문자열을 필터링 없이 HTML 안에 그대로 반영한다.
![](assets/img/writeup/ctf/dnxss/exploit.png)

