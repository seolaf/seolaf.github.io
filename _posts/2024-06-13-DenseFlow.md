---
title: ["[Research] - DenseFlow: Spotting Cryptocurrency Money Laundering in Ethereum Transaction Graphs"]
date: 2024-06-13 09:00:00 +0900
last_modified_at: 2024-06-13 09:00:00 +0900
categories: [research, crypto]
tags: [research]
---

> Lin, D., Wu, J., Yu, Y., Fu, Q., Zheng, Z., & Yang, C. (2024, May). DenseFlow: Spotting cryptocurrency money laundering in ethereum transaction graphs. In Proceedings of the ACM Web Conference 2024 (pp. 4429-4438).

***
## 연구 개요
DenseFlow라는 새로운 프레임워크를 통해 dense subgraphs 탐지 및 maximum flow 분석 기법을 활용하여 자금 세탁 행위를 식별하는 방법을 소개함.

### 문제 정의
1. C1: 대규모 조직화된 세탁 그룹
   - 주소 생성이 무료이고 쉽기 때문에, 해커들은 수천 개의 주소를 만들고 자금 세탁을 진행
   - 거래 기록을 복잡하게 만들어 탐지를 어렵게 함.
2. C2: 복잡한 다중계층의 자금 이동
   - 여러 개의 중간 주소를 활용하여 자금의 출처를 숨기기 위해 경로를 복잡하게 구성
   - 일반적인 사용자 주소 및 거래소와의 상호작용을 통해 흔적을 숨김.
  
***
## 연구 방법론: DenseFlow 프레임워크
그래프 기반 이상 탐지 및 최대 유량 알고리즘을 결합하여 동작함.
### Suspiciousness Metric
4가지 주요 특성을 정의하고 이를 기반으로 정밀한 의심 점수를 계산하여 탐지 성능을 개선함.
1. Dense Transfer → 그래프 밀도 기반 탐지
   - 세탁 주소내에서 빈번한 자금 이동이 발생
   - 의심스러운 계정들은 네트워크 내에서 높은 노드 밀도를 형성
2. Temporal Surge → 시간 기반 이상 탐지
   - 짧은 시간 내 집중적으로 거래 발생
   - MultiBurst 알고리즘을 활용하여 peak points를 검출
3. Amount Intensity → 거래 금액 기반 탐지
   - 특정 기간 동안 자금 이동량 급증이 발생한 경우 이를 의심스러운 트랜잭션으로 평가
4. Rating Deviation → 통계적 편차 측정
   - KL Divergence를 적용하여 정상 주소와 비교하여 트랜잭션의 의심스러운 정도를 정량적으로 평가

### Dense Subgraph Detection
- [Charikar의 greedy peeling method](https://15721.courses.cs.cmu.edu/spring2019/papers/24-costmodels/10.1.1.121.1184.pdf)를 활용하여 의심스러운 계정을 포함하는 최적의 하위 그래프를 탐색
- 서브셋의 의심점수 `g(S)`를 최적화하면서 반복적으로 노드를 제거하여 최적의 세탁 그룹을 탐색

### Tracing Maximum Flow 
- [Maximum Flow 문제](https://dl.acm.org/doi/pdf/10.1145/48014.61051)를 해결하여 자금이 실제로 이동한 경로를 탐색
- 초기 주소에서 시작하여 여러 개의 세탁 주소를 거쳐 최종 목적지까지의 경로를 추적


***
## 실험 및 성능 평가
### 데이터셋
4개의 실제 이더리움 해킹 사건 데이터셋을 사용  

| 데이터셋 | 주소 수 | 거래 수 | 세탁 주소 수 | 기간(일) |
|---------|---------|------------|-----------------|-----------------|
| **PlusTokenPonzi** | 34,521 | 58,049 | 30,782 | 817 |
| **AlphahomoraExplot** | 76,130 | 612,349 | 6,960 | 2,494 |
| **CryptopiaHack** | 152,779 | 815,242 | 8,787 | 2,481 |
| **UpbitHack** | 377,912 | 1,627,861 | 16,533 | 2,310 |

### 비교 기법
기존의 3가지 최첨단 기법과 성능 비교를 수행
1. [FRAUDAR](https://dl.acm.org/doi/pdf/10.1145/2939672.2939747) - 밀집 노드 기반 이상 탐지
2. [CubeFlow](https://arxiv.org/pdf/2103.12411) - 자금 이동 흐름 기반 탐지
3. [HoloScope](https://dl.acm.org/doi/pdf/10.1145/3132847.3133018) - 트랜잭션 피크 및 그래프 구조 활용 탐지

### 실험 결과
![](assets/img/research/denseflow.png)  
DenseFlow는 기존 방법들보다 필요한 탐지 계정 수를 최소화하면서도 높은 탐지 성능을 유지  

***
## 비판적 리뷰
전반적으로 괜찮은 방법론을 제시한 것 같다. 다만, 성능을 비교하기 위한 알고리즘들이 개념이 좀 동떨어진 것들이 아니었나 싶다. Elliptic Dataset을 활용한 GNN 기반 자금 세탁 탐지 알고리즘이라든지, 암호화폐 트랜잭션 그래프에 특화된 알고리즘끼리 비교를 하는게 정당하지 않았나 생각한다.