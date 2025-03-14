---
title: ["[Research] - Double and nothing: Understanding and detecting cryptocurrency giveaway scams"]
date: 2024-04-18 09:00:00 +0900
last_modified_at: 2024-04-18 09:00:00 +0900
categories: [research, crypto]
tags: [research]
---

> Li, X., Yepuri, A., & Nikiforakis, N. (2023, January). Double and nothing: Understanding and detecting cryptocurrency giveaway scams. In Proceedings of the Network and Distributed System Security Symposium (NDSS).

***
## 연구 개요
Giveaway Scam에 대한 최초의 대규모 연구  
CryptoScamTracker 시스템을 개발하여 자동으로 giveaway scam 웹사이트를 탐지하고,
- giveaway scam 사이트의 수량과 구조 분석
- 사기범이 사용한 도메인, 서버, 암호화폐 지갑 주소 분석
- 블록체인 데이터를 활용한 실제 피해 금액 분석
- 피해자가 중복 피해를 입는 패턴 확인

***
## 연구 방법론
### CryptoScamTracker 시스템 구조
![](assets/img/research/double_and_nothing.png)  

1. Domain Monitoring Module
   - Certificate Transparency Logs (CT Logs)를 이용하여 암호화폐 관련 키워드가 포함된 도메인 감지  
*CT 로그는 인증 기관이 발급한 모든 SSL/TLS 인증서를 기록하는 공개적이고 변경 불가능한 데이터베이스*
   - 감지된 도메인은 자동 크롤링 대상으로 설정  
2. Crawl and Detection Module
   - 감지된 도메인에 대해 HTML, 스크린샷, 텍스트 데이터를 수집
   - 암호화폐 관련 키워드 필터링을 통해 giveaway scam으로 의심되는 웹사이트를 추려냄
   - 다중 탐지 시스템 적용 (Selenium, 헤드리스 크롤러 등)
3. Analysis Module
   - 탐지된 사이트의 블록체인 데이터 분석 (지갑 주소, 거래 내역 등)
   - 사기 사이트의 도메인, IP, 서버 제공업체 분석
   - 피해자의 암호화폐 송금 패턴 분석

### 데이터 수집
- 수집 기간: 2022년 1월 - 2022년 7월 (6개월)
- 수집된 스캠 사이트 수: 10,079개
- 분석한 도메인 수: 3,863개
- 확보한 사기범 지갑 주소: 2,266개
- 탈취한 암호화폐 총액: $24.9M - $69.9M

### 데이터 분석
- 사기범이 선호하는 최상위 도메인: `.com`, `.org`, `.net`
- 주요 호스팅 업체: **Cloudflare**, **Reg.ru**, **DDoS-Guard**
- 75%의 스캠 도메인은 개설된 지 며칠 안에 스캠으로 활용됨.
- 스캠 사이트의 평균 수명은 26시간
- 스캠 사이트는 보통 SNS를 통해 홍보됨.
- Etherreum, Bitcoin, Cardano, Ripple이 가장 많이 타겟팅됨.

***
## 실험 결과 및 분석
### 피해 규모 분석
| 암호화폐 | 지갑 개수 | 총 탈취 금액 | 최소 피해액 (USD) | 최대 피해액 (USD) |
|---------|---------|------------|-----------------|-----------------|
| **BTC** (비트코인) | 860 | 940.07 BTC | $17.8M | $44.9M |
| **ETH** (이더리움) | 683 | 4,330.26 ETH | $4.31M | $16.6M |
| **ADA** (카르다노) | 215 | 2,141,876 ADA | $1.3M | $3.43M |
| **XRP** (리플) | 318 | 5,799,593 XRP | $1.74M | $5.08M |
- 사기범의 가장 큰 단일 지갑: 204.95 BTC 보유
- 가장 많이 사용된 지갑 주소: Ripple 지갑, 106개 사기 사이트에서 사용됨.
- 피해자의 85%는 2021-22년에 암호화폐를 처음 접한 사용자

### 스캠 사이트의 공통 특징
- 연예인 및 유명인사 사진을 도용
- 2배, 3배로 돌려준다는 메시지 반복 사용
- 가짜 트랜잭션을 표시하여 사용자 신뢰 유도
- 채팅 기능을 사용하여 피해자를 설득

### 피해자의 행동 패턴
- 일부 피해자는 같은 사기범에게 반복적으로 돈을 송금
- 사기당한 후 돈을 찾아준다는 또 다른 사기에 속음

***
## 비판적 리뷰
1. 방법론적 한계
   - CT 로그를 기반으로 암호화폐 관련 키워드가 포함된 도메인을 필터링하는 것은 다소 naive한 필터링한 방식
   - 또한, keyword를 선정한 기준이 모호함. `kf`라는 키워드가 왜 등장하는지 모르겠음.
2. 실험 결과 해석의 한계
   - 피해 규모 추정에 문제가 있음. 저자가 추산한 피해 규모 중에는 사기범의 신뢰성 확보를 위한 송금이 포함되어 있을 확률이 매우 큼.
   - 마찬가지로 피해자의 행동 패턴을 분석할 때, 사기범에게 반복적으로 돈을 송금한 사람은 피해자가 아닌 위장 송금일 가능성이 큼.