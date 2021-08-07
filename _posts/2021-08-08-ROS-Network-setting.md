---
title: "ROS Network Setting"
excerpt: "How to set ROS network?"
toc: true
toc_sticky: true
categories:
  - ROS
tag:
  - ROS
  - roscore
last_modified_at: 2021-08-08T05:30-06:00
---

ROS를 이용하는 가장 큰 이점 중 하나는 다양한 플랫폼을 하나로 묶을 수 있다는 것이다. 예를 들어 PC에서 마스터 노드를 열고 Jetson nano에서 마스터 노드로 접근해 마치 하나의 플랫폼 인양 토픽 등의 정보를 주고받을 수 있다. 마스터 노드는 쉽게 말해 roscore를 실행하는 곳이다. roscore는 하나만 실행해야 한다.

둘 이상의 기기를 하나의 ROS 환경 안에 묶기 위해서는 사전작업이 필요하다.

## **1\. 같은 네트워크**

같은 wifi에 연결해야 한다.

## **2\. 환경변수 설정**

환경변수는 모든 기기에서 설정해 줘야 한다. 이때 마스터 기기와 다른 기기들에 차이가 있다.

-   마스터

```
export ROS_IP={ip of master}
export ROS_MASTER_URI=http://localhost:11311
export ROS_HOSTNAME=$ROS_IP
```

-   주변 기기

```
export ROS_MASTER_URI=http://{ip of master}:11311
export ROS_HOSTNAME={my ip}
```

## **\* ip 주소 확인**

```
$ ifconfig
```

터미널에서 위 명령을 입력해 ip 주소를 알 수 있다.
