---
title: ["[Research] - Txphishscope: Towards detecting and understanding transaction-based phishing on ethereum"]
date: 2024-05-02 09:00:00 +0900
last_modified_at: 2024-05-02 09:00:00 +0900
categories: [research, crypto]
tags: [research]
---

> He, B., Chen, Y., Chen, Z., Hu, X., Hu, Y., Wu, L., ... & Zhou, Y. (2023, November). Txphishscope: Towards detecting and understanding transaction-based phishing on ethereum. In Proceedings of the 2023 ACM SIGSAC Conference on Computer and Communications Security (pp. 120-134).

***
## 연구 개요
이더리움에서 발생하는 트랜잭션 기반 피싱(TxPhish)에 대한 연구  
자동 탐지 시스템 TxPhishScope을 제안하여 대규모 탐지를 수행하였으며, 26,333개의 피싱 사이트와 3,486개의 피싱 계정을 탐지하고 신고함.

### 저자가 제시한 현재 한계점
1. 피싱 사이트의 수명이 짧아 신고-차단되기 전에 사라지는 경우가 많음.
2. TxPhish 사이트에 대한 신고 자체가 적음.
3. 대부분이 사용자의 신고에 의존하며 수동으로 탐지함.

***
## TxPhishScope
![](assets/img/research/Txphishscope.png)  
1. Real-time Certificate Collector
   - CT Log를 활용하여 새로 등록된 도메인 리스트를 실시간으로 수집
   - 피싱 사이트는 수명이 짧아, 도메인 등록 직후 탐지를 시도해야 함.
2. Website Scanner
   - 수집된 도메인이 이더리움과 관련이 있는지 분석
   - 웹사이트에서 공개 API 호출(Moralis, Infura 등)을 검사하여 블록체인과 관련된 페이지인지 확인
3. Transaction Trigger
   - 웹사이트를 자동으로 탐색하면서 피싱 트랜잭션을 발생시킴
   - 실제 사용자처럼 웹사이트를 방문해 버튼(Connet, Mint, Claim)을 클릭하여 피싱 트랜잭션이 발생하는지 확인
4. Transaction Detector
   - 피싱 트랜잭션을 시뮬레이션하여 결과 분석
   - 피싱 트랜잭션은 토큰을 받지 않고 특정 EOA로 전송, 지갑 내 모든 토큰에 대한 승인 요청을 하는 특징이 있음.

***
## 실험 및 평가
### TxPhishScope의 탐지 결과
- 기간: 2022.11.25. - 2023.07.31.
- 26,333개의 TxPhish 웹사이트 탐지
- 3,496개의 피싱 계정 탐지
- 탐지된 웹사이트 중 93.1%가 최초 보고
- $1.5M의 피싱 자금 흐름 분석 및 복구 지원
- Precision 100%, Recall 75.3%

### 탐지 속도 분석
- 평균 15시간 내 탐지, 31시간 내 보고
- 기존 사용자 신고 시스템(Chainabuse)의 탐지 속도보다 2-3배 빠름
- 피싱 웹사이트 평균 수명 113시간 보다 빠른 탐지 속도를 보임

### 피싱 웹사이트 특징 분석
- 짧은 수명 (평균 113시간)
- 낮은 운영 비용 (83.3%가 무료 인증서 사용, 최소 $0.13로 운영 가능)
- 빠른 업데이트 (인기 프로젝트가 등장할 때마다 유사한 피싱 사이트가 빠르게 생성됨)

### 피싱 자금 흐름 분석
- $80.8M 피싱 자금 흐름 분석
- 54%가 CEX(Binance 16.8%, OKX 11.0%)로 이동
- 11.8%가 믹서로 이동
- 8.7%가 지갑에 보관된 상태
- 22.6%가 기타 방법(카지노 등)으로 자금 세탁

***
## 비판적 리뷰
직전의 논문과는 달리 키워드 기반 필터링이 아닌 도메인 스코어링 알고리즘을 사용해서 피싱 사이트를 분류하는 시도가 좋았음.
전반적으로 시스템 설계, 실험 설계 및 분석이 깔끔하게 구성되었음.  
개발한 시스템이 평균적으로 15시간 내 탐지, 31시간 내 리포트한다고 했는데, 이런 타임갭이 어디서 생기는지에 대한 명확한 설명이 부족함. 매뉴얼한 검토 과정이 포함되어 있는지 등의 부연 설명이 필요함.