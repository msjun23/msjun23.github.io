---
title: "Starting Jetson Nano"
excerpt: "Introduction to Jetson Nano"
toc: true
toc_sticky: true
categories:
  - Jetson Nano
tag:
  - Jetson Nano
last_modified_at: 2021-08-08T12:30-13:00
---

Jetson Nano Developer Kit는 Image classification, object detection, segementation, 그리고 speech processing 등 다양한 뉴럴 네트워크를 병렬로 처리할 수 있는 작고 강력한 컴퓨터이다. 이 컴퓨터는 마이크로 유에스비 단자로도 파워 공급이 가능하며, 5와트의 전력으로도 작동한다.

[##_Image|kage@bpIdVM/btqIPRFwm1Q/HgSzLiIQkBzR9qSnZ9UAuk/img.jpg|alignCenter|data-origin-width="1200" data-origin-height="979" width="395" height="NaN" data-ke-mobilestyle="widthContent"|||_##]

마이크로SD 카드에 시스템 이미지를 삽입하는 것으로 해당 키트를 부팅할 수 있다. 시스템 이미지를 삽입하는 방법은 윈도우, 리눅스, 맥 모두 가능하며 하단 링크를 따라 Jetson Nano에서 Ubuntu 18.04를 부팅할 수 있을 것이다.

[starting-jetson-nano1](/assets/images/starting-jetson-nano/starting-jetson-nano1.jpg){: .align-center}

[

Getting Started With Jetson Nano Developer Kit

The NVIDIA® Jetson Nano™ Developer Kit is a small AI computer for makers, learners, and developers. After following along with this brief guide, you’ll be ready to start building practical AI applications, cool AI robots, and more.

developer.nvidia.com



](https://developer.nvidia.com/embedded/learn/get-started-jetson-nano-devkit)

마지막 스텝에선 다양한 Jetson Nano Tutorial들을 확인할 수 있다.

추가로 Jetson Nano Developer Kit는 이전 버전인 A02와 최신 버전인 B01 두가지가 있다. 버전에 따라 세부 핀이 다르고 카메라 연결 가능 갯수도 다르기에 구매시 이를 확인하는 것이 좋다.

[starting-jetson-nano2](/assets/images/starting-jetson-nano/starting-jetson-nano2.png){: .align-center}

---

## JetPack SDK

NVIDIA의 JetPack SDK는 AI 어플리케이션 구축을 위한 가장 포괄적인 솔루션이다. 모든 Jetson 모듈과 개발 키트는 JetPack SDK를 지원한다.

JetPack SDK는 리눅스 os시스템, 최신 버전의 리눅스 드라이버 패키지를 비롯해, 딥러닝, 컴퓨터 비젼, 가속 컴퓨팅 및 멀티미디어용 CUDA-X 가속화 라이브러와 API를 포함하고 있다. 또한 호스트 컴퓨터와 개발 키트를 위한 샘플, 문서, 개발 툴 등을 포함하고 있으며, 비디오 분석 스트리밍을 위한 DeepStream, 로보틱스를 위한 Isaac 등의 고수준 SDK도 제공한다.

현재 가장 최신 버전의 JetPack은 4.4버전이다. (작성일 2020.09.14)

JetPack은 위의 [Jetson Nano 시작 링크](http://developer.nvidia.com/embedded/learn/get-started-jetson-nano-devkit)를 따라 진행하면 설치를 완료할 수 있다.