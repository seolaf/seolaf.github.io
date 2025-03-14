---
title: ["[Research] - Watch your back: Identifying cybercrime financial relationships in Bitcoin through back-and-forth exploration"]
date: 2024-05-16 09:00:00 +0900
last_modified_at: 2024-05-16 09:00:00 +0900
categories: [research, crypto]
tags: [research]
---

> Gomez, G., Moreno-Sanchez, P., & Caballero, J. (2022, November). Watch your back: Identifying cybercrime financial relationships in Bitcoin through back-and-forth exploration. In Proceedings of the 2022 ACM SIGSAC conference on computer and communications security (pp. 1291-1305).

***
## 연구 개요
비트코인을 악용하는 방식을 분석하고, 자동화된 거래 추적 기술인 **Back-and-Forth Exploration**을 제안함.

### 문제 정의
기존의 연구는 거래 흐름을 **Forward Transaction Tracing**을 통해 분석함. 
1. 역방향 탐색(Backward Tracing) 부재: 자금의 출처를 정의하기 어려움.
2. Transaction Graph Explosion: tagging database가 불완전할 경우, 비정상적으로 큰 트랜잭션 그래프가 생성되어 분석이 어려워짐.

***
## 연구 방법론: Back-and-Forth Exploration
![](assets/img/research/watch_your_back.png)  
### 1. 양방향 탐색 수행
   - 기존 Forward Tracing과 달리, 거래 흐름을 앞뒤로 탐색하여 더 많은 금융 관계를 파악함.
   - Backward Tracing을 통해 공격자의 초기 자금이 어디에서 왔는지 역추적이 가능함.
### 2. Transaction Graph Explosion 문제 해결
   - 머신러닝 기반 거래소 주소 분류기
     - 거래소 주소를 자동으로 탐지하여, 불필요한 노드 확장을 방지
     - 거래소로 입금한 이후의 거래소 내부 거래는 분석에서 제외
     - Random Forest 기반 분류기를 사용하여 거래소 주소를 판별
     - 42개의 feature를 사용하여, 거래소 주소를 95% 이상의 정확도로 탐지
   - 태깅 데이터베이스 활용
     - 비트코인 주소의 태그 정보를 수집하여 알려진 사이버 범죄 관련 주소를 식별
### 3. Back-and-Forth Exploration 시스템 구조
   - Address Context Analysis
     - **Multi-Input Clustering (MI Clustering)**을 을 수행하여 동일한 소유자의 다른 주소를 탐색
     - BlockSci를 활용하여, 해당 주소와 관련된 모든 입출금 트랜잭션을 분석
   - Address Classification
     - 태깅 데이터베이스와 머신러닝 분류기를 활용하여, 해당 주소가 거래소인지, 일반 사용자인지, 악성코드 운영자인지 판별
     - 거래소로 확인된 주소는 추가 탐색에서 제외하여 그래프 크기를 조절
   - Transaction Graph Update
     - 새로운 관계가 발견되면 이를 그래프에 추가
     - 불필요한 노드와 연결을 제거하여 탐색 효율성을 극대화

***
## 데이터셋 및 실험 과정
### 데이터셋 
공개된 보고서 및 연구 자료를 활용하여 비트코인을 악용하는 30개의 악성코드 패밀리를 선정함.
- 비트코인을 C2 서버로 사용하는 악성코드 (Cerber, Pony, Skidmap, Glupteba)
- 클리퍼 악성코드: 감염된 시스템의 클립보드를 가로채서 비트코인 주소를 공격자가 통제하는 주소로 바꾸는 악성코드
- 랜섬웨어

### 실험 과정
1. 악성코드 패밀리와 관련된 Seed Address 수집
  - 보안 보고서, 블로그, 기존 연구에서 각 악성코드가 사용한 비트코인 주소를 수집
  - 예: 랜섬웨어 캠페인에서 피해자에게 제공된 입금 주소, 클리퍼 악성코드가 대체한 주소 등
2. Seed Address에서 시작하여 전체 비트코인 네트워크에서 관련 트랜잭션 탐색
  - Back-and-Forth Exploration 기법을 사용하여 해당 주소의 트랜잭션을 양방향(입금 & 출금)으로 탐색
  - 발견된 주소를 분석하여 태깅 데이터베이스와 비교
  - 거래소, 도박 사이트, 다크웹 마켓과의 관계 분석
3. 탐색된 트랜잭션 그래프에서 새로운 사이버 범죄 패턴 분석
  - 다른 사이버 범죄와 연결되어 있는지 분석
  - 예: 특정 랜섬웨어 운영자가 다른 클리퍼 악성코드도 운영하고 있음을 발견

***
## 실험 결과
1. Back-and-Forth Exploration이 기존 기법보다 더 많은 금융 관계를 발견
   - 30개 악성코드 패밀리 중 93%에서 새로운 금융 관계 발견
   - 기존 Forward Tracing 기법과 비교했을 때, 평균적으로 3.6배 더 많은 주소를 탐색 가능
   - 랜섬웨어, 클리퍼, 봇넷 간에 새로운 관계가 밝혀짐.
2. 기존 연구에서 놓친 C2 서버 주소 발견
   - Cerber 랜섬웨어: 기존 연구에서 놓친 3개의 새로운 C2 주소 탐지
   - Pony 악성코드: 탐지되지 않은 8개의 새로운 C2 서버 탐지
   - Glupteba 봇넷: 2개의 새로운 백업 C2 주소 탐지
3. 악성코드 운영자의 수익 흐름 추적 가능
   - 랜섬웨어 및 클리퍼 악성코드 운영자들이 수익을 거래소로 세탁하는 과정 발견
   - 일부 클리퍼 악성코드 운영자들은 믹서를 사용하여 자금 세탁
   - 랜섬웨어 운영자는 수익을 도박 사이트 및 underground market에 사용


***
## 비판적 리뷰
기존 방법론(Forward Tracing)은 단방향이므로 한계를 가진다고 주장하지만, 실제로 조사를 할 때는 Backward Tracing도 반드시 병행하게 되어 있음. 즉, 논문에서 제안하는 기법이 새로운 방법론이라고 보기는 어렵다. 단방향보다는 양방향 모두를 보는게 관계 분석에 더 도움이 된다는 것을 수치적으로 표현한 논문 정도인 것 같다.