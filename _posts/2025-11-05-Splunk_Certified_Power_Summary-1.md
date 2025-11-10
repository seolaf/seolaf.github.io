---
title: ["Splunk Certified Power User [SPLK-1002] - Summary(1)"]
date: 2025-11-05 09:00:00 +0900
last_modified_at: 2025-11-05 09:00:00 +0900
categories: [research, splunk]
tags: [splunk]
---

# Core Components & Deployments
## 3대 핵심 구성 요소

**Forwarder** → **Indexer** → **Search Head**

### Forwarder
- 로그 데이터를 indexer로 전송

### Indexer  
- 원시 데이터를 처리하고 **bucket**(시간별 데이터 디렉토리)에 저장
- 시간 기반 검색이 가장 효율적

### Search Head
- SPL 작성 및 검색 실행
- 검색 요청을 indexer로 전송

## 배포 유형

### Standalone
- All-in-one 단일 서버 (Search head + Indexer)

### Basic  
- 원격 forwarder + 중앙 Splunk 서버

### Multi-Instance
- 기능 분리: Search head / Indexer / Forwarder 각각 독립
- 대부분의 프로덕션 환경에서 사용

## 클러스터링

- **Search Head Cluster**: 검색 용량 증대, 최소 3대 필요, Deployer로 관리
- **Indexer Cluster**: 데이터 복제로 가용성 확보
- **Deployment Server**: 다수의 forwarder 관리



# Getting Data into Splunk
## Data Pipeline 4단계

1. **Input**: 데이터 수집
2. **Parsing**: 데이터 처리 (Indexer가 담당)
3. **License Tracker**: 라이선스 사용량 체크
4. **Indexing**: 압축 및 디스크 저장

## Input Phase

### Input 종류
- Forwarder로부터 전송
- 로컬 로그 파일
- TCP 포트 모니터링
- HTTP Event Collector
- 네트워크 트래픽
- 업로드 데이터

### 주요 Metadata

**Source**
- 데이터의 전체 파일 경로
- 데이터 수집 위치 및 방법

**Host**
- 데이터를 전송한 주체
- Forwarder 또는 로컬 머신

**Source Type**
- 데이터 포맷 형식 선택
- 이벤트 표시 방식 결정
- **중요**: 올바른 source type 선택 필수 (데이터 표시 방식에 영향)

## Parsing Phase

- Stream → Events 변환
- Indexer가 처리
- License 사용량 확인 (일일 데이터 수집량)



# App vs AddOn
## App

### 특징
- **GUI 제공**: 새로운 프론트엔드 경험
- **배포 위치**: Search head
- **가시성**: Apps 메뉴에서 표시
- Workstation 뷰 변경

### 예시
- AWS, Azure, Corelight
- **Premium Apps** (유료): Enterprise Security, Phantom (SOAR), UBA, ITSI

## AddOn (TA: Technology AddOn)

### 특징
- **기능성**: 백그라운드에서 실행
- **GUI 없음**: Workstation 뷰 변경 없음
- **배포 위치**: Indexer, Search head, Heavy forwarder
- 벤더별 데이터 처리

### 용도
- 데이터 온보딩
- 데이터 포맷 개선
- CIM(Common Information Model) 매핑
- API 연동

### 예시
- CrowdStrike, Juniper, Unix/Linux, Palo Alto

## 주요 포인트

- 같은 벤더가 App과 AddOn 모두 제공 가능
- Splunkbase에서 수천 개의 App/AddOn 다운로드 가능
- 직접 App/AddOn 개발 및 배포 가능
- **중요**: Splunk 지원 App/TA는 CIM에 자동 매핑



# The Basics of Searching
## Splunk 인터페이스

### 주요 메뉴
- **Apps 메뉴**: 설치된 apps/add-ons 확인
- **탭**: Search, Analytics, Dashboards, Reports, Alerts 등
- **Health Status**: 녹색 = 정상
- **Messages**: Instance 상태 확인

### Search History
- 이전 검색 기록 확인
- "Add to search"로 재사용 가능

## Time Picker
- Preset, Relative, Real-time 지원
- Date range 설정 가능
- Advanced: 시간 단위 조정

## 검색 모드

**Fast**
- 최소 필드만 반환, 디스크 접근 최소화

**Verbose**
- 모든 필드 및 값 표시, 최대 정보 제공

**Smart**
- Transaction 명령어 확인 후 Fast/Verbose 자동 선택

## Events Timeline
- 시간별 이벤트 분포 시각화
- Zoom in/out 가능
- 타임라인 드래그로 시간 범위 조정

## 필드 작업

### 필드 메뉴
- 왼쪽 패널에서 필드 및 값 확인
- 클릭으로 검색에 추가/제외 가능

### Raw Event
- 이벤트 확장으로 상세 정보 확인
- 문자열 하이라이트로 검색 추가

## Boolean 연산자

### OR
```spl
index=web OR index=security
```
- A 또는 B 매칭

### NOT
```spl
NOT action=purchase
```
- **주의**: `action!=purchase`와 다름
- NOT은 더 많은 이벤트 반환 (모든 관련 이벤트 포함)

## 수학 연산자
```spl
action=purchase          # 같음
action!=purchase         # 같지 않음
bytes>268               # 초과
bytes<268               # 미만
bytes>=268              # 이상
bytes<=268              # 이하
```

## 와일드카드
```spl
fail*
```
- 패턴 매칭 가능
- **주의**: 검색 성능에 부담 (과도한 사용 자제)

## 기타 기능

- **Job Inspector**: 검색 성능 확인
- **Export**: 결과 내보내기
- **Save As**: Report/Alert로 저장
- **Event Sampling**: 샘플 제한



# Knowledge Objects (KOs)
## Knowledge Objects란?

- 분석에 유용한 도구들
- Splunk에서 생성/구축한 대부분의 것들 = KO
- 팀워크 및 협업 향상

### 예시
- Alerts
- Tags
- Saved Searches
- Field Extractions
- Lookups
- Dashboards
- Reports

## Knowledge Manager

### 역할
- KO 생성 감독 및 유지보수
- 성능 관리
- KO 공유 및 활용 관리
- 네이밍 규칙 관리

## 네이밍 컨벤션 (Best Practice)
```
[그룹명]_[객체타입]_[설명]
```

**예시**
```
SOC_alert_50_daily_sales
SOC_analyst_alert_failed_logins
```

## Permissions (권한)

**Private**
- 본인만 확인 및 사용 가능

**App Only**
- 해당 App 내에서만 사용 가능

**All Apps**
- 모든 App에서 전역적으로 사용 가능

### 권한 관리
- Owner가 Read/Write 권한 변경 가능
- 팀 협업을 위해 적절한 권한 설정 중요

## Knowledge Objects 위치
- Settings → Knowledge 섹션
- Alerts, Event types, Tags, Field extractions, Lookups 등

## Alert 생성 실습

### 검색 예시
```spl
index=security eventtype="failed login" user=admin source="192.168.1.1"
```

### Alert 설정

**네이밍 규칙 적용**
```
SOC_Alert_ExcessiveFailedLogins
```

**Alert Type**
- Scheduled (크론 스케줄 권장)
- Real-time

**Trigger Conditions**
- Number of results
- Greater than 0 등

**Throttle**
- Alert 발생 후 재알림 대기 시간 설정
- 중복 알림 방지

**Actions**
- Log event
- Send email

### Permissions 설정
- **Owner**: 생성자
- **App**: This app / All apps
- **Read**: Everyone
- **Write**: Admin only

## Event Type 생성

### 예시
```spl
index=web action=purchase
```

**Event Type Name**
```
Purchases Made on Webstore
```

**설정 옵션**
- Tag 추가 가능
- Color 지정 가능
- Priority 설정

### Permissions
- Private → All apps로 변경
- Read: Everyone
- Write: Admin



# About the Fields
## Fields란?

- **Key-Value Pair** 형태
- Field name = **대소문자 구분** (case sensitive)
- Field value = **대소문자 구분 안 함** (case insensitive)

## 검색 기본 동작

**Operator 없을 때**
```spl
index=web sourcetype=access_combined categoryID=sports
```
- 자동으로 **AND** 연산자 적용

## Field Types

### Meta Fields
- host, source, sourcetype

### Selected Fields
- 이벤트 상단에 기본 표시
- "yes" 클릭으로 추가 가능

### Interesting Fields
- Splunk가 자동 인식한 필드

## Field Sidebar

**표기 의미**
- **A** = Alphanumeric (문자+숫자)
- **#** = Numeric (숫자)

## Fields 활용

### Field Window 기능
1. 모든 field value 표시
2. "Events with this field" 클릭 → 해당 필드 포함 이벤트 검색
3. 통계 자동 생성:
   - Top values
   - Top values over time
   - Rare values

## 중요: != vs NOT

**Does Not Equal (!=)**
```spl
categoryID!=sports
```
- categoryID 필드가 존재하고 값이 sports가 아닌 이벤트만 반환

**NOT Operator**
```spl
NOT categoryID=sports
```
- categoryID가 sports가 아닌 이벤트 + categoryID 필드가 없는 모든 이벤트 반환
- **더 많은 결과** 반환 (!=의 상위 집합)

## Best Practice

- AddOn 사용 시 필드 자동 추출 (수동 작업 감소)
- Source type 올바르게 설정