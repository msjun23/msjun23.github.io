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
last_modified_at: 2021-08-31T00:00-00:30
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
기존에 통용되던 우선순위 기반 경로 계획 알고리즘은 Fig.1과 같은 문제를 적절히 해결할 수 없었다. 본 논문은 다음과 같은 순서로 주어진 문제 해결법을 제시한다.
> 1. Terminology
> 2. Preconditions
> 3. Framework and Structure
> 4. Collision Resolver

## 3-1. Terminology
여러 로봇의 경로를 계획하기 위해 두 가지 개념이 소개된다.
- central multi robot route planner
- local path and trajectory controller

본 논문에서는 위의 두 가지 개념을 각각 ***Multi Robot Router(MRR)***, ***local controller***라고 명명한다. MRR은 각 로봇에 대해 병목 현상이 발생하지 않도록 경로를 계획하고 ***routing table***을 작성한다.

각각의 로봇들은 자신만의 Single Robot Router(SRR)를 수행한다. 이들은 각각 최종 route의 후보자가 된다. 모든 로봇들은 자신의 progress를 주기적으로(at low frequency, ~1Hz)로 공유하고, 이를 통해 로봇들간의 동기화(sychronization)를 이룰 수 있다.

최종적으로 맵이 만들어졌을 때 MRR은 그래프에 기반한다.이 그래프는 $V_0...V_n$ 까지의 vertex로 구성되고 각각은 위치 가능한 공간(accessible environment)임을 나타낸다. 특정 vertex에 로봇이 위치한다는 것은 체크 포인트(check point)에 도달했다는 것이다.

## 3-2. Preconditions
- 주변 환경(environment)
- 체크 포인트간에 자율 주행을 수행할 수 있는 능력
- MRR(Multi Robot Router)

이 세가지 요소가 두 웨이 포인트(way point)간의 관계를 정의하는데 사용된다.

플래너(planner)의 task를 줄이고, 충돌을 방지하기 위해 모든 세그먼트(segment)는 로봇들의 사이즈보다 커야하는 것은 당연하다. 또한 각각의 로봇들은 한 시간 단위에 하나의 포인트를 점유해야 하고, 이와 같은 전제조건은 Fig. 1과 같은 상황에서 시간을 고려한 알고리즘을 구현할 수 있게 해준다.

## 3-3. Framework and Structure
MRR은 기본적으로 SRR 위에 성립된다. 

![Paper-Review-MRRP2](/assets/images/Paper-Review-MRRP/Paper-Review-MRRP2.PNG){: .align-center}

위 다이어그램과 같이 각 로봇은 자신보다 우선순위가 높은 로봇의 경로를 방해하지 않도록 경로를 계획하고, 이를 바탕으로 MRR을 수행한다. 이때 사용되는 것이 ***route coordinator***라는 개념이다. Route coordinator는 현재 경로를 유지했을 때 발생 가능한 충돌을 예측하고 ***Collision Resolver***를 통해 새로운 경로를 계획한다.

이후 SRR이 완료되면, 즉 한 로봇의 경로가 확정되면 이를 전체 루트와 동기화를 시킨다. 이것이 위에서 언급한 ***routing table***이다.

## 3-4.Collision Resolver
예견된 충돌을 피하기 위해 경로를 수정하는 것이 Collision resolver의 역할이다. Collision resolver는 기존의 그래프 맵을 복사하여 새로운 그래프 맵에 대해 고려한다. 논문에서는 그래프를 확장(***potential expander***)한다고 표현했다.

![Paper-Review-MRRP3](/assets/images/Paper-Review-MRRP/Paper-Review-MRRP3.PNG){: .align-center}

보다 정확한 회피 알고리즘은 다음과 같다.

### 3-4-1. Wait
말 그대로 다른 로봇이 특정 지역을 지날 때까지 기다리는 전략이다. 

![Paper-Review-MRRP4](/assets/images/Paper-Review-MRRP/Paper-Review-MRRP4.PNG){: .align-center}

Fig. 4. (a)에서 알 수 있듯이 서로 그전 자신의 목표를 향해 이동한다면 충돌이 발생하게 된다. 경로를 계획하는 시점에서 백트래킹 과정을 거치며 이와 같은 충돌 여부를 검사한다. 만일 충돌이 예상된다면 Collision resolver가 호출된다. Collision resolver는 Fig.4. (b)와 같이 확장된 그래프를 통해 '기다리는' 행동을 취한다. 그와 동시에 우선순위가 높은 $r_0$가 먼저 자신의 목표에 도달할 수 있게된다.

### 3-4-2. Avoid
우선순위가 낮은 로봇이 우선순위가 높은 로봇의 경로를 피해가는 전략이다.

![Paper-Review-MRRP5](/assets/images/Paper-Review-MRRP/Paper-Review-MRRP5.PNG){: .align-center}

Fig. 5의 경우에는 Wait 만으로는 아무도 목적지에 도달할 수 없다. Wait 전략을 적용하기 위해 확장된 그래프에서도 충돌이 발생될 것으로 예상되어, Fig. 5 (b)에선 한 차원 더 확장된 그래프의 모습을 볼 수 있다. $r_1$이 $V_4'$으로 피하고 $r_0$가 먼저 목적지에 도달할 수 있게 한다.

### 3-4-3. Push

![Paper-Review-MRRP6](/assets/images/Paper-Review-MRRP/Paper-Review-MRRP6.PNG){: .align-center}

위와 같은 상황은 우선순위가 낮은 로봇이 우선순위가 높은 로봇을 가로막고 있는 상황이다. 그래프에서 확인할 수 있듯이 $r_0$가 목적지에 도달하기 위해 $r_1$이 목적지를 지나 밀리고 있다. 이때 $r_1$은 $V_4'$과 $V_3'$ 두 노드로 밀리는 경우로 나뉘는데 $V_4'$로 밀리는 경우는 또다른 충돌이 발생한다. 즉, 충돌이 발생하지 않는 해는 $r_1$이 $V_3'$로 이동하는 경우이다.

### 3-4-4. Extensions to the Router
위에서 언급한 세 가지 경우가 일반적인 prioitized planning 방식이다. 하지만 이것들이 만능은 아니다.

![Paper-Review-MRRP7](/assets/images/Paper-Review-MRRP/Paper-Review-MRRP7.PNG){: .align-center}

Fig. 8과 같은 경우에는 위에서 언급한 방식 중 어느것으로도 해결할 수가 없다.

- Fig.8 (a)의 경우 우선순위가 높은 로봇이 우선순위가 낮은 로봇을 가로막고 있다. 우선순위가 높은 $r_0$의 경로는 이미 확정이 되었고, 우선순위가 낮은 $r_1$은 자신보다 우선순위가 높은 $r_0$의 경로에 대해 어떠한 영향도 끼칠 수 없다.

- Fig. 8 (b)의 경우는 두 로봇이 정확히 같은 거리를 이동하여 서로의 위치가 완벽히 바뀌기를 원하고 있다. 얼핏 봤을 때 위에서 언급한 Avoid 전략을 적용할 수 있을 것도 같다. 하지만 두 로봇의 속도가 같고, 정확히 같은 거리를 이동하기 때문에, 어느 한 로봇이 다른 루트로 피하기 전에 교차로에서 이미 충돌이 발생하고 만다. 즉, Avoid 전략을 적용할 수 없다.

이상의 두 경우를 해결하기 위한 추가적인 전략은 다음과 같다.

1. ***Priority Re-scheduling***

우선순으로 경로를 찾는 중 한 로봇의 SRR이 실패하면, 두 로봇의 우선순위를 재설정하여 다시 SRR을 수행한다. 우선순위를 재설정하는 두 로봇 중 하나는 당연히 현재 SRR이 실패한 로봇이다. 이 로봇을 $r_f$라고 한다. 우선순위를 교환할 또다른 로봇은 $r_f$와 가장 많이 충돌한 로봇이고, 이를 $r_c$라고 한다. 두 로봇의 우선순위를 변경하고 각각의 로봇에 대해 다시 SRR을 수행한다.

2. ***Speed Re-scheduling***

Fig. 8 (b)의 경우 우선순위를 바꾸는 것도 도움이 되지 못한다. 이때는 충돌이 발생하는 두 로봇 중 한 로봇의 속도를 재설정한다. 우선순위 재설정과 비슷하게 $r_f$와 $r_c$를 설정하여 $r_c$의 속도를 줄인다. $r_c$는 우선순위가 높은 로봇으로, 이미 경로가 확정된 상태이다. 그러므로 $r_c$가 확정된 경로를 따라 느리게 이동한다면 $r_f$는 더 빠르게 이동하며 $r_c$를 피할 길을 찾을 것이다.

# 4. Result

본 논문에서 제기한 중심 토픽은 로봇의 경로 계획에 있어 시간을 고려요소로 추가하는 것이었다. 각 로봇의 속도를 알고, 특정 시간에서 각 로봇의 위치를 예측하는 것으로 충돌을 예상하고 다시 경로를 계획한다.

다만, 주변환경이 매우 동적이면(바닥 표면이 일정하지 않은 등), 로봇의 속도가 일정하지 않은 경우에는 해당 방법론이 적절하지 않을 수 있다. 하지만 일반적인 실내 환경에서는 다음 실험 결과와 같이 잘 작동하는 것을 확인할 수 있다.

![Paper-Review-MRRP8](/assets/images/Paper-Review-MRRP/Paper-Review-MRRP8.PNG){: .align-center}

> **PR: Priory Re-scheduling**
>
> **SR: Speed Re-scheduling**
>
> **CR: Collision Resolving**
> 
> - $R_1$: PR = off, SR = off, CR = off
> 
> - $R_2$: PR = off, SR = off, CR = on
> 
> - $R_3$: PR = off, SR = on, CR = on
> 
> - $R_4$: PR = on, SR = off, CR = off
> 
> - $R_5$: PR = on, SR = on, CR = on (MRR)
> 

![Paper-Review-MRRP9](/assets/images/Paper-Review-MRRP/Paper-Review-MRRP9.PNG){: .align-center}

<br>
<br>
<br>

# Reference
- B. Binder, F. Beck, F. König and M. Bader, "Multi Robot Route Planning (MRRP): Extended Spatial-Temporal Prioritized Planning," 2019 IEEE/RSJ International Conference on Intelligent Robots and Systems (IROS), Macau, China, 2019, pp. 4133-4139, doi: 10.1109/IROS40897.2019.8968465
- [ROS tuw_multi_robot](http://wiki.ros.org/tuw_multi_robot)
