---
title: ["[Research] - Understanding ethereum via graph analysis"]
date: 2024-03-21 09:00:00 +0900
categories: [research, crypto]
tags: [research]
---

> Chen, T., Li, Z., Zhu, Y., Chen, J., Luo, X., Lui, J. C. S., ... & Zhang, X. (2020). Understanding ethereum via graph analysis. ACM Transactions on Internet Technology (TOIT), 20(2), 1-32.

***
## 연구 개요
이더리움 트랜잭션 데이터를 그래프 분석 기법을 활용하여 체계적으로 연구한 아마도(?) 최초의 연구  
저자는 자금 이체, 스마트 컨트랙트 배포, 스마트 컨트랙트 호출을 분석하기 위해 세 가지 그래프를 구축하고 이를 다양한 그래프 이론적 방법으로 분석함.  
### 연구의 주요 기여점
- 이더리움에서 자금흐름, 스마트 컨트랙트 배포 및 호출 패턴을 분석하기 위한 새로운 접근법 제안
- Money Flow Graph (MFG), Contract Creation Graph (CCG), Contract Invocation Graph (CIG) 구축
- 그래프 분석을 통한 이더리움 주요 특징 분석과 이상 행위 탐지 방법론을 제시
- attack forensics, anomaly detection, deanonymization 문제를 해결하기 위한 접근법 개발

***
## 연구 방법론
### 데이터 수집
2015.07.30. - 2018.11.01. 사이의 모든 트랜잭션 데이터를 수집
- 39,572,734개의 EOA, 8,725,788개의 CA
- 336,410,379개의 external 트랜잭션과 333,011,145개의 internal 트랜잭션을 포함
  * internal 트랜잭션은 체인에 기록되지 않기 때문에, 이더리움 클라이언트(geth)를 수정하여 모든 internal 트랜잭션을 수집하는 방법을 개발
  * geth의 EVM에 **EVM opcode**를 감지하는 코드를 삽입하여 스마트 컨트랙트가 실행될 때 발생하는 모든 internal 트랜잭션을 수집

### 그래프 구축
#### 1. Money Flow Graph (MFG)
   - 전체 네트워크에서 ETH 거래 흐름을 나타냄.
   - 노드의 88%가 EOA 간의 자금 이체를 포함
   - 몇 개의 중앙 노드(hub nodes, 거래소 등)가 존재하여 네트워크 구조가 비트코인 네트워크와 유사
#### 2. Contract Creation Graph (CCG)
   - 0.3%의 EOA만이 스마트 컨트랙트를 생성
   - 스마트 컨트랙트의 99.2%는 단 한 번만 생성됨.
#### 3. Contract Invocation Graph (CIG)
   - 66%의 EOA는 스마트 컨트랙트를 호출하지 않음.
   - 47%의 스마트 컨트랙트가 호출되지 않음.
   - 84%의 호출 트랜잭션은 EOA에서 스마트 컨트랙트로 발생

### 그래프 분석
#### 1. 노드 중심성 (Centrality)
   - PageRank, Degree Centrality를 이용하여 중요 노드를 분석
   - MFG에서 가장 중요한 노드는 거래소 관련 계정
   - CIG에서는 **토큰 컨트랙트**가 높은 중심성을 가짐.
#### 2. 연결성 분석 (Connectivity Analysis)
   - MFG에서 매우 큰 연결된 네트워크가 발견되었는데 이는 대부분 거래소 관련
   - MFG의 가장 큰 strongly connected components (SCC)가 전체 노드의 75% 차지
   - CCG의 연결 구성요소는 대부분 작은 그룹으로 분리 → 스마트 컨트랙트는 독립적인 경우가 많음.
#### 3. 상관관계 분석 (Correlation Analysis)
   - MFG의 Pearson 상관계수: 0.45 (자금 input, output이 비례관계)
   - CIG의 Assortativity 계수: 0 (스마트 컨트랙트 호출이 특정 노드에 집중되지 않음.)

***
## Security Issues in Ethereum
### 1. Attack Forensics
네트워크에서 발생하는 다양한 유형의 공격에서 공격자가 소유한 여러 주소를 탐지하는 그래프 기반 접근법을 제안함.  
1. 공격자의 초기 주소 확보
2. 그래프 기반 확장 분석
   - MFG에서 ETH 이동 패턴을 분석
   - 다른 주소를 거쳐 자금이 돌아오는지 확인
   - 공격자 주소에서 배포한 스마트 컨트랙트를 추적하여 관련된 추가 주소를 식별
3. 의심스러운 주소 탐색
   - 비정상적으로 많은 스마트 컨트랙트를 배포하는 주소 (특정 공격 유형과 관련)
   - 자금 흐름 패턴이 일반 사용자와 다른 주소 (예: 단기적으로 대량의 ETH가 이동)

### 2. Anomaly Detection
비정상적인 스마트 컨트랙트 및 주소를 자동으로 탐지하는 기법을 제안함.  
1. CCG 분석
   - 특정 주소가 수천 개의 스마트 컨트랙트를 생성하는 경우, 이를 이상 계정으로 분류
   - 대부분의 사용자는 1-5개의 스마트 컨트랙트만 생성하지만, 특정 주소에서 수천 개의 컨트랙트를 생성하는 패턴이 발견됨.
2. CIG 분석
   - 생성된 후 한 번도 호출되지 않은 스마트 컨트랙트를 탐색
   - 비정상적으로 많은 컨트랙트가 호출되지 않는다면 공격을 위한 임시 컨트랙트일 가능성이 있음.
3. MFG 분석
   - 특정 주소에서 짧은 시간 동안 다수의 주소와 거래를 수행하는 경우, 자금 세탁일 가능성이 있음.
   - 정상적인 사용자의 경우 ETH 이동 패턴이 상대적으로 일정한 반면, 이상 주소는 특정 시점에 집중적인 거래를 수행함.

### 3. Deanonymization
그래프 분석을 활용하여 특정 주소의 익명성을 해제하는 기법을 개발함.  
1. 거래소 주소 분석
   - 거래소 주소의 입출금 패턴을 분석하여 사용자와 연결 가능성이 있는 주소를 확인
   - 예: 특정 주소가 Binance 거래소 주소를 통해 ETH를 반복적으로 입/출금하는 경우, Binance 사용자일 가능성이 높음.
2. 스마트 컨트랙트 호출 패턴 분석
   - 특정 주소가 반복적으로 동일한 스마트 컨트랙트를 호출하는 경우, 사용자의 활동을 추적할 수 있음.
3. IP 주소 및 타임스탬프 분석 (Off-Chain 데이터 활용)
   - 거래소와 협력하여 IP 주소 및 타임스탬프 데이터 활용이 가능함.

***
## 비판적 리뷰
전체 트랜잭션에 대한 graph analysis 결과는 상당히 흥미롭다. 하지만, Section 7(Applications based on Graph Analysis)의 내용이 굉장히 naive하다.  
Attack Forensics와 Anomaly Detection에서 제시한 탐지 방법론의 기준이 단순하여 오탐의 가능성이 굉장히 높음.  
또한, Deanonymization은 연구 결과(graph analysis)와도 큰 관계가 없고, 너무 당연한 소리..  

그리고 가장 큰 문제는 이더리움 네트워크는 동적인 그래프인데, 특정 시점에서의 정적인 그래프로 분석한 것에 대한 의문이 든다. 시간에 따른 트랜잭션 흐름을 고려한 방법론이 필요함.