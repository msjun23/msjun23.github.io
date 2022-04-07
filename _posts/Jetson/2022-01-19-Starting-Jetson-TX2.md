---
title: "[TX2] Starting Jetson TX2"
excerpt: "Introduction to Jetson TX2"
toc: true
toc_sticky: true
categories:
  - Jetson
tag:
  - Jetson TX2
use_math: true
last_modified_at: 2022-01-19T15:00-15:30
---

# Requirement
- **Jetson TX2 development kit**
- Host PC(Ubuntu, Python)
- USB to 5pin cable

# Installation
[공식 Documentation](https://docs.nvidia.com/sdk-manager/install-with-sdkm-jetson/index.html)

## 0. SDK Manager
Jetson TX2(벽돌)에 OS와 기타 개발 도구들을 설치하고자 한다. Jetson Nano와 다르게 SD 카드를 꽂아 부팅하는것이 아니라 Host PC와의 연결을 필요로 한다. Jetson Nano는 SD 카드에 Jetpack 이미지를 올려서 부팅하지만 Jetson TX2의 경우 일반 PC와 유사한 부팅 방식을 가진다. 그래서 첫 OS 설치 방법도 Nano와는 확연히 다르다. Jetson TX2를 시작하기 위해서 Host PC에 [NVIDIA 홈페이지](https://developer.nvidia.com/jetpack-sdk-46)의 SDK manager를 설치해야한다. 설치 방법은 그냥 프로그램 설치하듯이 클릭, 클릭하면 설치된다.

![sdk_manager](/assets/images/starting-jetson-tx2/sdk_manager.jpg)

## 1. STEP 01
제일 상단에 보면 Host PC와 Jetson 모델을 설정할 수 있다. Host PC는 우리의 관심 밖이므로 체크 해제해 준다. 다음으로 Jetson 모델을 TX2로 설정한다. 이때 Host PC와 Jetson TX2가 전원이 켜진 상태에서 USB로 연결되어 있어야 한다.

그 다음으로 Jetpack 버전 등을 설정하고 Step 02로 넘어간다.

![install0](/assets/images/starting-jetson-tx2/install0.png)

## 2. STEP 02
필요에 따라 라이브러리 등을 체크하고 STEP 03으로 넘어간다. 생각하기 귀찮을 때는 다 선택하면 된다. 그리고 하단의 체크 박스를 체크하면 STEP 03으로 넘어갈 수 있다.

![install1](/assets/images/starting-jetson-tx2/install1.png)

## 3. STEP 03-1
우측 하단의 버튼을 클릭해 설치를 진행한다. 어느정도 시간이 지나면 다음과 같은 창이 뜰것이다.

![install2](/assets/images/starting-jetson-tx2/install2.png)

위 명령에 따라서 TX2의 물리 버튼을 조작해 Recovery 모드로 진입해야 한다.

![btn](/assets/images/starting-jetson-tx2/btn.jpg)

좌측에서 두번째 버튼이 선에 가려져 안 보이지만, 총 네 개의 버튼이 있다. 좌측부터 순서대로 RESET, Volume, Recovery, Power 버튼이다.

> 0) 팝업 창 1.번 항목에서 Manual Setup을 선택한다.
>
> 1) Jetson TX2가 파워 잭만 연결되고 전원은 키지 않은 상태를 유지한다. Host PC와 TX2는 당연히 처음부터 USB 케이블로 연결되어 있는 상태일 것이다.
>
> 2) Recovery 버튼을 꾹 누르고 있는다.
> 
>> 2-1. Recovery 버튼을 누르는 동시에 Power 버튼을 한 번 눌렀다 때고,
>>
>> 2-2. Power 버튼 다음에 Reset 버튼을 눌렀다 땐다.
>
> 3) Recovery 버튼을 땐다.

이제 TX2에 설치를 진행할 준비가 끝났다. Host PC에서 터미널을 키고 lsusb 명령을 통해 Host PC에 TX2가 연결된 것을 확인할 수 있을 것이다.

![lsusb](/assets/images/starting-jetson-tx2/lsusb.png)

위와 같이 **NVidia Corp.** 가 떠있다면 잘 따라온 것이다. 이제 Flash 버튼을 클릭하면 자동으로 TX2에 설치가 진행된다.

![install3](/assets/images/starting-jetson-tx2/install3.png)

## 4. STEP 03-2
설치를 진행하는 도중에 다음과 같은 팝업이 뜰 것이다. 동시에 Jetson TX2에 연결된 모니터에 우분투 초기 설정 화면이 뜰 것이다.

![install4](/assets/images/starting-jetson-tx2/install4.png)

여기서 Host PC는 잠깐 멈추고 Jetson TX2에서 우분투 초기 설정을 완료해 주면 된다. **이때 주의할 점은 Jetson TX2의 네트워크를 Host PC와 동일하게 잡아주어야 한다.**

TX2에서 설정을 마치면, 다시 Host PC로 돌아와 팝업 창의 빈 칸을 채워주면 된다. 참고로 TX2의 IP 주소는 TX2의 터미널에서 ifconfig 명령어로 알 수 있다. 아마 192.168.xx.xx 형식일 것이다.

이제 install 버튼을 누르면 다시 설치가 재개된다.

![install5](/assets/images/starting-jetson-tx2/install5.png)
![install6](/assets/images/starting-jetson-tx2/install6.png)

## 5. + $\alpha$
아마 설치를 진행하면서 failed가 뜰 수 있을 것이다.

![failed](/assets/images/starting-jetson-tx2/failed.png)

이런 경우는 당황하지 말고, TX2에서 터미널을 켠 다음
```
$ sudo apt update
$ sudo apt upgrade
```

를 수행하면 된다. 아마 일부 설치가 failed로 끝났다면, SDK manager 우측 아래 버튼에 Retry가 뜰 것이다. TX2에서 패키지 업데이트를 해준 다음 Retry하면 설치가 잘 될 것이다.
