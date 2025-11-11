---
title: ["Splunk Certified Power User [SPLK-1002] - Summary(5)"]
date: 2025-11-07 09:00:00 +0900
last_modified_at: 2025-11-09 09:00:00 +0900
categories: [research, splunk]
tags: [splunk]
---

# Data Normalization & Troubleshooting
## Field Aliases

### 목적
- 데이터 정규화
- 다른 이름의 동일한 필드를 통일
- 검색 및 협업 효율성 향상

### 특징
- 원본 필드명도 유지 (20% 이상 결과에 포함 시 표시)
- 새 alias와 함께 표시

### 생성 방법
**Settings → Fields → Field Aliases → Add New**

### 실습 예시
```
# 목표: 모든 IP 필드를 source_ip로 통일

# Web (access_combined)
src → source_ip

# Security (linux_secure)  
src → source_ip

# Cisco (cisco:wsa:squid)
ip_address → source_ip
```

### 검색
```
source_ip=*
```
- 모든 source type에서 통일된 필드명으로 검색

## Calculated Fields

### 목적
- eval 명령을 자동화
- 반복적인 수학 계산 간소화
- Macro와 유사하지만 필드 전용

### 생성 방법
**Settings → Fields → Calculated Fields → Add New**

### 예시: Bytes → Megabytes
```
# Source type: access_combined
# Field name: megs
# Eval expression: bytes/1024/1024
```

### 사용
```
index=web
| stats sum(megs) by file
| sort -megs
```

## Buckets

### 데이터 저장 구조

**Hot Bucket**
- **유일하게 쓰기 가능**
- 최신 데이터 저장
- 가장 최근 이벤트

**Warm Bucket**
- 읽기 전용
- Hot에서 롤오버

**Cold Bucket**
- 읽기 전용
- Warm에서 롤오버

**Frozen Bucket**
- 아카이브 또는 삭제
- **검색 불가**
- 데이터 보관 정책에 따라 처리

**Thawed Bucket**
- Frozen 데이터를 다시 검색 가능하게 복원


### dbinspect 명령어
```
| dbinspect index=*
| table bucketId, state, index, modTime

# 특정 상태 필터링
| where state="hot"
| where state="warm"
| where state="cold"
```

## Job Inspector

### 기능
- 검색 성능 분석
- 에러 진단 및 문제 해결
- 실행 비용 확인

### 접근 방법
- 검색 결과 페이지에서 **Job Inspector** 클릭
- 또는 검색 바 옆 아이콘 클릭

### 제공 정보

**Execution Costs**
- 검색 실행 비용

**Search Job Performance**
- 실행 시간
- 처리된 이벤트 수
- 성공/실패 상태

**Job Details Dashboard**
- Duration
- User
- Status
- Internal metrics

### 사용 예시
```
# 에러 발생 검색
index=web
| stats values(host) by _time

# Job Inspector에서 확인:
# - 에러 메시지
# - 검색 성능
# - 최적화 제안
```

## Search Assistant

**Settings → User Preferences → SPL Editor**

**Full Mode**
- 상세한 제안
- Splunk Docs 링크
- 이전 검색 기록
- 초보자 권장

**Compact Mode**
- 간단한 제안

**None**
- 제안 없음



# Data Models
## Data Model이란?

### 정의
> "계층적으로 구조화된 검색 시간 매핑, 하나 이상의 데이터셋에 대한 의미론적 지식"

- 데이터 타입을 연결하는 모델
- Parent와 Child dataset의 계층 구조
- Child = Parent dataset의 부분집합

### 목적
- **대용량 데이터 빠른 검색**
- 검색 효율성 향상
- CIM(Common Information Model) 준수
- 데이터 정규화

## Acceleration (가속화)

### 개념
- Data Model 가속화 시 tsidx 파일 생성
- 인덱스 파일에서 직접 쿼리
- 검색 속도 대폭 향상

### 설정
**Settings → Data Models → Edit → Accelerate**
- Summary Range 설정
- 번개 아이콘(⚡) 표시

## 핵심 Commands

### 1. datamodel

**Command로 사용**
```
| datamodel Web search
| datamodel Authentication search
```
- 기존 데이터 모델 및 dataset 검색
- 데이터 검증 용도

**Argument로 사용**
```
| datamodel Network_Traffic All_Traffic search 
| search sourcetype=cisco:* 
| stats count by sourcetype
```
- 특정 데이터 모델 지정

### 2. tstats

**구문**
```
| tstats <stats-function> from datamodel=<datamodel-name> 
  where <where-conditions> by <field-list>
```

**예시**
```
# 기본 카운트
| tstats count from datamodel=Web

# 조건부 검색
| tstats summariesonly count from datamodel=Intrusion_Detection.IDS_Attacks 
  where IDS_Attacks.severity=high OR IDS_Attacks.severity=critical 
  by IDS_Attacks.src, IDS_Attacks.dest, IDS_Attacks.signature, IDS_Attacks.severity
```

**특징**
- stats보다 **훨씬 빠름**
- 가속화된 데이터 모델의 tsidx 파일 쿼리
- `summariesonly=true`: 요약 데이터만 사용 (더 빠름)

### 3. pivot

**접근 방법**
- Settings → Data Models → Pivot
- GUI 기반 검색 및 시각화
- SPL 작성 불필요

**사용 사례**
- 초보자용 데이터 탐색
- 빠른 시각화 생성


## 실습 예시

### Web Data Model 검색

**tstats 사용**
```
| tstats count from datamodel=Web
```

**datamodel 명령어로 Source Type 확인**
```
| datamodel Web search 
| stats values(sourcetype)
```

### Authentication Data Model

**Root Dataset 검색**
```
| datamodel Authentication search
| stats count by sourcetype
```

**Child Dataset 검색**
```
| datamodel Authentication Successful_Authentication search 
  sourcetype=linux_secure
| table host, Authentication.user, source, action, _raw
```

### Network Traffic
```
| datamodel Network_Traffic All_Traffic search 
| search sourcetype=cisco:* 
| stats count by sourcetype
```

## Field Aliases와 Data Model

### 필요성
- 다른 source type의 필드명이 CIM과 다를 때
- Field Alias로 매핑

**예시**
```
src → Authentication.src
user → Authentication.user
dest → Authentication.dest
```

## Pivot Tool 사용

1. Settings → Data Models
2. 원하는 Data Model 선택
3. **Pivot** 클릭
4. Split By 필드 선택
5. 시각화 선택
6. 결과 확인


# CIM (Common Information Model)
## CIM이란?

### 정의
- **공통 정보 모델**: 모든 Splunker가 참조하는 표준 모델
- **Application**: 22개의 사전 구성 Data Model 제공
- Data Model 활용한 데이터 정규화

### 목적
> "The CIM is life or death"

- 다양한 데이터 구조 통일
- 공통 필드명 사용
- 검색 효율성 향상

## CIM의 중요성

### 1. Data Normalization
- 필드명 추측 불필요
- 일관된 필드명 사용
- 예: clientip, src, ip_address → 모두 `src`로 통일

### 2. Enterprise Security (ES)
- **필수 요구사항**: ES는 CIM 준수 데이터만 사용
- Data Model 기반 검색 및 운영

### 3. 검색 효율성
- 대용량 데이터 빠른 검색
- Accelerated Data Model 활용
- 테라바이트급 데이터 처리

### 4. 팀 협업
- 공통 검색 방법
- 신규 사용자 교육 용이
- 일관된 분석 환경

### 5. 감사 도구
- CIM 준수율 확인
- 규정 준수 요구사항 충족

## CIM Add-on

### 설치
**Home → Find More Apps → Splunk Common Information Model**

### 제공 내용
- 22개 사전 구성 Data Models
- Tags, Event Types
- Field Aliases 템플릿

## CIM 준수 데이터 생성 과정

### 1단계: Event Type 생성
**Settings → Event Types → New**
```
# Name: Example_web1
# Search: index=web host=web1
# Tag: web
# Color: Magenta
```

### 2단계: Splunk Add-on Builder 설치
**Home → Find More Apps → Splunk Add-on Builder**

### 3단계: Add-on 생성
1. **New Add-on**
   - Name: Example_Web2
   - Prefix: TA- (자동 추가)

2. **Manage Source Types**
   - Add → Import from Splunk
   - Source type: access_combined
   - Save

3. **Map to Data Models**
   - New Data Model Mapping
   - Source type: access_combined
   - Index: web
   - Select Data Model: **Web**

### 4단계: Field Aliases 생성

**필드 매핑**
```
좌측 (기존 필드)    →    우측 (CIM 필드)
clientip          →    src
useragent         →    http_user_agent
method            →    http_method
referer_domain    →    http_referer_domain
```

**생성 방법**
- New Knowledge Object → FIELDALIAS
- 좌측 필드 선택 → 우측 CIM 필드 매칭
- OK → 반복

### 5단계: Validate and Package
- 검증 완료
- TA 다운로드 가능
- Settings → All Configurations에서 확인

## Data Model 확인

### Pivot으로 확인
```
Settings → Data Models → Web → Pivot
```

### tstats로 확인
```
| tstats count from datamodel=Web
```

### Event Count 확인
```
| datamodel Web search
| stats count
```

## CIM 필드 구조

### Web Data Model
**Inherited (상속)**
- host, source, sourcetype, _time 등

**Extracted (추출)**
- action, bytes, src, dest 등

**Calculated (계산)**
- 평가된 필드 값

### 색상 코드
- **Green**: 100% 일치 (Field Alias 불필요)
- **기타**: 매핑 필요

## 중요 포인트

### Tags 활용
```
# Web Data Model은 tag=web 필요
tag=web
```

### Macro 사용
- Data Model은 사전 정의 매크로 사용
- `cim_Web_indexes`

### 완전한 CIM 준수
- 모든 필드를 Data Model 필드에 매핑 필요
- 시간이 많이 소요되는 작업
- 점진적 매핑 권장

## Settings → All Configs

생성된 TA 확인:
```
TA-Example_Web2
```
