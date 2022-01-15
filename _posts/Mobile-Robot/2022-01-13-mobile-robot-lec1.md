---
title: "[Lecture 1] Mobile Robot Kinematics"
excerpt: "About Mobile Robot Kinematics"
toc: true
toc_sticky: true
categories:
  - Mobile Robot
tag:
  - Study
  - Mobile Robot
  - Kinematics
use_math: true
last_modified_at: 2022-01-13T00:30-01:00
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

Inertia나 Wheel dynamics, 제어 이론 등은 본 강의에서 많은 시간을 할애하여 언급하지는 않는다. 기본적인 내용이거나, 혹은 제어 이론과 같이 보다 하위단 내용을 파고들고자 한다면 상당히 긴 강의가 될 것이다.

이들에 대한 기본적인 지식이 있다는 가정하에 모바일 로봇 이론에 대해 강의를 진행한다.

# Mobile Robot Kinematics

## Translation
다음과 같이 간단한 2-Wheel Mobile Robot을 가정해보자.

![2wheelrobot_diagram](/assets/images/mobile-robot-lec1/2wheelrobot_diagram.PNG)

로봇의 중심을 기준으로 좌우, 같은 간격으로 두 개의 바퀴가 위치한다. 이 로봇은 이동할 때 두 바퀴를 굴려 이동할 것이다. 이때 두 바퀴의 선속도를 각각 $v_L, v_R$로 정의했다. 그렇다면 로봇 자체의 선속도는 두 바퀴 선속도의 평균으로 다음과 같이 정의될 것이다.

<center> $v=\frac{v_L+v_R}{2}$ </center>

## Rotation
위 그림과 같은 모바일 로봇은 두 바퀴의 선속도를 다르게 하여 회전 운동을 할 수 있다. 이는 딱히 설명하지 않아도 당연한 것으로 생각된다. 이제는 로봇의 회전속도를 구해볼 필요가 있다. 먼저 다음의 회전속도와 선속도의 기본 공식을 알고 있어야 한다.

<center> $v=r\times w$ </center>

이제 로봇이 회전하는 순간을 도식화하여 그려보자.

![2wheelrobot_diagram2](/assets/images/mobile-robot-lec1/2wheelrobot_diagram2.PNG)

강의자료에서는 로봇의 회전 중심을 $ICC(Instantaneous Center of Curvature)$로 명시했다. 로봇이 $ICC$를 중심으로 회전할 때 양측 바퀴에 위에서 언급한 $v=r\times w$ 식을 적용하면 다음과 같이 로봇의 회전속도 $w$를 도출할 수 있다.

<center> $ v_L=(R-a)\times w, v_R=(R+a)\times w<br>
         \frac{v_L}{w}+a=\frac{v_R}{w}-a=R<br>
         2a=\frac{v_R-v_L}{w}<br>
         \therefore w=\frac{v_R-v_L}{2a} $ </center>

이제 로봇의 병진속도(**Translation**)와 회전속도(**Rotation**)를 알 수 있다.



