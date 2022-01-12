---
title: "Mobile Robot Lec1 Kinematics"
excerpt: "About Mobile Robot Kinematics"
toc: true
toc_sticky: true
categories:
  - Study Mobile Robot
tag:
  - Study
  - Mobile Robot
  - Kinematics
last_modified_at: 2022-01-13T00:00-01:00
---

> 본 포스팅은 양정연 교수님의 SLAM 수업을 Reference로 공부한 내용을 정리한 것입니다.
>
> *출처: [Youtube, 양정연 교수님 SLAM](https://www.youtube.com/watch?v=pY2Vppa_y38&list=PL3Ax8f2laJ6vU449FdgWlVl2VtizhDIfj)*


# Mobile Robot
모바일 로봇, 혹은 이동 로봇은 특정 공간에 고정된 로봇이 아닌, 자체적인 동력을 가지고 이동을 할 수 있는 로봇을 말한다. 이와 같은 로봇에 대한 수요는 요즘 들어 급 부상하고 있다고 생각하고, 연구 소재로 많은 기관 및 기업에서 큰 관심을 가지고 있다. 이미 실증이 이뤄지고 있는 로보티즈의 일개미, 집개미나 우아한 형제들의 딜리와 같은 로봇들이 미래의 모습을 상상할 수 있게 해준다. 이러한 모바일 로봇을 보다 제대로 이해해보고자 한다.

모바일 로봇을 공부하기 위해 간단한 모델 하나를 소개하자면 다음과 같은 2-Wheel Robot을 들 수 있다.

![2wheelrobot](/assets/images/mobile-robot-lec1/2wheelrobot.PNG)

위와 같은 모바일 로봇이 원하는 속도를 가지고 회전과 이동을 수행하기 위해서는 많은 요소가 고려되어야 한다.
- Moment of Inertia
- Wheel Dynamics
- System Dynamics
- Disturbance
- Control Theorem