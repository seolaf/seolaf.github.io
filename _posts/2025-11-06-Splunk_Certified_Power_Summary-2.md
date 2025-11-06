---
title: ["Splunk Certified Power User [SPLK-1002] - Summary(2)"]
date: 2025-11-06 09:00:00 +0900
last_modified_at: 2025-11-06 09:00:00 +0900
categories: [research, splunk]
tags: [splunk]
---


## Splunk 구문 색상

**Orange (주황색)**
- Command modifiers
- Boolean 연산자, 키워드, as/by 절

**Blue (파란색)**
- Commands
- 실행할 작업 지정 (table, stats, sort 등)

**Green (녹색)**
- Arguments
- 함수에 적용되는 변수 (limit, span 등)

**Purple/Pink (보라/분홍)**
- Functions
- 수학 함수, 필드 계산 (sum, avg, min, max 등)

## SPL 구조 (좌→우)

### 1단계: 데이터 위치 지정
```spl
index=web OR index=security
```

### 2단계: 명령 실행
```spl
| stats sum(bytes) as Total_Bytes
```

### 3단계: 결과 포맷
```spl
| eval Total_Bytes=tostring(Total_Bytes, "commas")
```

## 기본 Commands

**table**
- 지정한 필드로 테이블 생성
```spl
| table host, source, action
```

**rename**
- 필드명 변경
```spl
| rename categoryID as Category
```

**fields**
- 특정 필드 포함/제외
```spl
| fields + host, source    # 포함
| fields - _raw            # 제외
```

**dedup**
- 중복 제거
```spl
| dedup user
```

**sort**
- 결과 정렬
```spl
| sort -bytes              # 내림차순
| sort +bytes              # 오름차순
```

## Best Practice
- 왼쪽에서 오른쪽으로 검색 구축
- 데이터 위치 먼저 지정 (성능 향상)
- Splunk Docs 참조 권장

## 명령어 실습

### table
```spl
index=web
| table clientip, action, categoryId, status
```
- 필드 순서대로 테이블 생성
- null 값 제거: `| where isnotnull(action)`

### rename
```spl
| rename action as Action, clientip as "Shoppers IP"
```
- 여러 필드는 쉼표로 구분
- 공백 포함 시 따옴표 사용

### fields vs table

**fields**
```spl
| fields clientip, action, categoryId
```
- 이벤트 형식으로 표시

**table**
```spl
| table clientip, action, categoryId
```
- 테이블 형식으로 표시

### dedup
```spl
| table clientip
| dedup clientip
```
- 중복 제거
- 대안: `| stats count by clientip`

### sort
```spl
| sort -count        # 내림차순 (-)
| sort +count        # 오름차순 (+)
```
- 또는 필드명 클릭으로 정렬 가능

## 실전 예시
```spl
index=web action=purchase
| top limit=6 categoryId
| stats count as "Total Purchases"
```

## Transforming Command란?

- 결과를 데이터 테이블로 변환
- 이벤트 값을 통계 목적의 숫자 값으로 변환
- **Smart mode**: Transforming command 포함 여부로 Fast/Verbose 자동 전환

## 3가지 Transforming Commands

### top
```spl
| top limit=10 field        # 기본값: 10개
| top limit=1 categoryId    # 가장 많은 값
| top field showperc=false  # 퍼센트 숨김
```
- 기본값: 상위 10개 값
- 가장 일반적인 필드 값 표시
- Argument로 개수 조정 가능

### rare
```spl
| rare limit=1 categoryId   # 가장 드문 값
```
- top의 반대
- 가장 드문 필드 값 표시

### stats
```spl
| stats sum(bytes)                    # 합계
| stats count by categoryId           # 그룹별 개수
| stats dc(categoryId)                # 고유 값 개수
| stats list(categoryId)              # 값 목록
| stats values(referer_domain)        # 고유 값 목록
```

### 다중 필드
```spl
| stats count by referer_domain, action
| stats sum(count) by referer_domain
```

### 복합 예시
```spl
index=security
| stats values(user) as "Login Name", count as Attempts by source_ip
| fillnull value="No Data Available"
```

**주요 함수**
- `count`: 개수
- `dc (distinct count)`: 고유 값 개수
- `sum`: 합계
- `avg`: 평균
- `list`: 목록
- `values`: 고유 값 목록

**특징**
- 가장 많이 사용되는 명령어
- 다양한 통계 함수 지원
- Splunk Docs에서 전체 함수 목록 확인 권장

## Transaction이란?

- 관련 이벤트를 필드 기준으로 그룹화
- 시간 범위에 걸친 이벤트 묶음
- 시작점과 종료점 확인 가능

## 주요 Arguments

**maxspan**
- 첫 이벤트와 마지막 이벤트 간 최대 시간
- 전체 트랜잭션 길이 설정

**maxpause**
- 각 이벤트 간 최대 시간 간격
- 기본값: 1분

**startswith / endswith**
- 키워드, 이벤트 ID로 시작/종료 지정
- 예: `startswith="login" endswith="logoff"`

## 사용 사례

- 이메일 대화 그룹화
- 웹 세션 추적 (JSESSIONID)
- 사용자 활동 분석
- SSH 로그인 시도 추적
- 브라우징 히스토리

## Transaction vs Stats

### Transaction 사용 시기
- 세밀한 분석 필요
- 이벤트 간 상관관계 확인
- 시작/종료 지점 확인
- **주의**: 리소스 사용량 높음, 신중하게 사용

### Stats 사용 시기
- 계산 및 집계
- 빠른 성능 필요
- 이벤트 그룹화 (제한 없음)
- **권장**: 가능하면 stats 우선 사용

## 실습 예시

### 웹 세션 분석
```spl
index=web
| transaction clientip maxspan=3m maxpause=3s endswith=purchase
| eval duration = tostring(duration, "duration")
| table clientip, duration, action
```

### SSH 실패 로그인
```spl
index=security
| transaction source_ip startswith="SSHD" maxspan=3m maxpause=3s
| eval duration = tostring(duration, "duration")
| table source, duration
| sort -duration
```

### HTTP 404 에러 분석
```spl
index=web status=404
| transaction JSESSIONID
```

## eval 명령어

### 기능
- 필드 평가 및 조작
- 계산된 결과 생성
- 새 필드 생성 또는 기존 필드 덮어쓰기
- **주의**: 디스크의 원본 데이터는 변경하지 않음 (재인덱싱 없음)

### 시간 변환

**Epoch → Human Readable**
```spl
| eval epoch_time=strptime(_time, "%s")
| eval human_time=strftime(epoch_time, "%m/%d/%Y %H:%M")
| eval DayOfYear=strftime(_time, "%j")
```

### case 함수
```spl
| eval status_code=case(
    status==404, "Not Found",
    status==400, "Bad Request",
    status==200, "Success"
)
| stats count by status, status_code
```

### count + eval 조합
```spl
| stats count(eval(status==404)) as "Number of Not Founds"
```

### MD5 해시
```spl
| eval hash=md5(file)
| table file, hash
```

## where 명령어

### 특징
- Boolean 연산자 사용
- True인 결과만 유지
- **첫 번째 파이프 이전에 사용 불가**

### 문법
```spl
| where status==404                    # 필드 값 비교
| where like(src, "64.%")             # 패턴 매칭
```

**따옴표 규칙**
- 쌍따옴표 (`"`): 필드 값
- 홑따옴표 (`'`): 필드 이름

### 사용 사례
- 두 필드 비교
- 조건 매칭
- fillnull과 함께 사용

## search 명령어

### 특징
- 키워드, 와일드카드 검색
- **검색 어디서나 사용 가능** (where와 차이점)

### 비교: where vs search
```spl
# where: Boolean 로직, 첫 파이프 전 불가
| where src="64.241.74.165"

# search: 키워드 검색, 어디서나 사용 가능
index=security user=inet
```

## Field Extraction 방법

### Regex
- 비구조화 데이터, 복잡한 로그
- Splunk가 자동 추출 못할 때 사용

### Delimiter
- 구조화 데이터
- 쉼표, 세미콜론, 공백 등 구분자 기반

## Field Extractor GUI 접근 (3가지)

1. **Settings → Fields → Field Extractions → Open Field Extractor**
2. **Fields 메뉴 하단 → Extract New Fields**
3. **이벤트 드롭다운 → Extract Fields** (권장)

## GUI Field Extractor 사용

### 단계
1. 필드 하이라이트
2. 필드명 입력
3. Add Extraction
4. Preview 확인
5. Validate (체크마크 확인)
6. Permissions 설정
7. Finish

## rex 명령어
```spl
| rex field=_raw "(?<email>\S+@\S+\.com)"
```

**특징**
- 기존 필드에서 새 필드 추출
- field 파라미터 필수
- 정규표현식 직접 작성

**구조**
```spl
rex field=<source_field> "(?<new_field_name>regex_pattern)"
```

## erex 명령어
```spl
| erex files examples="text/css,image/gif"
```

**특징**
- 예제 값 제공 시 자동으로 regex 생성
- examples 파라미터 사용
- 최소 2개 예제 권장

## 팁

### Regex 테스트
- regex101.com 활용 권장

### GUI에서 Regex 확인
- Show Regular Expression 클릭
- Edit Regular Expression
- View in Search (rex 명령어 형태 확인)

### Field Extraction 관리
- Settings → Fields → Field Extractions
- Filter by "Made by me"
- Edit/Delete 가능

## Lookup이란?

- 주로 **CSV 파일** 형태의 정적 데이터
- 인덱스에 없는 추가 정보 저장
- 데이터 보강(Enrichment) 용도
- **KV Store**: 동적 데이터용 (Power User 시험 범위 외)

## Lookup 생성

### 방법
**Settings → Lookups → Lookup Table Files → New Lookup**

1. CSV 파일 업로드
2. Destination App 선택 (예: Search & Reporting)
3. 파일명 지정 (예: `peopleinfo.csv`)
4. Permissions 설정

### Lookup Definition 설정
- Name 지정
- Type: File-based / KV Store
- Advanced Options:
  - Time-based lookup
  - Min/Max matches
  - Case sensitivity
  - Batch index query

## Lookup 명령어

### inputlookup
```spl
| inputlookup peopleinfo.csv where first_name="Henry"
```
- Lookup 파일 내용 조회
- WHERE로 필터링

### lookup
```spl
index=web action=purchase
| lookup productinfo.csv productID OUTPUT description
| table productID, description
| where isnotnull(description)
```
- 데이터 보강
- **INPUT**: Lookup 파일의 매칭 필드
- **OUTPUT**: 추가할 필드

### outputlookup
```spl
index=web
| table productID
| dedup productID
| outputlookup productinfo.csv
```
- 검색 결과를 Lookup 파일로 저장

## 실전 예시

### Product ID 보강
```spl
index=web action=purchase
| lookup productinfo.csv productID OUTPUT description
| stats count by productID, description
| where isnotnull(description)
| sort -count
```

**결과**: 제품 ID에 설명 추가, 판매량 분석 가능

