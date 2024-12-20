---
title: ["[CSAW 2024] archeology"]
date: 2024-10-08 09:00:00 +0900
categories: [writeup, ctf]
tags: [reversing, vm]
---

[archeology File Download](https://github.com/seolaf/seolaf.github.io/blob/main/assets/bin/ctf/archeology)


![](assets/img/writeup/ctf/archeology_writeup/SyyoNhR6FhDdesCMfBCXRQrMVPYHBq_rKrmAKAvMyO4=.png)

인자를 주었을 때, 파일의 텍스트를 암호화해주는 프로그램으로 추정된다.

코드를 자세히 살펴보자.


![](assets/img/writeup/ctf/archeology_writeup/Wuq9SwyPzzNqLvR2ZsWlNkhfqk1N4v07BBE91Cyn0-c=.png)

먼저 washing\_machine이라는 함수에 input이 들어간다.

![](assets/img/writeup/ctf/archeology_writeup/7Tat2t7uR6cMS4V9Ma7mPGKYMzwwqImksARdoDThbDE=.png)

첫번째 for문은 a\[i] ^= a\[i-1] 형식의 xor chain을 만드는 부분이고,

두번째 for문은 가운데를 중심으로 바이트를 대칭시키는 부분인데, 문자열 순서를 뒤집는다고 이해해도 무방하다.

![](assets/img/writeup/ctf/archeology_writeup/1N3G4yqcD7o-uJkAs7VAn4yAkfEuUq8hKRjUSCkl70g=.png)

이후에 main함수에 2중 for문으로 input 문자열을 확장시킨다.

앞의 3바이트는 0, 1, input\[i]이 저장되고,

140바이트를 특정 규칙에 따라 저장하고,

마지막 3바이트는 4, 1, i를 저장한다.

이렇게 inputLen만큼 반복해서 v20배열을 채워넣는다.

이렇게 확장한 v20배열은 runnnn함수로 간다.

배열을 한글자씩 읽어서 case문으로 처리를 하는 방식인데,

case를 하나하나 해석하다보니 동작하는 방식이 어셈블리와 유사했다.

![](assets/img/writeup/ctf/archeology_writeup/4tXKbAjo1nGmnCIY6vOhE7gUJgtZ0lROIUVL-vekSLA=.png)

![](assets/img/writeup/ctf/archeology_writeup/7MGEHe5ZDieDq7rpgu7sbCh0NBzskTaAqItSDuuu1l8=.png)

0 : 레지스터에 특정값을 저장한다. 1 : 두 레지스터값을 XOR한다. 2 : 레지스터값을 left rotate한다. 3 : 레지스터에 sbox에 넣은 특정값을 저장한다. 4 : 메모리에 레지스터값을 저장한다. 5 : 레지스터에 메모리값을 저장한다. 6 : 레지스터값을 출력한다. 7 : 프로그램 동작을 중지한다. 8 : 레지스터값을 right rotate한다.

즉, 0 1 input\[1] 0 0 AA 8 1 3 3 1 1 1 0 2 1 3 이라고 한다면,

reg\[1]에 input\[1]을 저장하고,

reg\[0]에 AA를 저장하고,

reg\[1]을 3만큼 shr하고,

reg\[1]에 sbox\[reg\[1]]을 저장하고,

reg\[1]에 reg\[1]^reg\[0]을 저장하고,

reg\[1]을 3만큼 shl한다.

![](assets/img/writeup/ctf/archeology_writeup/v1iIouqyHfQZMtEe4CncRBf3mffBk8RJLLUOQ3HAwPg=.png)

이후에 메모리에 저장된 값을 washing\_machine함수에 다시 넣고, hieroglyphs.txt를 읽어 stream에 저장한다.

for문으로 hieroglyphs.txt를 256줄까지 읽으며, 각 줄은 255글자까지 읽어 v16에 저장한다. 문자열에 newline이 있다면 이를 null로 대체한다. 다음 for문으로 memory의 문자를 인덱스로 v16에 저장된 글자를 프린트한다. 일종의 치환암호 방식으로 생각된다.

이 모든 과정을 역순으로 수행하면 평문을 구할 수 있을 것으로 보인다.

```python
def wmInv(input):
    inputLen = len(input)
    input = input[::-1]

    for i in range(inputLen -1, 0, -1):
        input[i] ^= input[i-1]

    return input

def runnnnInv(input):
    xor = [0xaa, 0xbb, 0xcc, 0xdd, 0xee]
    sbox = [
    72,  92, 188, 151, 129, 145,  96, 173, 148, 203, 
    146,  57,  26,  15,  48,  45,  69, 222,  20, 162, 
    8,  87, 182, 174, 118, 142, 135,  21,  12, 231, 
    98, 200,  88,  41, 109, 201, 167, 190,   4,  73, 
    5, 250, 117, 159, 253, 149, 187,  91, 121, 191, 
    218, 235,  33, 155, 165, 130,  58,  62, 185, 153, 
    240, 245, 107,   6, 252, 175, 242, 176, 120, 134, 
    207, 212, 131,  89,   0,  74, 181, 254, 171,  61, 
    199, 140, 227, 195, 229,   3,  90,  29, 157,  31, 
    10,  86, 192, 186,  67,  37, 119,  36, 124, 166, 
    223, 241,  75,  68, 255,  76, 170, 193, 105, 249, 
    56, 136, 154, 164, 230,  16, 220, 234, 104, 141, 
    95,  99, 189, 139, 243, 126, 219, 115,  93, 101, 
    103, 161, 114, 216, 177,  27, 158, 132,  22,  50, 
    225, 244, 239, 147, 172, 116,  54, 143, 204,  97, 
    13,  53,  18, 221,  78, 196, 100,  63,   9, 112, 
    42, 251, 197, 133,  59,  28,  80,  25, 213, 233, 
    71,  11, 226, 202, 198, 247, 178, 214, 248,  17, 
    84, 110, 144, 194, 236, 150,  81, 215, 232,  49, 
    128, 125,  24,  52, 183,   2, 160, 122, 179, 208, 
    70, 102,  55,  30, 123,  66, 108,  23, 217,  51, 
    43,  34, 206, 169, 127, 180,   7, 106,  65,  64, 
    38,  47, 168, 205, 113, 184,  83,  19,  94, 246, 
    224,  82,  79, 111, 228, 137,  60, 156, 163, 138, 
    77,  40,  14, 211, 210, 152, 238,  44,  46, 237, 
    39,  32,   1,  35,  85, 209
    ]
    sboxInv = [0] * 256

    for i, value in enumerate(sbox):
        sboxInv[value] = i

    for i in range(0, len(input)):
        for j in range(9, -1, -1):
            input[i] = ((input[i] >> 3) | (input[i] << 5)) & 0xff
            input[i] ^= xor[j%5]
            input[i] = sboxInv[input[i]]
            input[i] = ((input[i] << 3) | (input[i] >> 5)) & 0xff
    
    return input

def read_hieroglyphs(filename="C:\\Users\\junus\\Music\\hieroglyphs.txt"):
    hieroglyphs = []
    with open(filename, "r", encoding="utf-8") as f:
        for line in f:
            hieroglyphs.append(line.strip())
    return {glyph: index for index, glyph in enumerate(hieroglyphs)}

def memoryInv(input, glyphs):
    memory = []
    current_glyph = ""
    for char in input:
        current_glyph += char
        if current_glyph in glyphs:
            memory.append(glyphs[current_glyph])
            current_glyph = ""
    return memory

with open("C:\\Users\\junus\\Music\\message.txt", "r", encoding="utf-8") as f:
    cipher = f.read().strip()

memory = memoryInv(cipher, read_hieroglyphs())
ouputrunnnn = wmInv(memory)
inputwm = runnnnInv(ouputrunnnn)
plain = wmInv(inputwm)
print(bytes(plain))
```

![](assets/img/writeup/ctf/archeology_writeup/cpuewf11OHO7CeZMFD5i758xEj0LVSSmjVUTK32gOdM=.png)

