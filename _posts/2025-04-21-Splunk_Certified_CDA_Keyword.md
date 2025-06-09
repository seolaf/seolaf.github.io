---
title: ["Splunk Certified Cybersecurity Defense Analyst [SPLK-5001] - Keyword"]
date: 2025-04-21 09:00:00 +0900
last_modified_at: 2025-04-21 09:00:00 +0900
categories: [research, splunk]
tags: [splunk]
---

## 역할과 책임 (Roles & Responsibilities)

| 역할 | 주요 책임 |
|------|-----------|
| **Security Analyst** | 이벤트 분석, 위협 탐지 및 대응, 로그 모니터링 |
| **Security Engineer** | 보안 솔루션 **구현**, 시스템 설정, 기술적 문제 해결 |
| **Security Architect** | 보안 전략 수립, 보안 인프라 설계, 정책 정의 |
| **SOC Manager** | SOC 운영 관리, 인력/리소스 조정, 대응 전략 수립 |

## Splunk 리소스 종류 및 특성

| 리소스 | 설명 | 커뮤니티 기반 여부 |
|--------|------|--------------------|
| **Splunk Answers** | 사용자 커뮤니티 질문/답변 게시판 | ✅ 예 |
| **Splunk Lantern** | 베스트 프랙티스 및 사용 사례 제공 | ❌ 아니오 |
| **Splunk Guidebook** | 특정 주제에 대한 활용 가이드 | ❌ 아니오 |
| **Splunk Documentation** | 공식 제품 설명서 및 참조 문서 | ❌ 아니오 |

## Splunk Enterprise Security 프레임워크

| 프레임워크 | 주요 기능 | 핵심 목적 |
|------------|-----------|------------|
| **Risk Framework** | 수상한 활동에 대해 위험 점수 부여 | 위협 프로파일 상승, 이상 행위 탐지 |
| **Threat Intelligence Framework** | 외부 위협 인텔리전스 통합 | 알려진 악성 IP/도메인 등 탐지 |
| **Notable Event Framework** | 중요한 이벤트 생성 및 워크플로우 연결 | 경고 및 사건 조사 |
| **Asset and Identity Framework** | IP/사용자 계정을 실제 자산/사람과 매핑 | 분석 정확도 향상 |

## SPL 통계 관련 명령어

| 명령어 | 설명 | 주요 사용 예시 |
|--------|------|----------------|
| **top** | 가장 빈번한 값 출력 | `| top src_ip` |
| **rare** | 가장 드문 값 출력 | `| rare user` |
| **stats** | 통계 계산 (avg, count 등) | `| stats count by user` |
| **eventstats** | 이벤트에 통계 필드 추가 | `| eventstats avg(duration) as avg_dur` |
| ❌ least/uncommon/base | 유효한 SPL 명령어 아님 | - |

## Cyber Kill Chain® 단계 요약

| 단계 | 설명 |
|------|------|
| **1. Reconnaissance** | 타깃 정보 수집 |
| **2. Weaponization** | 악성 페이로드 제작 |
| **3. Delivery** | 악성코드 전달 (예: 이메일, USB 등) |
| **4. Exploitation** | 취약점 이용해 실행 권한 확보 |
| **5. Installation** | 악성코드 설치 및 지속성 확보 (ex. 레지스트리 수정) |
| **6. Command & Control (C2)** | 원격 제어 채널 구축 |
| **7. Actions on Objectives** | 데이터 탈취, 파괴 등 목표 수행 |

## 보안 대응 시간 지표 (MTTx Metrics)

| 지표 | 설명 | 예시 상황 |
|------|------|------------|
| **MTTD** (Mean Time to Detect) | 위협 발생 → 탐지까지 걸린 시간 | 공격이 시작된 시점부터 탐지까지 |
| **MTTA** (Mean Time to Acknowledge) | 탐지 → 경보 인지까지 걸린 시간 | 분석가가 경보를 확인한 시점까지 |
| **MTTR** (Mean Time to Respond) | 경보 생성 → 대응 완료까지 걸린 시간 | 조사 후 false positive로 종료 |
| **MTBF** (Mean Time Between Failures) | 두 장애 간 평균 시간 (보안보다는 인프라 측정용) | 시스템 장애 주기 측정 |

## SPL 명령어 - 필드 관련

| 명령어 | 역할 | 필드 생성 여부 | 사용 예시 |
|--------|------|----------------|------------|
| **rex** | 정규표현식으로 원본 데이터에서 필드 추출 | ✅ | `rex "src=(?<source_ip>\d+\.\d+\.\d+\.\d+)"` |
| **fields** | 특정 필드를 포함하거나 제외 | ❌ | `fields user, src_ip` |
| **regex** | 정규표현식 기반 필터링 | ❌ | `regex user="^admin"` |
| **eval** | 계산된 필드 생성 (연산, 문자열 처리 등) | ✅ | `eval is_admin = if(user=="admin", 1, 0)` |

## Cyber Threat Intelligence (CTI) 유형

| 유형 | 주요 내용 | 대상 독자 | 예시 |
|------|------------|-------------|------|
| **Strategic** | 위협 그룹의 목표, 의도, 장기 동향 | 경영진, 전략팀 | APT 동향 보고서 |
| **Operational** | 특정 공격의 실행 정보 (캠페인, 타깃 등) | 보안 운영팀 | 특정 공격 예고 정보 |
| **Tactical** | 공격자의 TTPs (전술/기술/절차) | 분석가, 방어자 | MITRE ATT&CK 기반 분석 |
| **Technical** (일부 자료에서 추가됨) | IP, 해시, 도메인 등 IOC | SIEM 연동용 | C2 서버 목록 |

## RBA (Risk-Based Alerting) 핵심 구성요소

| 용어 | 설명 | 예시 |
|------|------|------|
| **Risk Object** | 위험 점수가 부여되는 대상 | 사용자 계정, 호스트, IP 주소 등 |
| **Risk Event** (또는 Observation) | 위험 행위 탐지 시 생성되는 이벤트 | 관리자 권한 획득 등 |
| **Risk Score** | Risk Object에 누적된 위험 점수 | 100점 이상일 경우 경보 발생 가능 |
| **Risk Factor** | 위험 점수에 영향을 주는 조건 요소 | 예: "관리자 권한 획득"은 50점 |
| **Risk Index** | 위험 이벤트를 저장하는 Splunk 인덱스 | 보통 `risk` 인덱스를 사용 |

## SPL 반복 및 필드 처리 명령어

| 명령어 | 역할 | 특징 |
|--------|------|------|
| **foreach** | 여러 필드에 반복적으로 명령 적용 | 와일드카드 사용 가능 (`*_bytes`) |
| **rex** | 정규식으로 필드 추출 | 단일 필드 대상 |
| **makeresults** | 테스트용 이벤트 생성 | 검색 파이프라인 시작용 |
| **transaction** | 다수 이벤트를 하나로 그룹화 | 세션 추적 등 |

## Splunk ES - Notable Event 구성 요소

| 구성 요소 | 설명 |
|-----------|------|
| **Correlation Search** | 여러 데이터 소스를 분석하여 이상 징후 탐지 |
| **Adaptive Response Action** | 탐지 후 자동 실행되는 대응 조치 |
| **Notable Event** | 중요 이벤트로 등록되어 분석가가 조사 가능 |
| **Incident Review Dashboard** | 생성된 Notable Event를 조사하는 화면 |

## Splunk ES - 보안 프레임워크 매핑

| 기능 | 설명 | 예시 |
|------|------|------|
| **Framework Mapping** | 상관 검색을 NIST, MITRE, CIS 등 보안 기준에 매핑 | `NIST 800-171 - 3.1.12` |
| **Use Case Library** | 다양한 탐지 Use Case와 보안 프레임워크 연계 | 공격 유형별 분류 가능 |
| **Compliance Dashboard** | 보안 프레임워크 기반 시각화 및 추적 | NIST/CIS/ISO 기준 점검 |

## Splunk Enterprise Security - 주요 데이터 모델

| 데이터 모델 | 주요 내용 | 예시 필드 |
|--------------|-----------|------------|
| **Endpoint** | 파일, 프로세스, 접근권한, 사용자 활동 등 엔드포인트 수준 정보 | `file_path`, `file_acl`, `process_name` |
| **Malware** | 악성코드 탐지 및 분석 관련 정보 | `signature`, `malware_hash` |
| **Vulnerabilities** | CVE, 보안 취약점 스캔 결과 | `cve`, `severity` |
| **Alerts** | 탐지 경보와 관련된 메타 정보 | `alert_name`, `alert_severity` |

## Threat Hunting 분석 기법 정리

| 기법 | 설명 | 예시 |
|------|------|------|
| **Least Frequency of Occurrence Analysis** | 드물게 발생하는 이벤트나 행위를 집중 분석 | 로그인 1~2회만 한 사용자 조사 |
| **Co-Occurrence Analysis** | 여러 이벤트가 동시에 발생했는지 분석 | 로그인 + 파일 다운로드 동시 발생 |
| **Time Series Analysis** | 시간 흐름에 따른 패턴 분석 | 매주 로그인 패턴 변화 추적 |
| **Outlier Frequency Analysis** | 평균 대비 이상치 빈도 탐지 | 평균보다 훨씬 많은 로그인 시도 |

## Threat Hunting 접근 방식

| 접근 방식 | 출발점 | 설명 |
|-----------|---------|------|
| **Hypothesis-driven** | 가설 | "이런 공격이 있을 수 있다"는 전제 하에 조사 |
| **Data-driven** | 기존 데이터 | 이상 징후, 통계적 패턴 등 데이터를 먼저 보고 분석 시작 |

## Typosquatting 대응을 위한 Splunk ES 대시보드

| 대시보드 | 설명 | 관련도 |
|----------|------|--------|
| **New Domain Analysis** | 최근 처음 등장한 외부 도메인을 식별 및 분석 | ✅ Typosquatting 탐지에 최적 |
| **Access Anomalies** | 사용자 접근 패턴의 이상 탐지 | ⚠️ 일부 관련 가능 |
| **IAM Activity** | 인증, 계정 변경 등의 활동 모니터링 | ❌ 직접적 관련 없음 |
| **Malware Center** | 악성코드 탐지 및 분석 | ❌ 도메인 위협보다는 파일 위협 중심 |

## SPL 테스트용 명령어 - makeresults

| 명령어 | 기능 | 예시 |
|--------|------|------|
| **makeresults** | 데이터 없이 검색 파이프라인 시작용 이벤트 생성 | `| makeresults` |
| **eval** | 필드 생성 또는 계산 | `| eval count=1` |
| **table** | 결과 필드를 테이블 형식으로 정리 | `| table user, access` |
| **사용 예시** | `| makeresults | eval status="success"` → 테스트용 필드 생성 |

## TTP (Tactics, Techniques, Procedures) 구조 - MITRE ATT&CK 기준

| 분류 | 설명 | 예시 |
|------|------|------|
| **Tactic** | 공격자의 목적 또는 목표 (Why) | Initial Access, Execution |
| **Technique** | 목적을 이루기 위한 방법 (How) | Spearphishing Attachment |
| **Procedure** | 기술을 실행하기 위한 구체적 단계 | Cobalt Strike + 이메일 캠페인 + C2 연결 |
| **Policy** | 보안 규정, 내부 지침 | 비밀번호 정책, 접근 제어 정책 등 |

## Splunk ES & SOAR 관련 보안 콘텐츠 앱

| 앱 | 설명 | 주요 용도 |
|-----|------|----------|
| **ESCU (Enterprise Security Content Update)** | 정기적 보안 탐지 콘텐츠 업데이트 제공 | 최신 탐지 로직, 상관 검색, 대응 액션 포함 |
| **SSE** | 이전 보안 앱, 현재는 덜 사용됨 | 과거용 |
| **Threat Hunting** | 헌팅용 검색 및 대시보드 제공 | 수동 위협 탐지 지원 |
| **InfoSec** | 간단한 보안 콘텐츠 제공 (일반용 Splunk에 적합) | 소규모 환경용 |

## Splunk 검색 함수

| 함수 | 설명 |
|------|------|
| **TERM()** | 괄호 안의 값을 Splunk에서 하나의 토큰으로 검색 |
| **CASE()** | `eval` 안에서 조건문 작성 시 사용 (`case(condition, result, ...)`) |
| **LIKE()** | `where`에서 문자열 패턴 비교 (`like(user, "%admin%")`) |
| **FORMAT()** | 서브서치 결과를 메인 검색 형식으로 포맷팅 |

## Splunk ES - Asset and Identity 기반 필드

| 필드명 | 설명 | 자동 생성 조건 |
|--------|------|----------------|
| **src_category** | 소스 자산의 분류 (ex. internal, external, printer 등) | 자산 정의 및 매핑 활성화 시 |
| **dest_category** | 목적지 자산의 분류 | 자산 정의 시 |
| **src_priority** | 자산의 중요도 (ex. critical, high) | 자산 정보에 포함되어야 함 |
| **user_category** | 사용자 ID의 분류 | Identity 설정 시 |

## Splunk Security Content (ESCU/SSE) 구성 요소

| 구성 요소 | 설명 |
|------------|------|
| **Correlation Searches** | 탐지 규칙, Notable Event 생성 |
| **Dashboards** | 탐지 결과 시각화, 헌팅 보조 |
| **Reports** | 정기 실행 및 통계 제공용 콘텐츠 |
| **Lookups & Macros** | 탐지 로직 보조 도구 |
| ❌ Validated Architectures | 설치/구축 참조용 자료, 콘텐츠 라이브러리에 포함되지 않음 |

## Continuous Monitoring 단계 요약

| 단계 | 주요 활동 |
|------|-----------|
| **Establish and Architect** | 모니터링 목표 정의, 데이터 요구 사항 설계 |
| **Implement and Collect** | ✅ 데이터 수집, 탐지 생성, 드릴다운 및 대시보드 구성 |
| **Analyze and Report** | 데이터 분석, 보고서 작성, KPI 측정 |
| **Respond and Review** | 대응 조치 수행, 탐지 효과 평가 및 튜닝 |

## Splunk ES 주요 데이터 모델 비교

| 데이터 모델 | 주요 정보 | 사용 예시 |
|-------------|-----------|-----------|
| **Endpoint** | 호스트 내부 활동: 프로세스, 파일, 네트워크 생성자 등 | 어떤 프로세스가 연결을 만들었는가? |
| **Authentication** | 사용자 인증, 로그인/로그아웃 기록 | 계정 도용, 비정상 로그인 탐지 |
| **Network Traffic** | IP 간 통신, 프로토콜, 포트 정보 등 | 어떤 IP와 연결되었는가? |
| **Web** | 웹 요청, URL, 도메인 기반 활동 | 웹 필터링, URL 분석 |

## rex 명령어의 sed 모드

| 구성 요소 | 설명 | 예시 |
|-----------|------|------|
| **mode=sed** | 정규식 기반 문자열 치환 | `rex field=cc mode=sed "s/\d{12}/XXXX-XXXX-XXXX/"` |
| **rex (기본)** | 정규표현식으로 필드 추출 | `rex field=_raw "user=(?<username>\w+)"` |
| **사용 목적** | 민감 정보 마스킹, 값 치환 등 |

## Splunk CIM - Authentication Data Model 주요 필드

| 필드명 | 설명 | 예시 |
|--------|------|------|
| **src_user** | 인증 또는 권한 상승을 시작한 사용자 | `bob` |
| **dest_user** | 대상 계정 (예: 상승 후 전환된 계정) | `root` |
| **action** | 성공/실패 여부 (success / failure) | success |
| **app** | 인증이 발생한 애플리케이션 | sshd |

## Splunk ES 주요 프레임워크 비교

| 프레임워크 | 주요 기능 | 제공 정보 |
|------------|-----------|------------|
| **Asset and Identity** | IP, 사용자 등 필드에 컨텍스트 자동 추가 | 자산 이름, 사용자 역할, 중요도 등 |
| **Threat Intelligence** | 외부 인텔리전스 연동 | 악성 IP, 도메인, 해시 식별 |
| **Adaptive Response** | 탐지 후 자동화된 조치 실행 | 격리, 알림, 티켓 생성 등 |
| **Risk Framework** | 위험 기반 탐지 및 점수 부여 | `risk_score`, `risk_object`, 누적 위험 추적 |

## Splunk ES - Correlation Search 관련 기능

| 기능 | 설명 | 예시 |
|------|------|------|
| **Annotations** | 보안 프레임워크 (MITRE, CIS 등) 매핑 | tactic: Lateral Movement, technique: T1021 |
| **Playbooks** | SOAR에서 자동화된 대응 워크플로우 | 사용자 계정 비활성화 |
| **Comments** | 수동 조사 시 기록하는 메모 | 조사자 의견 작성 |
| **Enrichments** | IP, 사용자, 해시 등에 대한 외부 정보 추가 | Threat Intelligence 연동 결과 |

## Continuous Monitoring 사이클 단계

| 단계 | 주요 활동 |
|------|-----------|
| **Define and Predict** | 목표 정의, 요구사항 설정, 예측 기준 마련 |
| **Establish and Architect** | 시스템 및 데이터 흐름 설계 |
| **Implement and Collect** | 데이터 수집 도구 구현, 수집 시작 |
| **Analyze and Report** | ✔️ 데이터 분석, 성과 측정, 개선 제안 도출 |

## Splunk 도구 비교 - 데이터 소스 및 탐지 분석 목적

| 도구 | 주요 목적 | 적합도 |
|------|-----------|--------|
| **Security Essentials** | ✔️ 보안 탐지 가이드 + 데이터 소스 기반 활용 분석 | ✅ |
| **SOAR** | 자동 대응 및 워크플로우 실행 | ❌ |
| **ITSI** | IT 인프라 모니터링 및 서비스 보장 | ❌ |
| **Splunk Intelligence Management** | 위협 인텔리전스 수집/분석 | ❌ |

## Splunk ES - Risk Object 관련 시각화 기능

| 기능 | 설명 | 사용 위치 |
|------|------|------------|
| **Risk Event Count 클릭** | 특정 Risk Object에 대한 시간순 위험 이벤트 표시 | ✅ Incident Review 내 |
| **Risk Event Timeline** | 위험 이벤트의 연대기 시각화 | 클릭 시 팝업 |
| **Risk Analysis Dashboard** | 전체 리스크 이벤트 및 상관관계 분석 | Security Intelligence 탭 |
| **Risk Investigation Dashboard** | 리스크 기반 탐지 전용 분석 화면 | Workflow 액션으로 접근 가능 |

## 분석 효율 향상을 위한 Splunk 요청 예시

| 요청 유형 | 목적 | 전제 조건 |
|-----------|------|------------|
| **Field Extraction** | 로그 내 유용한 값을 필드로 추출해 검색/분석에 활용 | ✅ 분석 효율화의 기본 |
| Risk Message 추가 | Risk Notable에 설명 정보 강화 | 필드가 먼저 있어야 가능 |
| 탐지 추가 | 새로운 이상행위 발견 시 | 기존 탐지로 부족할 때 |
| Allowlist | 오탐을 줄이기 위해 예외 처리 | 비악성 판단이 명확할 때 |

## David Bianco's Pyramid of Pain 요약

| 인디케이터 유형 | 예시 | 공격자 회피 난이도 | 고통 수준 |
|------------------|------|--------------------|------------|
| **Hash Values** | MD5, SHA256 등 | 매우 쉬움 | ❌ 낮음 |
| IP Addresses     | 고정 IP 차단 | 쉬움 | ❌ 낮음 |
| Domain Names     | C2 도메인 차단 | 중간 | ⚠️ 보통 |
| Network/Host Artifacts | 파일 경로, 프로세스 이름 | 어려움 | ✅ 높음 |
| Tools            | 공격 툴 자체 식별 | 더 어려움 | ✅ 높음 |
| **TTPs**         | MITRE ATT&CK 기술 | 매우 어려움 | ✅ 최고 |
