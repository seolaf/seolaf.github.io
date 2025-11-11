---
title: ["Splunk Certified Power User [SPLK-1002] - Summary(3)"]
date: 2025-11-07 09:00:00 +0900
last_modified_at: 2025-11-07 09:00:00 +0900
categories: [research, splunk]
tags: [splunk]
---

# Visualizing Your Data
## 시각화 유형

- Tables
- Single Values
- Gauges
- Bar Charts
- Line Charts
- Area Charts
- Pie Charts
- Maps (지리 정보 기반)

## 핵심 Commands 비교

**stats**
- 통계 집계
- 시간 축 없음

**chart**
- 통계 요약 및 시각화
- `over` 및 `by` 절 사용
- 시간 축 없음 (정적)

**timechart**
- 시간 기반 시각화
- 데이터 변화를 시간 순으로 표시

## 시각화 생성

### Quick Reports
- 필드 메뉴에서 하이퍼링크 클릭
- 자동 시각화 생성

### timechart
```
index=web
| timechart avg(bytes) by host
| timechart count by action
```
- 시간 경과에 따른 변화 표시
- Line chart 권장

### chart
```
index=web
| chart count over host by description
| chart useother=f usenull=f limit=0
```
- 정적 값 표시
- Bar chart 권장

## 포맷 옵션

### Stacking Mode
- **Off**: 각 필드가 별도 막대
- **Stacked**: 수직 누적
- **Stacked 100**: 백분율 누적

### Chart Overlay
```
| timechart count by action
```
- 여러 메트릭을 한 차트에 표시
- Purchase 등 특정 필드 오버레이

### Trellis
- 각 값을 개별 차트로 분리
- X축 공유 안 함

### Multi-Series
- **Yes**: 각 시리즈가 독립 Y축
- **No**: 하나의 Y축 공유

### Arguments
```
useother=f          # "other" 제거
usenull=f           # null 값 제거
limit=0             # 모든 값 표시 (기본값: 10)
```

## Dashboard 생성

### Single Value
```
index=web action=purchase
| stats count
```
- 단일 메트릭 강조
- 색상 조건부 포맷팅 가능

### Pie Chart
```
| stats count by action
```
- 비율 표시에 적합

### Bar Chart
```
index=web action=purchase
| lookup productinfo.csv productID OUTPUT description
| chart count over host by description useother=f limit=0
```

### Line Chart
```
index=security eventtype=failed_login
| timechart count by user limit=5 useother=f usenull=f
```
- 시간별 추세 분석

## Dashboard 편집

- 패널 드래그 앤 드롭으로 재배치
- 개별 패널 제목 설정
- Dark/Light 테마 선택
- X/Y축 커스터마이징
- Legend 위치 조정
- Data values 표시/숨김

## trendline 명령어

### 기능
- 차트에 이동평균 오버레이
- 트렌드 분석

### 함수 타입
- **SMA**: Simple Moving Average (단순 이동평균)
- **EMA**: Exponential Moving Average (지수 이동평균)
- **WMA**: Weighted Moving Average (가중 이동평균)

### 문법
```
| timechart count
| trendline sma5(count) as "Moving Average"
```
- 숫자는 **period** (기간)
- period 지정 필수

### 예시
```
index=_internal sourcetype=splunkd
| timechart count
| trendline sma5(count) as "Moving Average of Total Events"
```

## iplocation 명령어

### 기능
- IP 주소에 위치 정보 추가
- City, Country, Latitude, Longitude 생성

### 문법
```
| iplocation clientip
```

### 생성 필드
- City
- Country
- Region
- lat (Latitude)
- lon (Longitude)

### 예시
```
index=web
| iplocation clientip
| table clientip, City, Country, lat, lon
```

## geostats 명령어

### 기능
- 지리적 데이터 통계 계산
- 클러스터 맵 시각화

### 필수 Arguments
- `latfield`: 위도 필드
- `longfield`: 경도 필드

### 문법
```
| geostats latfield=lat longfield=lon count by email
| geostats latfield=lat longfield=lon count by action globallimit=10
```

### 예시
```
index=web
| iplocation clientip
| geostats latfield=lat longfield=lon count by action
| where purchase > 100
```

## addtotals 명령어

### 기능
- 열/행 합계 추가

### 문법
```
| addtotals row=f col=t label="Total" labelfield=lon fieldname=purchase
```

### Parameters
- `row=true/false`: 행 합계
- `col=true/false`: 열 합계
- `label`: 합계 레이블
- `labelfield`: 레이블 표시 위치
- `fieldname`: 합계 계산 대상 필드

### 대안
- Format 옵션에서 GUI로 설정 가능
- 모든 열에 적용됨

## 클러스터 맵 포맷팅

### 설정 옵션
- **Zoom on scroll**: 스크롤 줌 활성화/비활성화
- **Markers**: 마커 스타일
- **Tiles**: 타일 스타일
- 줌 레벨 수동 조정

### 활용 사례
```
# 판매량 상위 지역
| where purchase > 500

# 판매량 하위 지역  
| where purchase < 100
```

## Dashboard 구성 예시

1. **Trendline Panel**: 이동평균 추세
2. **High Sales Map**: 판매 많은 지역
3. **Low Sales Map**: 판매 적은 지역



# Reports and Drilldowns
## Reports

### 정의
- **Saved Search** (저장된 검색)
- 이벤트, 통계 테이블, 시각화 표시

### 생성 및 관리
**Settings → Searches, Reports and Alerts**

### 스케줄링
- Live 실행 또는 스케줄 설정
- Cron 스케줄링 지원
- 커스텀 스케줄 (예: 매주 월요일 06:00)

### 네이밍 컨벤션
```
[그룹명]_Report_[설명]
예: SOC_Report_ExecutablesSeen
```

### Knowledge Object
- 팀원과 공유 가능
- Permissions 설정 필수

## Drilldowns

### 기능
- 패널의 값 클릭 시 액션 실행
- 시각화에서 이상 패턴 발견 시 세부 조사

### 드릴다운 타입

**Link to Search**
```
Edit Drilldown → Link to search
```
- 클릭 시 새 검색 열림
- 해당 값으로 검색 자동 실행

**Link to Dashboard**
- 다른 대시보드로 이동

**Link to Report**
- 저장된 리포트로 이동

**Manage Tokens**
```
Token: userclick
Value: $click.value2$
```
- 클릭한 값을 토큰으로 전달
- 같은 대시보드 내 다른 패널에서 사용

## Tokens

### Text Input
```xml

  Log Level

```
- 사용자 텍스트 입력
- 패널에서 `$loglevel$` 사용

### Time Input
```xml

  Time Range
  
    -7d
    now
  

```
- 시간 범위 선택
- Shared Time Picker 설정

### Token 사용 예시
```
index=_internal log_level=$loglevel$
| eval Date=strftime(_time, "%m/%d/%Y")
| table log_level, reason, Date
| dedup Date
| sort Date
```

## 실습 예시

### Dashboard with Inputs
```
# Panel 1: 사용자 입력 기반 검색
index=_internal log_level=$loglevel$
| table log_level, reason, Date

# Panel 2: 드릴다운 이벤트 표시
index=* $userclick$
```

### Report 생성
```
host=mycomputer *.exe
| table Name, Path, CommandLine
| dedup Name
```
- Save as Report
- Edit Schedule: 매주 월요일 06:00
- Add to Dashboard

### Drilldown 설정
1. **Edit Drilldown** 클릭
2. **Manage tokens on this dashboard** 선택
3. Token name: `userclick`
4. Value: `$click.value2$`
5. 새 패널 추가: `index=* $userclick$`

## PDF Export

**Export → Export PDF**
- 대시보드를 PDF로 저장
- 차트 및 테이블 포함
- 스케줄 전송 가능

## Home Dashboard 설정

1. **Dashboards → Edit → Set as Home Dashboard**
2. **Settings → User preferences**
3. Default app: Home
4. Apply

**결과**: 로그인 시 자동으로 Home Dashboard 표시



# Alerts
## Alert란?

- **스케줄 또는 실시간**으로 실행되는 검색
- 특정 조건 매칭 시 분석가에게 알림
- 즉각적인 조치 또는 추가 조사 필요 시 사용

## Reports vs Alerts

**Reports**
- 스케줄 실행 가능
- 정보 제공 목적

**Alerts**
- 스케줄/실시간 실행
- 즉각적인 알림 및 조치 필요

## Trigger Actions

- **Log event**: 이벤트 기록
- **Send email**: 이메일 알림
- **Output to lookup**: Lookup 테이블에 저장
- **Custom actions**: 스크립트 실행 등

## Throttling (억제)

### 목적
- 중복 알림 방지
- 지정된 시간 동안 한 번만 알림

### 설정
- Field to group by 선택
- 억제 기간 설정 (예: 1시간)

**예시**: 1시간 동안 동일 조건으로 여러 번 매칭되어도 1회만 알림

## Severity Levels
- Info
- Low
- Medium
- High
- Critical

## Alert 생성 실습

### 예시: Wireshark 실행 감지
```
index=my_computer_logs remote_port=80 process_name="Wireshark.exe"
| stats values(process_name)
```

### 설정
**Alert Name**
```
SOC_Alert_SomeoneOpenedWireshark
```

**Description**
```
Wireshark was launched from my box
```

**Alert Type**
- Scheduled (Cron)
- Real-time

**Cron Schedule**
```
*/1 * * * *  # 매 1분마다 실행
```

**Trigger Condition**
- Number of results > 0
- Trigger: For each result

**Trigger Action**
- Add to triggered alerts
- Severity: Critical

## Alert 관리
**Settings → Searches, Reports and Alerts**
- Enable/Disable
- Edit
- Run manually
- View results

**Activity → Triggered Alerts**
- 트리거된 알림 확인
- 결과 조회
- 편집/삭제

## Permissions
- Knowledge Object이므로 공유 설정 필수
- 알림을 받아야 하는 모든 사람에게 권한 부여
