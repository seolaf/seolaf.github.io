---
title: ["[Research] - DEFCON33: The Ultimate Guide to Crypto Money Laundering"]
date: 2025-08-16 09:00:00 +0900
last_modified_at: 2025-08-16 09:00:00 +0900
categories: [research, crypto]
tags: [research, crypto, money laundering, dprk]
---

> Thomas Roccia. DEFCON 33 (2025). Where's my crypto, Dude? The Ultimate Guide to Crypto Money Laundering (and how to track it).
[pdf](https://media.defcon.org/DEF%20CON%2033/DEF%20CON%2033%20presentations/Thomas%20Roccia%20-%20Where%E2%80%99s%20My%20Crypto%2C%20Dude%20The%20Ultimate%20Guide%20to%20Crypto%20Money%20Laundering%20%28and%20How%20to%20Track%20It%29.pdf)

## Bybit 해킹 사건
2025년 2월, Bybit은 약 14.6억 달러 규모의 암호화폐를 탈취당했다. 공격자는 **Safe{Wallet}** 개발자의 AWS 환경을 침해해 코드 인젝션을 했고, 이를 통해 다중서명 지갑의 로직을 교체하여 거액의 자산을 유출했다. 탈취된 자산은 곧바로 여러 단계의 세탁 과정을 거쳐 흔적을 감추며 이동하기 시작했다. 

이 사건은 단순한 해킹에 그치지 않고, 자금 세탁 기법의 총집합을 보여준 사례로 기록되고 있다.

![Bybit Case](assets/img/research/CryptoML-bybit.png)  


## Crypto Money Laundering 101 (DPRK Edition)
북한 해커 그룹을 포함한 사이버 범죄자들은 암호화폐를 훔친 뒤 곧바로 현금화하지 않는다. 여러 단계를 거쳐 돈의 출처를 숨기고, 추적을 어렵게 만든 뒤 현금화한다. 
![alt text](assets/img/research/CryptoML-101.png)

### 1. Immediate Asset Conversion
#### 활용 기법 
- 탈취한 stETH, cmETH와 같은 토큰화 자산을 빠르게 ETH와 같은 자산으로 전환함.
- 토큰화 자산은 발행사가 동결할 수 있기 때문에 위험함.
- DEX를 이용해 신속히 ETH로 바꿔서 동결 회피 및 익명성을 확보함.
![alt text](assets/img/research/CryptoML-conversion.png)  
#### 추적 방법
- 탈취 직후 2시간 내 거래 패턴을 모니터링
- Uniswap, SushiSwap, 1inch 같은 DEX의 로그 확인
- 대량·반복적인 swap이나 가스비 패턴에서 단서 발견 가능

### 2. Layering via Multiple Wallets
#### 활용 기법
- 한 번에 50여 개 지갑으로 ETH를 나눠 보낸 뒤, 다시 수백개 주소로 자동 스크립트를 통해 분산함.
- 예: 10K ETH씩 50개 → 1K ETH씩 500개 → 더 작은 단위로 쪼개기
#### 추적 방법
- 송금 시점, 금액, 가스비 패턴을 비교해 자동화된 분산 여부 확인
- Multi-Hop 분석을 통해 여러 단계를 거쳐도 최종 합류 지점(Convergence Point)을 찾아낼 수 있음.
- 그래프 분석(Subgraph Extraction)을 통해 구조적인 패턴 식별 가능

### 3. Cross-Chain Bridges
#### 활용 기법
- ETH와 ERC-20 토큰을 BTC, Tron, 기타 체인으로 전송함.
- **Chainflip**, **MultiChain**, **Thorchain** 같은 브릿지를 사용함.
- Wrapped Token(WBTC, renBTC)도 중간단계로 활용함.
#### 추적 방법
- 브릿지 입출금 기록을 모니터링
- 시간·금액·토큰 종류를 기반으로 체인 간 이동을 매칭
- DPRK 관련 지갑 주소(OFAC 블랙리스트 등)와 대조

### 4. DEX Swaps
#### 활용 기법
- 규제·KYC 없는 **Uniswap**, **Dodo**, **Paraswap** 등에서 익명 지갑 간 swap함
- ETH → USDT → obscure token → ETH 같은 다중 스왑 경로를 활용함.
- Parallel Transaction과 대규모 유동성 풀 활용으로 흔적을 교란함.
#### 추적 방법
- DEX 스마트 컨트랙트 이벤트(Swap, AddLiquidity 등) 분석
- 수십 초 내 다단계 스왑을 추적해 자금 경로 재구성
- Flash Loan, MEV 패턴, Tornado Cash 같은 믹싱 서비스와의 연계 여부 확인

### 5. No-KYC Exchanges
#### 활용 기법
- 신분 확인 절차가 없는 즉시 스왑 서비스 이용(**eXch**)
- 대규모 세탁 시 거래소가 처리 용량을 초과해 일시 중단되기도 함.
#### 추적 방법
- eXch 입출금 지갑 주소와 패턴 매칭
- 입금 후 활동이 멈췄다가, 일정 시간 뒤 BTC로 인출되는 흐름 확인

### 6. ETH to BTC Conversion
#### 활용 기법
- Bybit 해킹 사건의 경우, ETH의 60%가 BTC로 전환됨.
- Wrapped BTC(WBTC, renBTC) → 브릿지 → BTC 네트워크 분산
- **Atomic Swap**, 크로스체인 브릿지를 통한 전환을 선호함.
#### 추적 방법
- ETH → WBTC 전환 이벤트 모니터링
- 브릿지의 Lock/Burn 이벤트와 BTC 체인에서의 출금 시간 매칭
- BTC에서 Peeling Chain 패턴 식별

### 7. Mixers & CoinJoin
#### 활용 기법
- **Tornado Cash**(ETH), **CoinJoin**(BTC), **Railgun** 등을 주로 사용함.
- 고정 금액 입금, 시간 지연 출금, 다중 라운드 믹싱으로 패턴을 교란함.
#### 추적 방법
- Mixer 스마트 컨트랙트와의 상호작용 기록 확인
- 입금·출금 시간/금액 기반의 통계적 상관관계 분석
- Mixer 이용 전후 지갑을 연결하는 Taint Analysis 수행

### 8. OTC Cash-Out
#### 활용 기법
- 마지막 단계에서 OTC 브로커 네트워크를 통해 현금화
- 규제가 약한 지역의 P2P 플랫폼을 활용함.
- 장기간에 걸쳐 점진적  출금을 통해 탐지를 회피함.
#### 추적 방법
- 대규모 스왑 후 스테이블 코인 입금 지갑 모니터링
- OTC 브로커 지갑 클러스터와의 연결 여부 확인
- Binance, Huobi, OKX 같은 대형 거래소의 입출금 지갑과 매칭

## Building an AI Agent
LLM 기반 AI를 활용해 자금 흐름을 자동으로 추적하는 시스템
### 구성 요소
- **Memory  / Context**: 수사 진행 중 발견되는 지갑, 트랜잭션, 패턴을 저장
- **Tooling**: 블록체인 explorer(Etherscan 등), 인텔리전스 API(Chainalysis, TRM), 블랙리스트(OFAC, mixer 주소) 연결
- **Scheme Identification**: 전형적인 세탁 패턴(예: Peeling chain, Gass fee 패턴, Multi-hop 경로)을 인식
- **Action**: 대규모 의심 거래 탐지, 의심되는 지갑/패턴 자동 리포트, 그래프 기반 시각화

