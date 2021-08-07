---
title: "ROS Create Package"
excerpt: "How to create ROS package?"
toc: true
toc_sticky: true
categories:
  - ROS
tag:
  - ROS
  - Package
  - Jetson nano
  - qt creator
last_modified_at: 2021-08-08T05:10-06:00
---

ROS 패키지(Package)를 생성하고 편집하는 것은 qt creator의 ros plugin을 사용하면 아주 편리하다. 실제로 ROS를 설치하고 패키지 관련해서 작업을 할 때 많이들 qt creator로 작업을 한다.

qt creator를 설치할 때 한 가지 주의할 점이 있다. 터미널에 명령어를 통해 qt creator를 설치하게 되면 자동으로 최신 버전(인지 임의의 버전인지 확신은 못하겠지만 아무튼)이 설치되는데, 이 버전에서는 ros workspace를 생성할 수가 없다. 그렇기에 우분투 버전에 따라 ros workspace가 지원이 되는 qt creator를 설치해야 한다.

qt creator를 설치하는 방법은 다음 [링크](https://ros-qtc-plugin.readthedocs.io/en/latest/_source/How-to-Install-Users.html)를 참조하길 바란다. 도움이 되기 바라며 추천 [유튜브 링크](https://www.youtube.com/watch?v=Wes4QUIV6G8&list=PL5rGJe4zWqzc7aOUF8xdA4_Yyu6HQXi_o&index=4)도 걸어두겠다.

---

## **Jetson Nano에서 ROS 패키지 작업**

 나는 일단 PC나 노트북이 아닌 Jetson Nano에서 ROS 작업을 하길 원했다. 하지만 여기서(벌써?) 한 가지 문제점에 부딪히고 말았다. 먼저 위의 링크에서 설명하는 데로 Jetson Nano의 Ubuntu 버전에 맞는 인스톨러를 다운로드하였다. 하지만 실행이 되지 않았다.

![ros-create-pkg1](/assets/images/ros-create-pkg/ros-create-pkg1.png){: .align-center}

invalid encoding 이라는데 대충 구글링을 해보니 32비트 64비트 같은 여하튼 귀찮은 문제인 듯싶었다. 모자란 구글링, 영어 실력을 탓하며 qt creator를 포기해야 했고, 다른 방법을 찾아야 했다.

 다른 방법이랄 것도 없다. 그냥 생으로 ROS 패키지를 만들고 작업을 하기로 했다. 그렇다고 터미널만 가지고 하기에는 너무 불편할 것 같아서 선택한 것이 VSCode이다. VSCode는 따로 말이 필요 없을 정도로 유명하고 많이 쓰이는 에디터이다. VSCode에서 ROS 패키지를 편집해 보겠다.

---

## **0\. 작업 폴더 생성 및 초기화**

 ROS에서는 catkin이라는 ROS 전용 빌드 시스템을 사용한다. 이를 위해 catkin 작업 폴더를 생성하고 초기화해준다.

```
$ mkdir -p ~/catkin_ws/src
$ cd ~/catkin_ws/src
$ catkin_init_workspace
```

이제 작업 공간인 catkin\_ws 폴더와 그 안에 src 폴더, src 안에 CMakeLists.txt가 생겼을 것이다. 여기서 한 번 빌드를 해주면 build, devel, src 세 개의 폴더가 생성된다. 빌드는 아래와 같이 하면 된다.

```
$ cd ~/catkin_ws
$ catkin_make
```

여기서 빌드 관련 파일은 build에 저장되고, 빌드 후 실행 관련 파일은 devel에 저장된다.

마지막으로 bashrc에 빌드 시스템과 관련하여 아래의 줄을 추가해준다.

```
$ source ~/catkin_ws/devel/setup.bash
```

## **1\. 패키지 생성**

 위에서 생성한 작업 공간을 VSCode에서 열어보면 다음과 같을 것이다. 위와 같이 터미널을 사용하지 않고 처음부터 VSCode에서 폴더나 파일을 만들어도 상관은 없다. 참고로 세세한 폴더명이나 위치 등은 알아서 잘 정리하길 바란다. 나는 catkin\_ws2로 작업 공간 이름을 정했고, 위치는 home/Documents/ros/catkin\_ws2로 했다.

![ros-create-pkg1](/assets/images/ros-create-pkg/ros-create-pkg2.png){: .align-center}

이제 src 폴더 내에 패키지를 생성할 것이다. 패키지 생성 명령어는 아래와 같다.

```
$ catkin_create_pkg [패키지이름] [의존패키지1] [의존패키지2]
```

실제로 패키지를 생성해 보겠다.

![ros-create-pkg1](/assets/images/ros-create-pkg/ros-create-pkg3.png){: .align-center}

![ros-create-pkg1](/assets/images/ros-create-pkg/ros-create-pkg4.png){: .align-center}

```
$ catkin_create_pkg ros_tutorial1_pkg std_msgs roscpp
```

성공적으로 src/ros\_tutorial1\_pkg 가 생성되었다. 의존성 패키지로 std\_msgs와 roscpp를 달아주었다. 내부 요소는 각각 다음과 같다.

-   include 폴더
-   소스 코드 폴더
-   CMakeLists.txt : 빌드 설정 파일
-   package.xml : 패키지 설정 파일

## **2\. cpp 파일 생성**

 예제 파일을 생성한다. publisher.cpp, subscriber.cpp 두 개를 생성하겠다. publish, subscribe 등의 개념적인 부분은 여기선 기술하지 않겠다. 간단히만 말하면 publisher가 뿌려주면 subscriber가 받는다. 아래 예제를 보고 확인하자. 두 cpp 파일의 생성 위치는 catkin\_ws/src/ros\_tutorial1\_pkg/src/이다. (catkin\_ws2랑 catkin\_ws랑 왔다 갔다 하는데 그렇게 중요한 건 아니니 그냥 넘어가자..)

![ros-create-pkg1](/assets/images/ros-create-pkg/ros-create-pkg5.png){: .align-center}

![ros-create-pkg1](/assets/images/ros-create-pkg/ros-create-pkg6.png){: .align-center}

![ros-create-pkg1](/assets/images/ros-create-pkg/ros-create-pkg7.png){: .align-center}

cpp 파일을 만들었다고 끝이 아니다. 제대로 끝마치기 위해선 패키지 폴더 안에 있는 CMakeLists.txt를 수정해야 한다.

![ros-create-pkg1](/assets/images/ros-create-pkg/ros-create-pkg8.png){: .align-center}

위와 같이 add\_executable부분과 target\_link부분을 추가해준다.

```
add_executable(ros_tutorial1_publisher src/publisher.cpp)
add_executable(ros_tutorial1_subscriber src/subscriber.cpp)


target_link_libraries(ros_tutorial1_publisher
${catkin_LIBRARIES}
)
target_link_libraries(ros_tutorial1_subscriber
${catkin_LIBRARIES}
)
```

add\_executable에서는 노드의 이름을 설정해주고, 뒤 쪽에는 해당하는 cpp파일을 적어준다. target\_link에도 노드의 이름을 설정해준다. 모든 작업이 끝난 후 반드시 저장을 해준다.

## **3\. 빌드**

 빌드는 catkin\_ws로 돌아가 아래와 같이 명령어를 입력하면 된다.

```
$ catkin_make
```

![ros-create-pkg1](/assets/images/ros-create-pkg/ros-create-pkg9.png){: .align-center}

VSCode 터미널에서 별다른 에러 없이 빌드를 성공했다. qt creator에서 빌드를 진행하면 각 cpp 파일 헤더 부분의 빨간 줄이 사라지고 깔끔해지는데 여기서는 빌드를 해도 빨간 줄이 남아있다. 왜 그런지는 모르겠지만 노드를 실행하는 데는 문제가 없었다.

## **4\. 실행 결과**

![ros-create-pkg1](/assets/images/ros-create-pkg/ros-create-pkg10.png){: .align-center}

 publisher와 subscriber가 잘 작동하는 것을 볼 수 있다.