---
title: "Virtual Mouse using Hand Detection"
excerpt: "2020 상반기 바람 프로젝트"
toc: true
toc_sticky: true
categories:
  - Projects
tag:
  - Projects
  - BARAM
  - Deep Learning
  - Detection
  - Ubuntu
last_modified_at: 2021-12-05T18:30-20:00
---

**프로젝트 링크:** [github](https://github.com/msjun23/hand-mouse) <br>
**진행 기간:** 2020.3월 ~ 7월

해당 프로젝트는 [이 곳](https://github.com/MrEliptik/HandPose)을 참조하여 제가 구현하고자 한 내용을 적용했습니다.

---
# Introduction & Motive
2020년 처음 딥러닝이라는 분야를 접해본 계기가 되는 프로젝트였다. 당시 CES2020에서 발표한 [투명 키보드](https://youtu.be/5ul6NwujWIw)를 보고 이를 직접 구현해보고 싶어 영상처리와 딥러닝이라는 것을 처음 공부하기 시작했다. 하지만 처음부터 이러한 완성형 어플리케이션을 만들기는 어려웠기에 천천히 시작하기로 했다.

딥러닝이라는 분야가 가장 핫하게 사용되는 detection과 classification을 해보고자 했다. 처음에 생각했던 컨셉을 가져가며, 두 분야를 적절히 융합한 어플리케이션을 구현해보고자 하여, **Virtual Mouse using Hand Detection**이라는 프로젝트를 진행하게 되었다.

# System Architecture
![sys_arch](/assets/images/hand-mouse/sys_arch.png)

# Implementation
[Tensorflow API](https://github.com/tensorflow/models/tree/master/research/object_detection)를 사용하여 직접 제작한 데이터셋으로 전이학습을 수행했다.

![tf_API](/assets/images/hand-mouse/tf_API.jpg)

손을 인식한 결과인 bounding box의 중심점을 반환하고, 파이썬 입력 자동화 라이브러리인 pyautogui를 사용하여 마우스가 손을 따라다니도록 구현했다.

![result](/assets/images/hand-mouse/result.jpg)

그리고 현재 손동작을 9가지 모습으로 구분하여 각기 다른 입력 명령을 수행하도록 했다. 이 때 각각의 손동작은 크게 변하고 특징치가 확실할 때 구분이 잘 되어 알파벳 수화 중에서 특징이 큰 동작들을 선정하여 사용했다.

![my_handpose](/assets/images/hand-mouse/my_handpose.png)<br>
![txt](/assets/images/hand-mouse/txt.jpg)

# Result
그 결과는 아래와 같다.
![4](/assets/images/hand-mouse/4.gif)

# Epilogue
처음 딥러닝이라는 분야를 접하고 공부하게 된 프로젝트였다. 이 프로젝트를 시작으로 딥러닝, 그 중 객체 인식(Object Detection)이라는 분야에 대해 관심을 가지게 되었다. 이후에도 계속 딥러닝을 공부하고자 했고, 점차...
> 시작: 딥러닝, 객체 인식<br>
> -> 컴퓨터 비전 & 학습, 딥러닝<br>
> -> + 로봇(내가 항상 하고 싶어했던.)<br>
> -> 로봇 비전, 딥러닝을 곁들인

이와 같은 순서로 나의 관심 분야 혹은 내 목표가 정해지지 않았을까 싶다. 이 프로젝트가 끝나고 1년이 넘게 지나서야 이 포스팅을 하고 있지만, 지금도 여전히 딥러닝, 로봇 비전에 대한 관심은 여전하다고 생각한다. 실제로 그동안 공부한 내용이 모두 학습과 로봇 비전, 모바일 로봇에 관련된 내용이고, 진행한 몇 가지 프로젝트의 컨셉 또한 그러하다. 앞으로 대학원에 진학을 하던, 취업을 하게되던 내가 나아갈 방향은 아마도 큰 변화가 없으리라 생각한다.
