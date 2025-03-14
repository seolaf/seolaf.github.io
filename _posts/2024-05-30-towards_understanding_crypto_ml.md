---
title: ["[Research] - Toward understanding asset flows in crypto money laundering through the lenses of Ethereum heists exploration"]
date: 2024-05-30 09:00:00 +0900
last_modified_at: 2024-05-30 09:00:00 +0900
categories: [research, crypto]
tags: [research]
---

> Wu, J., Lin, D., Fu, Q., Yang, S., Chen, T., Zheng, Z., & Song, B. (2023). Toward understanding asset flows in crypto money laundering through the lenses of Ethereum heists. IEEE Transactions on Information Forensics and Security, 19, 1994-2009.

***
## 연구 개요
이더리움 해킹 사건을 중심으로 암호화폐 기반 돈세탁 특징 연구

### 주요 연구 질문
- RQ 1: 돈세탁 계정의 트랜잭션 특징은 무엇인가?
- RQ 2: 돈세탁 네트워크의 구조적 특성은 어떻게 다른가?
- RQ 3: 돈세탁이 Web3 경제에 미치는 영향은 무엇인가?

***
## 연구 방법론
### 데이터셋 구축
2018년 - 2022년까지 33건의 주요 해킹 사건을 분석하여 돈세탁 패턴을 연구함.
- 160,000개 이상의 주소를 포함하는 web3 돈세탁 데이터셋 최초 구축
- 크로스 체인 트랜잭션, DEX 활용, 스테이블코인 스왑, 믹싱 서비스 사용 등의 패턴 분석
- EthereumHeist 공개 데이터셋 제공 (*링크도 없고 검색해도 안나옴..*)

### Heuristic Tracing Algorithm
암호화폐 돈세탁을 3단계(Placement → Layering → Integration)로 정의함.
1. 의심계정을 탐지하여 Layering 및 Integration 계정을 식별
2. Etherscan API 기반 거래 기록 수집
3. 특정 조건을 충족하는 계정을 Layering 그룹으로 분류

***
## 실험 결과 분석
### RQ 1. 계정의 트랜잭션 특성
- 돈세탁 계정의 수명은 매우 짧거나 극도로 김.
- 일반 계정보다 3-5배 높은 평균 거래 금액
- 단기간 내 집중적인 대량 송금 발생

*짧은 시간 내에 자금을 분산시키거나, 일정 기간 후에 돈세탁을 시도하는 두 가지 전략을 사용*

### RQ 2. 네트워크의 구조적 특징
- Self-loop 비율이 낮음. 단순 반복 거래보다는 분산형 네트워크 구조를 선호함
- 일반 이더리움 네트워크보다 4배 이상 높은 트랜잭션 밀도
- Small World 현상 미발견 (일반적인 social network와 차이점)

### RQ 3. 경제적 영향
- DEX 사용 증가
- 크로스체인 서비스 활용 증가
- ETH 가격 하락과 대량 현금화의 상관관계

### 신규 기법: Fake 토큰 활용
Akropolis Hacker 사례
- 해커가 위조 RZN 토큰을 생성
- 유니스왑에 LP 생성
- 해커들이 자신이 만든 위조 토큰을 사고팔며 돈세탁 진행

***
## 비판적 리뷰
먼저, 데이터 샘플의 편향 가능성이 커보인다. **Heist** 태그를 기반으로 데이터를 수집하고, 휴리스틱한 방법으로 추적을 진행했는데 사전 태깅된 소수 계정 중심의 데이터가 구축되었을 가능성이 큼.  
또한, 트랜잭션을 필터링하는 threshold 값의 객관적 검증이 부족함. 관련해서 FP, FN 문제가 발생할 가능성이 높음.  
AML을 연구하기 위한 groundtruth 데이터가 없어 연구에 어려움이 많았는데, 그것을 해소하기 위한 연구 정도로 보임. (하지만, 공개했다는 데이터셋은 찾을 수가 없었음...)