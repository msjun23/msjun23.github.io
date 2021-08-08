---
title: "ROS Installation"
excerpt: "How to install ROS-Melodic?"
toc: true
toc_sticky: true
categories:
  - ROS
tag:
  - ROS
  - Installation
  - Jetson Nano
last_modified_at: 2021-08-08T03:10-04:00
---

**ROS 설치 공식 문서는 해당 [링크](http://wiki.ros.org/melodic/Installation/Ubuntu)에서 확인할 수 있다.**

## **0\. ROS란?**

 **ROS**(Robot Operating System)는 그 이름 그대로의 의미를 가진다. 이 글을 읽고 있는 시점에서 운영체제와 같은 기본적인 정보는 모르지 않을 것이라 믿고 있다. 많이들 접하는 운영체제로는 컴퓨터를 위한 Window, Mac OS, Linux 등이 있고, 스마트폰을 위한 Android, ios 등이 있다. ROS란 정말 단순히 말하자면 이와 같은 **로봇**을 위한 **운영체제**이다. 하지만 완벽한 운영체제라고 하지는 않는다. 완벽히 따지고 들자면 ROS는 다른 기본적인 운영체제 위에서 실행되는 **메타 운영체제**로 분류되며, 시뮬레이션, 응용 소프트웨어 등 로봇 개발에 있어 많은 이점을 제공한다.

## **1\. Setup your sources.list**

packages.ros.rog에서 소프트웨어를 다운로드하기 위한 설정을 먼저 진행한다.

```
$ sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
```

## **2\. 공개키 추가**

서버에서 패키지를 다운로드하기 위해 키를 추가해야 한다.

```
$ sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
```

## **3\. 설치**

ROS 설치에 앞서 Debian 패키지가 최신인지 확인한다.

```
$ sudo apt update
```

ROS에는 다양한 라이브러리와 툴이 존재한다. 설치를 진행할 때도 아래와 같이 여러 버전으로 나뉘어 있어 **본인에게 필요한 것을 다운로드하면** 된다. 물론 각각의 ROS 패키지를 따로 다운로드할 수도 있다.

-   Desktop-Full Install : 그냥 다 들어있다고 보면 된다.

```
$ sudo apt install ros-melodic-desktop-full
```

-   Desktop Install : 2D/3D 시뮬레이션 등이 빠져있다. 나는 PC, Jetson nano 둘 모두에 ROS를 설치하였는데, PC는 FULL 버전으로 설치하였고, Jetson nano는 그냥 Desktop 버전으로 설치하였다.

```
$ sudo apt install ros-melodic-desktop
```

-   ROS-Base : 기본적인 것들만 깔리고, GUI 툴이 없다. Jetson nano에 ROS를 깔 때 대부분 Desktop 버전 아니면 ROS-Base 버전 둘 중 하나로 까는 것 같다.

```
$ sudo apt install ros-melodic-ros-base
```

-   Individual Package : PACKAGE 부분에 본인이 원하는 패키지 명을 삽입하면 된다.

```
$ sudo apt install ros-melodic-PACKAGE

# e.g.
$ sudo apt install ros-melodic-slam-gmapping
```

## **4\. 환경변수 설정**

다음 명령어는 ROS 환경변수를 매 실행마다 자동으로 적용되게 도와준다.

```
$ echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc
$ source ~/.bashrc
```

## **5\. 빌드를 위한 도구와 의존성 패키지 등 설치**

```
$ sudo apt install python-rosdep python-rosinstall python-rosinstall-generator python-wstool build-essential
```

또한 ROS를 사용하기 전에 반드시 **rosdep을 초기화**해야 한다. rosdep은 ROS의 소스를 컴파일하거나, 다른 요소들을 쉽게 다운로드할 수 있도록 도와준다.

```
$ sudo rosdep init
$ rosdep update
```

## **6\. 설치 확인**

```
$ roscore
```

터미널에 위와 같이 입력했을 때 버전 정보와 SUMMARY 아래로 이런저런 게 뜨면 정상적으로 설치를 끝낸 것이다.
