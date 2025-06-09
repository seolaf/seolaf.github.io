---
title: ["[Research] - SCAMMAGNIFIER: Piercing the Veil of Fraudulent Shopping Website Campaigns"]
date: 2025-05-07 09:00:00 +0900
last_modified_at: 2025-05-07 09:00:00 +0900
categories: [research, anomaly detection]
tags: [research]
---

> Bitaab, M., Karimi, A., Lyu, Z., Oest, A., Kuchhal, D., Saad, M., ... & Doupé, A. SCAMMAGNIFIER: Piercing the Veil of Fraudulent Shopping Website Campaigns.

***
## 개요
온라인 쇼핑 웹사이트 사기(fraudulent shopping websites)의 탐지 및 분석을 위한 새로운 프레임워크인 **SCAMMAGNIFIER**를 소개함. 연구는 2023년 5월부터 2024년 6월까지 수집한 1,155,237개의 쇼핑 도메인을 대상으로 진행되었으며, 46,746개의 사기 웹사이트를 탐지함.

***
## 방법론
### SCAMMAGNIFIER 프레임워크
SCAMMAGNIFIER는 세 가지 주요 단계로 구성됨:
1. **Data Collection**: 매일 신규 등록 도메인을 수집하고, WHOIS 정보, 웹사이트 콘텐츠, 스크린샷을 기록
2. **Filtering**: Beyond Phish 분류기를 활용해 수집된 웹사이트가 사기 가능성이 있는지 판별
3. **Auto-Checkout**: 실제 사용자의 결제 과정을 모방하여, 결제 게이트웨이의 상인 ID(merchant ID)를 수집

이 프레임워크는 Selenium 기반으로 구현되었으며, 자동화된 체크아웃 과정을 통해 사기 사이트의 결제 프로세스를 분석함.

***
## 데이터셋
* 수집된 도메인: 1,155,237개
* 탐지된 사기 웹사이트: 46,746개
* 자동 체크아웃 성공: 41,863건
* 추출된 상인 ID: 5,278개 (금융기관 A를 통해 4,484개 추출, 고유 ID는 2,790개)

***
## 실험 방법 및 결과
### 사기 사이트의 구조적 특징
* 연구 결과, 소수의 상인 ID가 많은 수의 사기 사이트를 관리하고 있음을 확인함. 예를 들어, 가장 많은 도메인(974개)을 관리하는 단일 상인 ID가 발견됨.
* 도메인 등록 후 첫 거래가 일어나기까지 대부분 10일 이내로 빠르게 진행되는 것으로 나타났으며, 97.73%가 1년 이내에 수익화를 시작함.

### 회피 전략
* 사기 사이트들은 결제 단계에서 다른 도메인으로 리다이렉션하여, 결제 프로세서가 원래 사이트를 확인하지 못하게 하는 전략을 사용함.

### 마케팅 전략
* 페이스북(인스타그램 포함, 28.78%), 구글(21.10%), 빙(9.38%)을 통한 광고로 대부분의 사용자가 사기 사이트로 유입되는 것으로 나타남.

### 경제적 분석
* 연구 결과, 사기 웹사이트 운영자들은 다양한 결제 게이트웨이와 전자상거래 플랫폼(Shopify, Wix, Squarespace 등)을 활용하며, 매우 조직적으로 운영되고 있음을 확인함.

***
## 사기 탐지 도구: SCAMCHECKER 브라우저 확장
* SCAMMAGNIFIER의 자동 체크아웃 기능을 활용한 크롬 기반 브라우저 확장을 개발하여 사용자가 사기 웹사이트 방문 시 실시간 경고를 제공함.
* Beyond Phish 분류기 단독으로는 59.30%였던 탐지율을 자동 체크아웃 기능과 결합함으로써 76.74%로 향상시킴.

***
## 결론
SCAMMAGNIFIER 연구는 사기 쇼핑 웹사이트의 생태계를 깊이 있게 이해하는 데 기여하였으며, 금융기관과의 협력을 통해 실제 환경에서 적용 가능한 탐지 및 예방 솔루션을 제안함. 


