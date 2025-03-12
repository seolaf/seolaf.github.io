---
title: ["[Research] - Tracking counterfeit cryptocurrency end-to-end"]
date: 2024-04-04 09:00:00 +0900
categories: [research, crypto]
tags: [research]
---

> Gao, B., Wang, H., Xia, P., Wu, S., Zhou, Y., Luo, X., & Tyson, G. (2020). Tracking counterfeit cryptocurrency end-to-end. Proceedings of the ACM on Measurement and Analysis of Computing Systems, 4(3), 1-28.

# 연구 개요
이더리움 트랜잭션 데이터를 그래프 분석 기법을 활용하여 체계적으로 연구한 아마도(?) 최초의 연구  
저자는 자금 이체, 스마트 컨트랙트 배포, 스마트 컨트랙트 호출을 분석하기 위해 세 가지 그래프를 구축하고 이를 다양한 그래프 이론적 방법으로 분석함.  
### 연구의 주요 기여점
- 이더리움에서 자금흐름, 스마트 컨트랙트 배포 및 호출 패턴을 분석하기 위한 새로운 접근법 제안
- Money Flow Graph (MFG), Contract Creation Graph (CCG), Contract Invocation Graph (CIG) 구축
- 그래프 분석을 통한 이더리움 주요 특징 분석과 이상 행위 탐지 방법론을 제시
- attack forensics, anomaly detection, deanonymization 문제를 해결하기 위한 접근법 개발