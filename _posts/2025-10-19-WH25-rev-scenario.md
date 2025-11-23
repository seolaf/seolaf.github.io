---
title: ["[WHITEHAT 2025 qual CTF- rev] Scenario"]
date: 2025-10-19 09:00:00 +0900
last_modified_at: 2025-10-19 09:00:00 +0900
categories: [writeup, ctf]
tags: [reversing, vm]
---
> 발견한 백도어의 출처와 행동을 분석하기 위해 백도어를 활성화하는 키를 찾아주세요. 

간단한 VM 리버싱 문제이다.  

### 바이너리 로직
1. **ptrace 기반 VM**: 자식 프로세스가 바이트코드를 실행하고, 부모가 `0xcc` (INT3) 브레이크포인트마다 개입해서 연산 수행
2. **스택 기반 아키텍처**: `local_b0`이 스택 포인터 역할
3. **메모리 영역**: `local_a8`은 별도 메모리 공간 (변수 저장용)

### 바이트코드 해석

바이트코드 형식: `0xcc [opcode] [operand]`

주요 연산:

- **0xf0 XX**: PUSH XX (값을 스택에 푸시)
- **0xf1**: ADD (스택 top 2개 더하기)
- **0xf2**: MUL (곱하기)
- **0xf3~0xf5**: AND, OR, XOR
- **0xf9**: STORE (스택 → 메모리)
- **0xfa**: LOAD (메모리 → 스택)
- **0xfb**: EXIT (결과 반환)
- **0xfc**: ROL (rotate left)

### Solve.py
```python
def reverse_bit_shuffle(value):
    result = 0
    result |= ((value >> 7) & 1) << 7  # bit 7 -> bit 7
    result |= ((value >> 6) & 1) << 5  # bit 6 -> bit 5
    result |= ((value >> 5) & 1) << 3  # bit 5 -> bit 3
    result |= ((value >> 4) & 1) << 1  # bit 4 -> bit 1
    result |= ((value >> 3) & 1) << 6  # bit 3 -> bit 6
    result |= ((value >> 2) & 1) << 4  # bit 2 -> bit 4
    result |= ((value >> 1) & 1) << 2  # bit 1 -> bit 2
    result |= ((value >> 0) & 1) << 0  # bit 0 -> bit 0
    
    return result


def rotate_right(value, rotation):
    return ((value >> rotation) | (value << (8 - rotation))) & 0xFF


def reverse_transform(expected_value, rotation):
    value = expected_value
    
    value = reverse_bit_shuffle(value)
    
    value = rotate_right(value, rotation)
    
    input_val = (value - 60)
    
    if input_val % 5 != 0:
        for candidate in range(256):
            if ((candidate * 5 + 60) & 0xFF) == value:
                return candidate
    
    return (input_val // 5) & 0xFF


def solve_flag(expected_table):
    rotations = [0, 3, 6, 1, 4, 7, 2, 5] * 5
    
    flag = []
    for i in range(len(expected_table)):
        rotation = rotations[i % 8]
        input_byte = reverse_transform(expected_table[i], rotation)
        flag.append(input_byte)
    
    return bytes(flag)


def bit_shuffle(value):
    result = 0
    result |= ((value >> 7) & 1) << 7
    result |= ((value >> 5) & 1) << 6
    result |= ((value >> 3) & 1) << 5
    result |= ((value >> 1) & 1) << 4
    result |= ((value >> 6) & 1) << 3
    result |= ((value >> 4) & 1) << 2
    result |= ((value >> 2) & 1) << 1
    result |= ((value >> 0) & 1) << 0
    return result


def rotate_left(value, rotation):
    return ((value << rotation) | (value >> (8 - rotation))) & 0xFF


def verify_flag(flag, expected_table):
    rotations = [0, 3, 6, 1, 4, 7, 2, 5] * 5
    
    result = 0
    for i in range(len(flag)):
        value = (flag[i] * 5 + 60) & 0xFF
        
        rotation = rotations[i % 8]
        value = rotate_left(value, rotation)
        
        value = bit_shuffle(value)
        
        value ^= expected_table[i]
        
        result |= value
    
    return result == 0



if __name__ == "__main__":
    expected_table = [
        0x3b, 0x45, 0xa3, 0xe0, 0x87, 0x27, 0x07, 0xe9, 
        0x0b, 0xad, 0xb2, 0x58, 0xce, 0x35, 0x07, 0xb0, 
        0x0b, 0xc5, 0x31, 0x2a, 0xd5, 0xa4, 0xc4, 0xe0, 
        0x71, 0xa1, 0x22, 0xe0, 0x1d, 0x87, 0xe8, 0x8a, 
        0x66
    ]
    
    flag = solve_flag(expected_table)
    
    print(flag.hex())
    print(flag)
```