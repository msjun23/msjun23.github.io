---
title: "[Xsens] ROS & xsens_mti_driver 사용하기"
excerpt: "ROS & xsens_mti_driver tutorial"
toc: true
toc_sticky: true
categories:
  - ROS
tag:
  - ROS
  - Xsens
  - MTi-680G
use_math: true
last_modified_at: 2022-04-15T11:00-11:30
---

# Xsens MTi driver
Xsens의 MTi-680G 센서를 사용해 GPS와 IMU 정보를 ROS 상에서 확인해보고자 한다.

![ros-mti-680g](/assets/images/ros-mti-680g/mti680g.jpg)

본 포스팅은 공식 [ROS documentation](http://wiki.ros.org/xsens_mti_driver)에 기반한다.

[Xsens 공식 SW 다운로드 센터](https://www.xsens.com/software-downloads)에서 MTi관련 패키지를 다운받아준다. 아래와 같이 확인할 수 있을 것이다.

```bash
└── MT_Software_Suite_linux-x64_20**.*
  ├── magfieldmapper_linux-x64_20**.*.tar.gz
  ├── mtmanager_linux-x64_20**.*.tar.gz
  ├── mfmsdk_linux-x64_20**.*.sh
  ├── mtsdk_linux-x64_20**.*.sh
  └── MTSS.README
``` 

해당 폴더에 가서 mtsdk_linux-x64_20**.*.sh 쉘을 실행하고 mti-driver를 설치한다. 이때 파일 버전은 본인이 받은 버전으로 맞춰준고 실행한다.(내가 받은 버전은 2021.4)
```bash
$ sudo apt-get install sharutils
$ cd ~/Downloads/MT_Software_Suite_linux-x64_2021.4/
$ sudo ./mtsdk_linux-x64_2021.4.sh
```

쉘을 실행하면 설치 위치를 물을 것이다. 이때 그냥 enter를 누르면 기본 위치인 **/usr/local/xsens/** 에 설치된다.

![mtsdk_install](/assets/images/ros-mti-680g/mtsdk_install.png)

# Build
**/usr/local/xsens/** 에 존재하는 **xsens_ros_mti_driver**를 **catkin_ws/src/** 로 복사한다.

그 후 아래 명령어를 순차적으로 실행한다.

```bash
$ cd ~/catkin_ws
$ pushd src/xsens_ros_mti_driver/lib/xspublic && make && popd
$ catkin_make
$ source devel/setup.bash   # bash 파일에 선언했다면 불필요

# Launch the Xsens MTi driver. Display example (orientation data should be enabled):
$ roslaunch xsens_mti_driver display.launch
```

**display.launch** 파일을 실행하면 rviz와 함께 노드가 실행된다. rviz 없이 오로지 센서 값만 사용하고 싶다면 아래 launch 파일을 실행하면 된다.

```bash
$ roslaunch xsens_mti_driver xsens_mti_node.launch
```

# Troubleshooting
혹시나 위 launch 파일을 실행시켰을 때 다음과 같은 에러가 발생할 수 있다. 당연히 아무런 센서 값도 받아지지 않을 것이다.

```bash
$ [ERROR] [~~~]: No MTi device found.
```

이는 센서가 연결된 포트의 권한 할당 문제인듯 싶다. 다음 명령어를 통해 ttyUSB0에 MTi가 정상적으로 연결되어 있음을 확인하고 권한을 부여하면 센서 사용이 가능할 것이다.

```bash
$ cd
$ ls -al /dev/ttyUSB*
$ sudo chmod 777 ttyUSB0
```

만약 ``` ls -al /dev/ttyUSB*``` 명령어를 입력했을 때 ttyUSB로 연결된 목록조차 안뜬다면 아래 방법 중 하나를 시도하면 된다.

```bash
# 임시 방법
$ sudo /sbin/modprobe ftdi_sio
$ echo 2639 0301 | sudo tee /sys/bus/usb-serial/drivers/ftdi_sio/new_id
```

```bash
# 영구적 인식 방법
# Create a file called “95-xsens-ftdi.rules” in the folder /etc/udev/rules.d with the following contents:
$ ACTION=="add" \
, ATTRS{idVendor}=="2639" \
, ATTRS{idProduct}=="0301" \
, RUN{builtin}+="kmod load ftdi_sio" \
, RUN+="/bin/sh -c 'echo 2639 0301 > /sys/bus/usb-serial/drivers/ftdi_sio/new_id'"
```

두 방법 중 하나를 시도하면 ``` ls -al /dev/ttyUSB*``` 명령어를 입력했을 때 ttyUSB0 포트가 보일 것이다. 이제 해당 포트에 ``` sudo chmod 777 ttyUSB0``` 명령어를 통해 권한을 부여해주면 된다.
