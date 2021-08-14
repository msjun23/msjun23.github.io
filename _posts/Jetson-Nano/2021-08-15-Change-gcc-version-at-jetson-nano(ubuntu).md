---
title: "Jetson Nano(ubuntu)에서 gcc 버전 변경하기"
excerpt: "Change gcc version at Jetson Nano(Ubuntu)"
toc: true
toc_sticky: true
categories:
  - Jetson Nano
tag:
  - Jetson Nano
  - Ubuntu
  - gcc
last_modified_at: 2021-08-15T01:00-01:30
---

Jetson Nano에는 기본적으로 7.5.0 버전의 gcc, g++가 설치되어있다. 하지만 Ubuntu에서 개발을 진행하다 보면 gcc, g++의 버전을 변경해야 하는 경우가 생긴다. 거의 오픈소스를 활용하는 경우에 이와 같은 상황이 발생할 가능성이 크다. 이럴 때마다 gcc를 제거하고 다른 버전으로 다시 설치하는 것은 매우 번거로운 일이다. 이를 해결하기 위해 여러 gcc, g++버전을 설치해두고 필요에 따라 버전을 변경할 수 있도록 하는 것은 현명한 대안이 될 수 있다.

update-alternatives 명령어를 활용하면 쉽게 gcc 버전을 변경할 수 있다.

나는 VTK를 빌드하는 과정에서 gcc 5 버전이 필요했다.

---

먼저 현재 gcc 버전을 확인하는 방법은 아래와 같다.
```bash
$ gcc --version
```

<br>

다른 gcc 버전을 설치하기 위해 ppa repository를 추가해줘야 한다.
```bash
$ sudo add-apt-repository ppa:ubuntu-toolchain-r/test
$ sudo apt-get update
```

<br>

gcc 및 g++를 설치한다. 나는 5 버전이 필요했으므로 5 버전을 설치했다.
```bash
$ sudo apt-get install gcc-5 g++-5
```

<br>

이제 설치된 여러 버전의 gcc, g++를 확인할 수 있다.
```bash
# gcc 확인
$ sudo dpkg -l|grep gcc|awk '{print $2}'

# g++ 확인
$ sudo dpkg -l|grep g++|awk '{print $2}'
```

<br>

여러 버전의 gcc를 관리하기 위해 alternatives를 확인한다.
```bash
# gcc
$ sudo update-alternatives --display gcc

# g++
$ sudo update-alternatives --display g++
```

<br>

처음에는 설치된 것이 없으므로 아래와 같이 뜰 것이다.

![change-gcc-version-at-jetson-nano(ubuntu)1](/assets/images/change-gcc-version-at-jetson-nano(ubuntu)/change-gcc-version-at-jetson-nano(ubuntu)1.png)

<br>

이제 alternatives에 등록을 해준다.
```bash
$ sudo update-alternatives --install <link> <name> <path> <priority>
```

각 인자가 의미하는 것은 다음과 같다.
![change-gcc-version-at-jetson-nano(ubuntu)2](/assets/images/change-gcc-version-at-jetson-nano(ubuntu)/change-gcc-version-at-jetson-nano(ubuntu)2.png)

```bash
# gcc
$ sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-5 50
$ sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-7 70

# g++
$ sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-5 50
$ sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-7 70
```

<br>

이제 다시 한번 alternatives를 확인해보면 아래와 같이 뜰 것이다.
![change-gcc-version-at-jetson-nano(ubuntu)3](/assets/images/change-gcc-version-at-jetson-nano(ubuntu)/change-gcc-version-at-jetson-nano(ubuntu)3.png)

<br>

이때 priority 뒤의 숫자가 우선순위를 의미한다. 숫자가 큰 버전의 gcc를 auto mode에서 기본 gcc 버전으로 사용한다.

마지막으로 버전을 변경할 때는 아래와 같이 진행하면 된다.
```bash
# gcc
$ sudo update-alternatives --config gcc

# g++
$ sudo update-alternatives --config g++
```
![change-gcc-version-at-jetson-nano(ubuntu)4](/assets/images/change-gcc-version-at-jetson-nano(ubuntu)/change-gcc-version-at-jetson-nano(ubuntu)4.png)

<br>

사용하고자 하는 gcc버전의 번호를 입력하면 선택된 버전의 gcc를 사용할 수 있다. g++도 동일하다.
