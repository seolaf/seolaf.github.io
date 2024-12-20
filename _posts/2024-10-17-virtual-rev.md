---
title: ["[TFCCTF 2024 - rev] virtual-rev"]
date: 2024-10-17 09:00:00 +0900
categories: [writeup, ctf]
tags: [reversing, vm]
---
<a href="/assets/bin/ctf/virtual-rev/virtual-rev" download>virtual-rev Download</a>  

![](assets/img/writeup/ctf/virtual-rev_writeup/z_90rxw9yZGV_TpofQmz6TcU4OVI971MVxbwctzyIGA=.png)

올바른 luma code를 입력하여 flag값을 도출하는 것으로 보인다.


![](assets/img/writeup/ctf/virtual-rev_writeup/u-IQJaIRXf1z_IAxzW_ggW7fUfZ7WSaQGfs0RpJayyk=.png)

바이너리를 실행했을 때 registers initialised를 출력하는 부분이다.

l0 \~ l5, lax를 0으로 초기화하고, lip에 리턴주소를 넣어 프린트한다.

~~참고로 initialised라는 스펠링으로 미루어보아 출제자는 영국 출신임을 알 수 있다.~~



![](assets/img/writeup/ctf/virtual-rev_writeup/yk-9hqTRVuhFtovfqWWfah-An4hYItH0PKPCzpEv5c4=.png)

최대 30자의 input을 받아 s에 저장한다.

strtok은 스트링 토큰화에 사용되는 함수로

dest에는 s를 기준으로 첫번째 스페이스 혹은 뉴라인을 기준으로 앞의 문자열이 저장된다.

뒤이어 선언되는 변수들은 모두 동작 플래그로 사용된다.

![](assets/img/writeup/ctf/virtual-rev_writeup/ofRZ2Aabb1AL3sXTosPQ1ySd4QPVlImVTPDJ4vP8pGU=.png)

첫번째 비교함수이다. 

for문을 11번 돌며, off\_5020에 있는 문자열과 dest값을 비교한다.

만약 같다면 배열 index가 OP5020 변수에 저장이 된다.

![](assets/img/writeup/ctf/virtual-rev_writeup/LMrN0fYB-N0f2pX_1TLN5hlAwlOc3qgTn30E2e34RZM=.png)

두번째 비교함수이다.

for문을 6번 돌며, off\_5080에 있는 문자열과 dest값을 비교한다.

만약 같다면 배열 index가 OP5080변수에 저장이 된다.

![](assets/img/writeup/ctf/virtual-rev_writeup/_hXJ9V-irZCG8mxFCP4rM5_XSrA1NISX_Dxj5ZB_IUo=.png)

세번째 비교함수이다.

dest 값이 "FLG"와 일치하는지 비교하고, 같다면 OPFLG 변수에 1을 저장한다.



![](assets/img/writeup/ctf/virtual-rev_writeup/wQtexUqQ_7KPzqsw7xfswTsMLIFUEQcWAXu1v7STsGY=.png)

stringd2에는 남은 입력값에서 첫번째 ,를 기준으로 앞의 문자열이 저장된다. 

memmove는 strtok을 할때 source 문자열에 \0을 박아놔서 그걸 핸들링하기 위함으로 보인다.

![](assets/img/writeup/ctf/virtual-rev_writeup/LMhCNqHa7BBsQ0Zt4olbxbfQvV5XrlfJrGwCiG0U9yw=.png)

stringd2는 또 다른 문자열 비교함수로 input되는데,

for문을 8번 돌면서 stringd2값이 l0\~lax 값과 일치하는지 비교한다.

일치한다면 stringd2Cmp5180 변수로 배열의 index가 저장된다.



![](assets/img/writeup/ctf/virtual-rev_writeup/4vW_FJ6FVBRjJ4RvPNQiEV70Dcua4TKiOd-x2CrIGYM=.png)

stringd3는 남은 입력값에서 \n을 기준으로 앞의 문자열이 저장된다.

마찬가지로 비교함수에 넣는다.



여기까지의 로직을 정리해보자면,

입력값은 `dest` |` ` `\n`|`stringd2`|`,`|`stringd3`|`\n`  혹은 `dest`|` ` `\n`|`stringd2`|`\n` 처럼 토큰화된다.

![](assets/img/writeup/ctf/virtual-rev_writeup/01gwFgCqMbLqYd-USoXQpXvGOlKecbJ-VZZU_OESTys=.png)

토큰값을 각 flag 변수들로 검증하는 부분이다.

dest가 5080배열에 있는데, stringd3가 있거나, stringd2가 없는 경우

dest가 5020배열에 있는데, stringd2가 없거나, stringd3가 없는 경우

dest가 FLG인데, stringd2와 stringd3가 있는 경우에 에러를 발생한다.



즉 이 프로그램은 일종의 OPERATOR 역할을 하는 것으로 추정된다.

5080배열은 인자를 하나로 받는 operator,

5020배열은 인자를 두개로 받는 operator 들이 있는 것으로 보인다.

![](assets/img/writeup/ctf/virtual-rev_writeup/_pzabl5Al9OypsSAcMLsnciL50E4j61OQsEVT6wTqsA=.png)

각 operator들을 사용할 수 있는 횟수를 통제하는 부분이다.  10번 이상 못쓰나보다.

![](assets/img/writeup/ctf/virtual-rev_writeup/iAjjtkDcEqp6rKqqroFPI1DLnNo3d4j9BYnrdXLhvz4=.png)

dest값에 맞는 operator 함수들을 호출하는 부분이다.

![](assets/img/writeup/ctf/virtual-rev_writeup/f5jI7BrJAG2n-8lRva1sxzQ3roxD7SJJo9uo9T7c-qA=.png)

각 함수들을 변환한 결과이다.

![](assets/img/writeup/ctf/virtual-rev_writeup/RPR3IqutwPum_s-GbfopOL5W4ZgZ2GN-GfM2B7FFlaE=.png)

이 함수는 메모리를 0으로 초기화시키는 함수로 보이는데, 어디에도 참조되지 않는 이상한 녀석이다.



![](assets/img/writeup/ctf/virtual-rev_writeup/lJb5dmNwXkXQjU51ppSlU4Xlp9jyqXmjcopdqhZpRYQ=.png)

dest값이 FLG일 때, flagOpen함수를 실행하게 되는데,

l0 == 1337, l1 == 108, l2 == 117, l3 == 109, l4 == 97을 만족하는지 체크한다.



```asm
inc lax    # lax = 1
inc lax    # lax = 2
mov l5,lax # l5 = 2, lax = 2
mul l5,lax # l5 = 4, lax = 2
mov l4,l5  # l4 = 4, l5 = 4, lax = 2
dec l4     # l4 = 3
inc l5     # l5 = 5
shl l4, l5 # l4 = 96
inc l4     # l4 = 97 ****
mov l1, l4 # l1 = 97
add l1, l5 # l1 = 102
add l1, l5 # l1 = 107
inc l1
mov l3, l1
inc l3
dec l5
mov l2, l3
add l2, l5
add l2, l5
mov l0, l3
shl l0, l5
mov l5, l1
shl l5, lax
sub l0, l5
mov l5, l4
shr l5, lax
add l0, l5
inc l0
FLG
```

```asm
OAN lax
OAN lax
MISZ l5,lax
XKA l5,lax
MISZ l4,l5
MAZ l4
OAN l5
STF l4,l5
OAN l4
MISZ l1,l4
RALK l1,l5
RALK l1,l5
OAN l1
MISZ l3,l1
OAN l3
MAZ l5
MISZ l2,l3
RALK l2,l5
RALK l2,l5
MISZ l0,l3
STF l0,l5
MISZ l5,l1
STF l5,lax
MQZL l0,l5
MISZ l5,l4
QER l5,lax
RALK l0,l5
OAN l0
FLG
```

![](assets/img/writeup/ctf/virtual-rev_writeup/hRUFeynXepRz8H4ygLgf61wD3QnfhPSUdVX_L2QupF4=.png)
