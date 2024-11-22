---
title: ["SQLi Cheat Sheet"]
date: 2024-11-01 09:00:00 +0900
categories: [sheatsheet, sqli]
tags: [web, los, lord of sqli, cheatsheet]
---

![자료](assets/img/cheatsheet/los_all_clear.png)

Lord of SQLInjection 올클 기념으로 작성하는 SQLi Cheat Sheet  
(근데 이제 웹린이의 삽질을 곁들인..)  


## 문제별 교훈들

### 1. GREMLIN  
SQL 파서가 `--` 주석을 이해하기 위해선 반드시 뒤에 공백이 들어가야 한다.  
따라서, `-- `로 넘기는게 확실하다.


### 5. WOLFMAN  
공백(`%20`)은 `%09`, `%0a`, `%0b`, `%0c`, `%0d`, `%a0`, `/**/`으로 대체 가능하다.  
`()`를 적절히 써서 공백없이 쿼리를 짜는 것도 가능하다.  


### 8. TROLL  
```php
  if(preg_match("/admin/", $_GET[id])) exit("HeHe");
  $query = "select id from prob_troll where id='{$_GET[id]}'";
```  
문제의 의도는 대문자로 `preg_match()`를 우회하는 것이다.  
하지만, `ad\min`과 같은 문자열로도 우회가 된다.  

`preg_match()`는 `\`를 이스케이프 문자로 인식해서, 문자열이 매치되지 않고,  
MySQL에서는 `\`뒤에 특별한 이스케이프 시퀀스로 인식되는 문자가 아닌 경우 `\`를 제거한다.  

`NO_BACKSLASH_ESCAPES` 모드를 설정하면 `\`를 자동으로 제거하지 않는다.  

### 11. GOLEM  
MySQL의 `like()`는 대소문자를 구분하지 않는다.  
Blind SQLi를 수행할 때, like를 써야한다면, `substr()` 보다는 `ascii(substr())`, `binary(substr())`, `mid()`를 사용하는 습관을 갖자.  

### 25. GREEN_DRAGON  
쿼리에 문제가 없는데 결과가 안나온다면, 테이블이 비어있을 수 있다는 것을 의심하자.  
이럴때는 union을 사용해서 데이터를 넣어준다.  

### 30. OUROBOROS  
`Quine Code`라는 개념이 등장.  
자기 자신을 출력하는 코드이다.


## 자세한 writeup  
[lord of sqli writeups](https://github.com/seolaf/lord-of-sqli)