---
title: "[Docker] Ubuntu에서 docker 설치하기"
excerpt: "How to use docker?"
toc: true
toc_sticky: true
categories:
  - Blog
tag:
  - Ubuntu
  - Docker
  - Container
last_modified_at: 2022-12-11T11:30-12:00
---

# Why Docker?
개발을 하다보면 다양항 OS, 라이브러리, 의존성 패키지들이 복잡하게 얽힐 수 있다. 간단한 작업이라면 모르겠지만, 점차 작업의 범위가 넓어지고 다루는 프로젝트가 많아지다보면, 일일이 현재 프로젝트에 맞춰 환경 세팅을 하는 것이 불가능에 가까워진다. 매 프로젝트마다 OS를 옮겨다니며 그래픽 드라이버를 설치하고 그에 맞는 CUDA, CuDNN 설치, 기타 라이브러리 버전 매칭까지 오히려 프로젝트 하나마다 컴퓨터를 새로 하나씩 사고싶어진다.

이런 상황에서 사용하고자 하는 것이 **도커(Docker)** 다. 도커는 기존의 VM(가상 머신, Virtual Machine)과 다르게 하나의 OS위에서 주어진 자원을 공유하며 컨테이너(Container) 단위로 작업 공간을 다룰 수 있게해준다. VM과 같이 새로운 OS를 부팅하는 것이 아니기 때문에 속도도 빠르고, 자원도 더 효율적으로 이용할 수 있다.

# Docker Installation
도커를 사용하기 위해선 **Docker Engine** 을 설치해야 한다. 도커의 경우 [docs](https://docs.docker.com/engine/install/ubuntu/)가 매우 잘 정리돼있어, [docs](https://docs.docker.com/engine/install/ubuntu/)만 따라가도 설치는 어렵지않게 마칠 수 있다. 도커를 설치하기 위한 몇 가지 방법이 존재하지만 가장 기본적인 방법인 repository를 이용하여 설치하도록 하겠다. 아래 명령어 들은 [2022.12.11] 기준으로 docs에서 복사한 내용이며, 향후 GPG key 이슈 등이 존재하거나 모종의 이유로 설치가 안돼면 직접 [docs](https://docs.docker.com/engine/install/ubuntu/)를 읽어보며 설치하는 것을 추천한다.

## 1. *apt* update & install pkgs
```bash
$ sudo apt-get update

$ sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```

## 2. Add official GPG key
```bash
$ sudo mkdir -p /etc/apt/keyrings

$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

## 3. Set up the repository
```bash
$ echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

## 4. Install Docker
```bash
# Update apt
$ sudo apt update

$ sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

위 명령어까지 문제없이 따라왔다면, 성공적으로 도커를 설치한 것이다. 아래 명령어를 통해 현재 도커 버전을 확인함과 동시에 제대로 설치되었음을 알 수 있다.

```bash
$ sudo docker version
```

![dowtodocker](/assets/images/how-to-docker/docker_version.png)


# Docker run
Hello world는 국룰이다. 아래와 같이 두 가지 튜토리얼 이미지 파일을 실행시켜 보았다. 처음에는 이미지 파일이 존재하지 않기 때문에 자동적으로 [docker hub](https://hub.docker.com/)에서 받아와 실행할 것이다. 한 번 파일을 받은 이후부터는 local에 저장된 파일을 사용하기 때문에 따로 허브에서 불러올 필요가 없어진다.

```bash
$ sudo docker run hello-world
```
![helloworld](/assets/images/how-to-docker/docker_helloworld.png)

```bash
$ sudo docker run docker/whalesay cowsay Hello-World!
```
![whalesay](/assets/images/how-to-docker/docker_whalesay.png)
