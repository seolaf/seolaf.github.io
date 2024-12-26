---
title: ["[CSP Bypass] - PHP Output Buffer"]
date: 2024-12-26 09:00:00 +0900
categories: [cheatsheet, CSP]
tags: [web, csp bypass, PHP buffer overflow]
---

# PHP Output Buffer를 이용한 CSP 우회 취약점 분석

## Output Buffer의 이해
PHP의 Output Buffer는 실제 HTTP 응답을 생성하는 과정에서 중요한 역할을 한다. 웹 서버가 클라이언트에 데이터를 전송할 때, 직접 전송하지 않고 먼저 버퍼에 저장한다.  
기본적으로 PHP의 출력 버퍼는 4KB의 크기를 가지며, 다음과 같은 경우에 버퍼의 내용이 클라이언트로 전송된다.  
1. 버퍼가 가득 찼을 때(4KB 초과)
2. 스크립트 실행이 종료될 때
3. flush() 또는 ob_flush() 함수가 명시적으로 호출될 때

## HTTP 응답 구조와 헤더의 중요성
HTTP 응답은 헤더와 본문으로 구성되며, 프로토콜의 특성상 헤더가 반드시 본문보다 먼저 전송되어야 한다.  
CSP는 HTTP 헤더 중 하나로 설정되며, 이는 브라우저에게 어떤 리소스가 허용되고 차단되어야 하는지를 알려주는 중요한 보안정책이다.  

## 취약점의 발생 원리
해당 취약점은 두 가지 특성이 결합되어 발생한다.
1. HTTP 헤더는 어떠한 출력보다 먼저 전송되어야 함
2. Output Buffer가 가득차면 자동으로 출력이 발생함

```php
echo "some content";  // 출력 발생
header("Content-Security-Policy: default-src 'self'");  // CSP 설정
```
위와 같은 코드는 이미 출력이 시작된 후에 헤더를 설정하려 시도하므로 "Headers already sent" 경고가 발생하고 CSP 설정은 실패한다.  

## 공격 시나리오
공격자는 이러한 특성을 이용하여 다음과 같은 단계로 CSP를 우회할 수 있다.  

1. 출력 버퍼를 초과하는 데이터 전송
   * 예: 'script' 문자열이 포함된 매우 긴 입력값 전송
   * PHP의 에러 메시지나 경고를 이용한 버퍼 채우기

2. 버퍼 오버플로우 발생
   * 4KB를 초과하는 데이터가 생성되면 PHP는 자동으로 버퍼를 비움
   * 이 시점에서 아직 CSP 헤더가 설정되지 않은 상태

3. CSP 없는 응답 전송
   * 버퍼의 내용이 먼저 클라이언트로 전송됨
   * 이후 설정되는 CSP 헤더는 무시됨

4. XSS 공격 실행
   * CSP의 보호 없이 자바스크립트 실행 가능
   * 공격자의 악성 스크립트가 실행될 수 있음

## 방어 방법
1. 헤더 우선 설정
   ```php
   // 올바른 순서
    header("Content-Security-Policy: ...");
    // 이후 출력 시작
    echo "content";
    ```

2. 출력 버퍼링 활용
   ```php
    ob_start();
    // 출력 발생
    echo "content";
    // 헤더 설정
    header("Content-Security-Policy: ...");
    // 버퍼 플러시
    ob_end_flush();   
    ```

3. PHP 설정 강화
   ```ini
    ; php.ini
    output_buffering = 4096    ; 적절한 버퍼 크기 설정
    implicit_flush = Off       ; 자동 플러시 비활성화   
    ```