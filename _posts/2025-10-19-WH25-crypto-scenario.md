---
title: ["[WHITEHAT 2025 qual CTF- crypto] Scenario"]
date: 2025-10-19 09:00:00 +0900
last_modified_at: 2025-10-19 09:00:00 +0900
categories: [writeup, ctf]
tags: [crypto, reversing]
---
> 공격자의 서버에서 문서 암호화 도구와 암호화된 문서를 발견하였습니다. 도구를 분석하고 암호화된 문서를 해독해주세요.

## 코드 리뷰
공격자는 평문을 특수한 방식으로 인코딩 한다.  

1. 평문을 bit stream으로 변환
2. 이를 21bit씩 나눔
3. 각 블록에 대해 생성 다항식이 0x769인 BCH(31,21) 인코딩을 수행
4. 31비트 code word에 대해 MSB가 0이면 bit order reverse, MSB가 1이면 rotate left 15bits
5. 1bit를 랜덤하게 flip해서 단일 오류를 삽입
6. 최종 비트 열을 .enc 파일로 저장

## 복호화를 위한 아이디어
BCH(31,21)은 한 비트 오류까지 복구 가능한 코드이다.  

flip된 비트 위치나 치환 여부를 몰라도, codeword가 생성 다항식으로 나누어 떨어지도록 1bit만 뒤집으면 원래 데이터 복구가 가능하다.  

따라서,  
1. 암호문을 31bit씩 자르고
2. 각 블록에 대해 reverse / rotate-left-15 역치환 두 가지를 모두 시도
3. GF(2) 다항식 나눗셈을 이용해 생성다항식(0x769)으로 나머지가 0이 되도록 1bit를 수정
4. 수정된 codeword의 상위 21bit가 원본 평문 데이터

## Solve.py
```python
import sys

G = 0x769  # generator: x^10 + x^9 + x^8 + x^6 + x^5 + x^3 + 1 (degree 10)

def bits_from_bytes_msb(b: bytes):
    out = []
    for byte in b:
        for k in range(7, -1, -1):
            out.append((byte >> k) & 1)
    return out

def bytes_from_bits_msb(bits):
    n = (len(bits) // 8) * 8
    out = bytearray()
    for i in range(0, n, 8):
        v = 0
        for k in range(8):
            v = (v << 1) | bits[i + k]
        out.append(v)
    return bytes(out)

def bits_to_int_msb(bits):
    v = 0
    for b in bits:
        v = (v << 1) | b
    return v

def int_to_bits_msb(x, n):
    return [(x >> (n - 1 - i)) & 1 for i in range(n)]

def inv_perm_reverse(T):
    return T[::-1]

def inv_perm_rotate_left15(T):
    S = [0] * 31
    S[15:31] = T[0:16]
    S[0:15]  = T[16:31]
    return S

def gf2_mod(x: int, poly: int) -> int:
    deg = poly.bit_length() - 1
    xb = x.bit_length()
    while xb >= deg + 1:
        shift = xb - deg - 1
        x ^= (poly << shift)
        xb = x.bit_length()
    return x

def divisible_gf2(cw: int) -> bool:
    return gf2_mod(cw, G) == 0

def correct_single_bit(cw: int):
    if divisible_gf2(cw):
        return cw, -1
    good = None
    where = None
    for i in range(31):
        t = cw ^ (1 << (30 - i))
        if divisible_gf2(t):
            if good is not None:
                return None, None
            good, where = t, i
    return (good, where) if good is not None else (None, None)

def main():
    if len(sys.argv) != 2:
        print(f"Usage: {sys.argv[0]} important.txt.enc", file=sys.stderr)
        sys.exit(2)

    ct = open(sys.argv[1], "rb").read()
    bits = bits_from_bytes_msb(ct)

    nblocks = len(bits) // 31
    bits = bits[: nblocks * 31]

    pt_bits = []
    for bi in range(nblocks):
        T = bits[bi*31:(bi+1)*31]

        for inv in (inv_perm_reverse, inv_perm_rotate_left15):
            S = inv(T)
            cw = bits_to_int_msb(S)
            fixed, idx = correct_single_bit(cw)
            if fixed is not None:
                data = fixed >> 10
                pt_bits.extend(int_to_bits_msb(data, 21))
                break
        else:
            raise RuntimeError(f"Block {bi}: cannot correct single-bit error")

    pt = bytes_from_bits_msb(pt_bits).rstrip(b"\x00")
    sys.stdout.buffer.write(pt)

if __name__ == "__main__":
    main()
```