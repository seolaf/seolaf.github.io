---
title: ["[Research] - Graph Network Models To Detect Illicit Transactions In Block Chain"]
date: 2025-02-26 09:00:00 +0900
last_modified_at: 2025-02-26 09:00:00 +0900
categories: [research, crypto]
tags: [research]
---

> Adloori, H., Dasanapu, V., & Mergu, A. C. (2024). Graph Network Models To Detect Illicit Transactions In Block Chain. arXiv preprint arXiv:2410.07150.

***
## 연구 개요
Graph Attention Network 기반의 Residual Network 모델을 제안하여 AML 성능을 개선

### 기존 시스템의 한계
1. KYC: 적용이 불가능한 구간이 분명히 있음.
2. Rule-based: 새로운 유형의 불법 행위를 반영하지 못함.
3. ML-based: RF, XGBoost 등은 그래프 구조를 반영하지 못하여, 트랜잭션 간의 관계성을 학습하는 데 한계가 있음.

***
## 연구 방법론
### 데이터셋
**Elliptic Dataset**
- 203,769개의 노드, 234,355개의 엣지
- 4,545개(2%)의 불법 거래, 42,019개(21%)의 합법 거래, 나머지(78%)는 미분류
- 각 노드에는 166개의 feature가 있음. 94개는 local features, 72개는 트랜잭션 정보 기반의 aggregated features

### 그래프 네트워크 모델 적용
#### 1. Graph Convolutional Network (GCN)
- 노드 간 인접 정보를 활용하여 특징을 학습하는 방법
- 고차원 정보를 충분히 반영하지 못하는 한계가 있음.
#### 2. Graph Attention Network (GAT)
- 각 노드가 주변 노드로부터 얼마나 중요한 정보를 얻을지 Self-Attention 기법을 활용하여 학습
- GCN보다 높은 성능을 보이지만, 딥러닝 학습 과정에서 gradient vanishing 문제가 발생할 수 있음.

#### 3. GAT-ResNet (제안 모델)
- GAT의 성능을 개선하기 위해 Residual Connection을 도입
- ResNet이 딥러닝 기울기 소실을 해결하는 것처럼, GAT-ResNet은 깊은 그래프 네트워크에서도 학습 안정성을 유지함.

***
## 실험 및 결과
### 실험 설정
- 데이터셋 분할
  - train: 초기 34개 time steps
  - test: 이후 15개 time steps
  - 비율 7:3
- 하이퍼파라미터
  - GCN: 2-layer, Adam (learning rate: 0.001)
  - GAT: 2-layer, 8-head, Adam (learning rate: 0.001)
  - GAT-ResNet: 3-layer, 4-head, Adam (learning rate: 0.001)

### 성능 비교
| Algorithm            | Precision | Recall | F1 Score | Micro-Avg F1 Score |
|----------------------|-----------|--------|----------|---------------------|
| Logistic Regr<sup>\(AF\)  | 0.454     | 0.633  | 0.529    | 0.928               |
| Logistic Regr<sup>\(AF-NE\)  | 0.457     | 0.651  | 0.537    | 0.929               |
| Logistic Regr<sup>\(LF\)  | 0.433     | 0.654  | 0.521    | 0.924               |
| Logistic Regr<sup>\(LF-NE\) | 0.478     | 0.653  | 0.552    | 0.933               |
| RandomForest<sup>\(AF\)   | 0.981     | 0.651  | 0.782    | 0.977               |
| RandomForest<sup>\(AF-NE\)   | 0.984     | 0.647  | 0.781    | 0.977               |
| RandomForest<sup>\(LF\)   | 0.909     | 0.648  | 0.757    | 0.973               |
| RandomForest<sup>\(LF-NE\) | 0.914     | 0.646  | 0.757    | 0.974               |
| XGBoost<sup>\(AF\)        | 0.982     | 0.617  | 0.758    | 0.975               |
| XGBoost<sup>\(AF-NE\)        | 0.982     | 0.613  | 0.755    | 0.975               |
| XGBoost<sup>\(LF\)        | 0.929     | 0.598  | 0.723    | 0.971               |
| XGBoost<sup>\(LF-NE\)      | 0.937     | 0.598  | 0.73    | 0.972               |
| GCN                  | 0.812     | 0.512  | 0.628    | 0.961               |
| GAT                  | 0.857     | 0.516  | 0.644    | 0.963               |
| **GAT-ResNet**       | **0.956** | **0.581** | **0.723** | **0.971** |

전체 모델 중에서는 RandomForest가 가장 높은 성능을 보였지만, 그래프 네트워크 모델 중에서는 GAT-ResNet이 가장 높은 성능을 보임.


***
## 비판적 리뷰
그래프 네트워크 모델이 전통적인 ML 모델에 비해 낮은 성능을 보였지만, 논문에서 제시한 방법론으로 어느정도 성능을 개선시킴.  
RF와 앙상블하여 모델을 구축한다면 성능이 더 좋아질까 궁금함.