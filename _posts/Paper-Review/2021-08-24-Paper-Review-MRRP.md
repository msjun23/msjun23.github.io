---
title: "Paper Review: MRRP"
excerpt: "Multi Robot Route Planning(MRRP): Extended Spatial-Temporal Prioritized Planning"
toc: true
toc_sticky: true
categories:
  - Paper Review
tag:
  - Paper Review
  - Study
  - Path Planning
  - Multi Robot
  - Autonomous Driving
last_modified_at: 2021-08-27T14:30-15:00
---

> Multi Robot Route Planning(MRRP): Extended Spatial-Temporal Prioritized Planning
> 
> 주어진 맵에 Multi Robot이 충돌과 병목 현상 없이 각자의 경로 계획을 성공적으로 마치는 것을 목표로 한다.
>

<br>

# 1. Introduction
해당 논문은 ***Priotitized planning(우선순위 기반 경로 계획)*** 에 기반을 두고 있다. 우선순위 기반 경로 계획에 대해 설명하기 전에 더 큰 틀을 확인할 필요가 있다.

동시에 여러 로봇의 경로 계획을 수행하기 위해서는 다음과 두 가지 방법이 있다.
> 1. Centralized approach
>     - 완전한 최적의 경로를 계획할 수 있다.
>     - 하지만 연상량이 급격히 증가한다.
> 2. **Decentralized approach**
>     - 하나의 문제를 여러개의 작은 문제로 나눈다.
>     - 완전한 최적 경로가 아닐 수 있다.
>     - 본 논문에서 해당 방법론을 적용한다.

본 논문에서 사용되는 방법론인 Decentralized approach에 대해 설명하자면 다음과 같다. 각각의 로봇이 자신만의 경로를 계획한다. 그 후 모든 결과를 하나의 global plan으로 병합(merge)한다. 이와 같은 방법론을 적용한 대표적인 예시가 위에서 언급한 ***Prioritized planning***이다. 각 로봇에 우선순위를 부여하고, 순서대로 경로를 계획한다. 우선순위가 낮은 로봇은 선행된 로봇의 경로를 고려하여 이를 피해가는 경로를 계획하게 된다.

각 로봇에 대한 경로를 구하고 그것들을 탐색 그래프(search graph)에 추가하여 가능한 시나리오들을 구한다. 각각의 루트(routes, 경로(계획)과 혼용하여 사용)를 구하고 동기화를 수행한다.

<br>

# 2. Problem
우선순위 기반 경로 계획은 다음과 같은 상황에서 적절한 solution을 찾지 못한다.

![Paper-Review-MRRP1](/assets/images/Paper-Review-MRRP/Paper-Review-MRRP1.PNG){: .align-center}

위와 같은 상황은 충분히 마주할 수 있는 상황이고, 극복해야할 필요성이 있다. 이를 위해서, ***wait, avoid, push***와 같은 특수한 명령이 요구된다. 본 논문은 위와 같이 일반적인 우선순위 경로 계획법으로 해결하지 못하는 상황을 해결하는데 초점을 둔다.

Fig. 1에서 제기된 4가지 상황은 모두 시간 의존적(time-dependent)이다. 그렇기에 Multi Robot Router(MRR, 멀티 로봇 경로 계획)은 시간을 고려하여 경로를 계획한다. 시간을 변수로 사용함으로써 특정 로봇이 기다리는(wait) 행동을 수행할 수 있게 된다. 이와 같은 행동은 우선순위가 높은 로봇이 우선순위가 낮은 로봇의 경로를 방해할때 ***Collision resolver***에 의해 수행된다. Collision resolver는 공간적이고 일시적인 제약조건을 고려함으로써 다른 로봇의 경로를 방해하지 않는 경로 계획을 세울 수 있게한다.

<br>

# 3. Main Approach
기존에 통용되던 우선순위 기반 경로 계획 알고리즘은 Fig.1과 같은 문제를 적절히 해결할 수 없었다. 이번 챕터에서는 다음과 같은 순서로 주어진 문제 해결법을 제시한다.
> 1. Terminology
> 2. Data structure
> 3. Planner
> 4. Extension & Execution

## 3-1. Terminology
여러 로봇의 경로를 계획하기 위해 두 가지 개념이 소개된다.
- central multi robot route planner
- local path and trajectory controller

본 논문에서는 위의 두 가지 개념을 각각 ***Multi Robot Router(MRR)***, ***local controller***라고 명명한다. MRR은 각 로봇에 대해 병목 현상이 발생하지 않도록 경로를 계획하고 ***routing table***을 작성한다.

각각의 로봇들은 자신만의 Single Robot Router(SRR)를 수행한다. 이들은 각각 최종 route의 후보자가 된다. 모든 로봇들은 자신의 progress를 주기적으로(at low frequency, ~1Hz)로 공유하고, 이를 통해 로봇들간의 동기화(sychronization)를 이룰 수 있다.

최종적으로 맵이 만들어졌을 때 MRR은 그래프에 기반한다.이 그래프는 V_0...V_n 까지의 vertex로 구성되고 각각은 위치 가능한 공간(accessible environment)임을 나타낸다. 특정 vertex에 로봇이 위치한다는 것은 체크 포인트(check point)에 도달했다는 것이다.

## 3-2. Preconditions
- 주변 환경(environment)
- 체크 포인트간에 자율 주행을 수행할 수 있는 능력
- MRR(Multi Robot Router)

이 세가지 요소가 두 웨이 포인트(way point)간의 관계를 정의하는데 사용된다.

플래너(planner)의 task를 줄이고, 충돌을 방지하기 위해 모든 세그먼트(segment)는 로봇들의 사이즈보다 커야하는 것은 당연하다. 또한 각각의 로봇들은 한 시간 단위에 하나의 포인트를 점유해야 하고, 이와 같은 전제조건은 Fig. 1과 같은 상황에서 시간을 고려한 알고리즘을 구현할 수 있게 해준다.

##
