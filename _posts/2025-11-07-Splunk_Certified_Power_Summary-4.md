---
title: ["Splunk Certified Power User [SPLK-1002] - Summary(4)"]
date: 2025-11-07 09:00:00 +0900
last_modified_at: 2025-11-07 09:00:00 +0900
categories: [research, splunk]
tags: [splunk]
---

# Tags and Event Types
## Tags

### 정의
- 이벤트에 대한 빠른 리마인더
- Field-value pair에 생성
- 데이터 분석 보조

### 특징
- 하나의 필드에 여러 태그 가능
- **Case sensitive** (대소문자 구분)
- 상관관계 식별 지원

### 생성 방법
1. 이벤트 드롭다운 → Actions → Edit Tags
2. Tag 이름 입력 후 저장
3. 검색 재실행으로 tag 필드 생성

### 검색
```
tag=login account_name=Hailie
| timechart span=1d count
```

### 관리
**Settings → Tags**
- 생성된 태그 확인
- Permissions 설정
- App/AddOn의 사전 정의 태그 확인 가능

## Event Types

### 정의
- 이벤트 하이라이트 및 색상 코딩
- 지식 공유 도구
- 데이터 카테고리화

### 특징
- 색상으로 이벤트 구분
- 시간 범위 설정 불필요
- Knowledge Object
- 카테고리별 분류 가능

### 생성 및 설정
```
index=web action=purchase
```
1. Save as Event Type
2. Settings → Event Types → Edit
3. 색상 선택 (예: Green)
4. Priority 설정

### 예시
- status=200 → Green (정상)
- status=404 → Red (에러)
- action=purchase → Green (구매)

### 관리
**Settings → Event Types**
- Filter by owner
- Edit colors
- Set priority

## Tags vs Event Types

**Tags**
- Field-value에 라벨링
- 텍스트 기반 식별
- `tag=값` 검색

**Event Types**
- 이벤트 시각적 구분
- 색상 코딩
- `eventtype=값` 검색



# Macros
## Macro란?
- 검색의 **단축키/바로가기**
- 저장된 검색을 이름으로 호출
- 반복적인 긴 쿼리를 간단하게 실행
- 시간 절약 및 효율성 향상

## 생성 방법
**Settings → Advanced Search → Search Macros → Add New**

### 기본 구조
- **Name**: Macro 이름
- **Definition**: SPL 검색 내용
- **Arguments**: 파라미터 개수 (선택)

## Macro 실행
### Backtick 사용
```
`macro_name`
`macro_name(argument)`
```

## Arguments
### 사용 방법
```
# Macro definition
log_level=$input$
| table host, log_level, event_message, _time

# Arguments field
input

# Macro name
loglevel(1)
```
- `$argument_name$`로 변수 표시
- Macro 이름에 argument 개수 표시 필수 `(1)`, `(2)` 등

### 실행
```
`loglevel(error)`
`loglevel(info)`
`loglevel(*)`
```

## 실습 예시

### 1. 간단한 Macro (Arguments 없음)

**salesmade**
```
index=web action=purchase
| lookup productinfo.csv productID OUTPUT description
| where isnotnull(description)
| stats count by description
| addtotals col=t label="Total" labelfield=description fieldname=count
```

**사용**
```
`salesmade`
```

### 2. Top 5 IPs
**top5**
```
(index=web OR index=security)
| top limit=5 src
```

**사용**
```
`top5`
```

### 3. Arguments 사용

**loglevel(1)**
```
index=_internal log_level=$input$
| table host, log_level, event_message, _time
```

**사용**
```
`loglevel(error)`
`loglevel(info)`
`loglevel(warn)`
```

## Macro 확장 (Expand)

### 키보드 단축키
- **Windows**: Ctrl + Shift + E
- **Mac**: Command + Shift + E

### 기능
- Macro 내부 검색 내용 표시
- 디버깅 및 이해에 유용

## 관리

**Settings → Advanced Search → Search Macros**
- 생성된 macro 확인
- Edit/Delete
- Permissions 설정
- Filter by owner

## Best Practice
- 반복적인 긴 검색에 사용
- 수학 함수 포함 가능
- 팀 공유로 효율성 증대



# Workflows
## Workflow Actions란?

- Splunk 환경에 기능 추가
- 데이터 Push/Pull 또는 검색 수행
- 반복 작업 자동화로 시간 절약

## Workflow Action 유형

### GET
- 웹 리소스 활용
- Splunk 필드를 외부 웹사이트로 전송
- 추가 정보 조회

**예시**
- IP Whois lookup
- Windows Event Log code 조회
- 도메인 정보 조회

### POST
- 데이터 Push
- 외부 시스템에 데이터 전송

**예시**
- SNOW 티켓 생성
- 온라인 폼 입력
- 자동 티켓팅

### Search
- 이벤트 기반 2차 검색 실행
- 선택된 필드로 새 검색 시작
- Advanced 사용 사례

## Workflow Action 생성

### 설정 경로
**Settings → Fields → Workflow Actions → Add New**

### 설정 항목

**Name**
- Workflow action 이름

**Label**
- Event Action 메뉴에 표시되는 텍스트

**Apply only to fields**
- 적용할 필드 선택 (예: clientip)

**Show action in**
- Event menu
- Fields menu
- Both

**Action type**
- Link
- Search
- POST

**URI / Link URL**
```
https://whois.domaintools.com/$clientip$
```
- `$field_name$` 형식으로 변수 사용

**Open link in**
- Same window
- New window

**Link method**
- GET
- POST

## 실습 예시: IP Whois Lookup

### 1단계: URL 구조 분석
```
https://whois.domaintools.com/192.168.1.1
```
- IP 주소가 URL 경로에 포함

### 2단계: Workflow Action 생성
- Name: `IP whois lookup`
- Label: `whois:$clientip$`
- Apply to: `clientip`
- Action type: `link`
- URL: `https://whois.domaintools.com/$clientip$`
- Method: `GET`

### 3단계: 사용
1. 이벤트 드롭다운
2. clientip 필드 클릭
3. Event Actions → whois 선택
4. 새 창에서 IP 정보 표시


## Best Practice
- 자주 사용하는 외부 조회 도구 연동
- Permissions 설정으로 팀 공유
- 반복 작업 자동화

## 활용 사례
- IP/Domain reputation 조회
- Event code 해석
- 티켓 생성 자동화
- 외부 데이터베이스 연동



