---
title: "Ubuntu에 CUDA, cuDNN 설치하기"
excerpt: "Ubuntu에 CUDA, cuDNN 설치하기"
toc: true
toc_sticky: true
categories:
  - Blog
tag:
  - Ubuntu
  - CUDA
  - cuDNN
last_modified_at: 2022-08-20T18:30-19:00
---

> 본 포스팅은 Ubuntu 18.04 환경에서 CUDA와 cuDNN을 설치하는 방법에 대한 내용입니다. 본인의 OS와 필요한 CUDA, cuDNN 버전을 확인하시어 알맞은 설치를 진행하시길 바랍니다.

# Version check
본인의 GPU, CUDA를 설치하는 목적(향후 CUDA 버전에 맞춰 설치하게될 라이브러리, 사용하고자 하는 어플리케이션, 플랫폼 등과의 호환성 등)을 다방면으로 고려해서 설치하길 바란다. 특히 Ubuntu를 거의 처음 다뤄보는 사람이라면 옆에 항상 Ubuntu 설치 USB를 구비하고 작업을 진행하길 바란다. 알 수 없는 문제가 발생했을 때는 그냥 OS부터 다시 설치하는게 맘 편하다.

본 포스팅에서는 .deb 파일을 받아 설치를 진행할 것이다. 이 방법으로 CUDA를 설치하면 이미 설치되어있는 그래픽 드라이버를 삭제하고 CUDA와 어울리는 그래픽 드라이버 버전으로 재설치가 된다. 그렇기에 사실 기존에 설치되어 있던 그래픽 드라이버는 그리 중요하지 않지만, 아래 링크에서 그래픽 드라이버와 CUDA의 매칭되는 버전을 확인하는 것을 추천한다.

[그래픽 드라이버 버전에 맞는 CUDA 버전 확인하기](https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html)

![tab1](/assets/images/CUDA-install/table1.JPG)

# CUDA 설치하기
NVIDIA의 [CUDA Toolkit Archive](https://developer.nvidia.com/cuda-toolkit-archive)에서 설치하고자 하는 CUDA 버전을 받아온다.

나는 개인적으로 CUDA 10.2 버전이 가장 안전한 것 같다. 19~20년도쯤에 나온 버전이지만 해당 버전으로 아직까지 크리티컬한 호환성 이슈는 없었고, Pytorch 최신 버전도 아직까지 CUDA 10.2를 지원한다. 가장 무난한 버전이라고 생각하고, 본인이 어떤 CUDA 버전이 필요한지 모르겠다면, 일단 10.2를 추천한다.

설치하고자 하는 버전의 링크를 타고 들어가면 다음 그림과 같이 본인의 OS, 아키택처 등을 선택하는 옵션이 보일 것이다. 여기서 본인에 맞게 클릭클릭 해주면 하단에 설치 명령어를 보여준다.

![cuda1](/assets/images/CUDA-install/cuda1.JPG)

![cuda2](/assets/images/CUDA-install/cuda2.JPG)

**Base Installer** 에 있는 명령어를 한 줄씩 따라서 수행하면 CUDA 설치를 정상적으로 마칠 수 있을 것이다.

그리고 그 아래 추가적인 **Patch** 내역이 있으면 해당 파일도 받아 설치하길 권한다. Download 버튼을 누르면 *cuda-repo-OS버전-CUDA버전-아키택처.deb* 같은 파일이 하나 받아질 것이다. 그 파일이 존재하는 위치에서 터미널을 키고 다음 명령어를 통해 설치하면 된다.

```bash
# 본인이 받은 파일명으로 바꿔서 실행!!!!!
$ sudo dpkg -i cuda-repo-ubuntu1804-10-2-local_10.2.2-1_amd64.deb
```

# cuDNN 설치하기
cuDNN을 설치하기 위해서는 NVIDIA 계정이 있어야 한다. 로그인을 한 후, [cuDNN Archive](https://developer.nvidia.com/rdp/cudnn-archive)에서 본인 CUDA 버전과 필요에 맞는 cuDNN 버전을 찾는다.

나는 CUDA 10.2 버전을 설치했으므로 CUDA 10.2에 맞는 cuDNN 8.2.1을 다운 받을 것이다.

![cudnn1](/assets/images/CUDA-install/cudnn1.JPG)

**Library for Windows and Linux, Ubuntu(x86_64)** 타이틀 아래 **cuDNN Library for Linux (x86)** 를 받아준다. 그럼 압축 파일 하나가 받아질 것이다. 먼저 압축을 풀어준다.

```bash
$ tar -xzvf cudnn-10.2-linux-x64-v8.2.1.32.tgz
```

그럼 cuda 폴더가 생길텐데 이 폴더 안에 있는 파일들을 아까 설치한 CUDA 설치 폴더 안에 복사해주면 된다.
```bash
$ cd cuda
$ sudo cp include/cudnn* /usr/local/cuda-10.2/include
$ sudo cp lib64/libcudnn* /usr/local/cuda-10.2/lib64/
$ sudo chmod a+r /usr/local/cuda-10.2/lib64/libcudnn*

# cuDNN 설치 확인하기
$ cat /usr/local/cuda/include/cudnn_version.h | grep CUDNN_MAJOR -A 2
```

# 설치 확인하기
CUDA, cuDNN까지 설치가 완료됐다면 안전하게 한 번 재부팅을 권한다. 재부팅 후 다음 명령어를 통해 CUDA가 제대로 설치됐음을 확인할 수 있다.

```bash
# nvcc를 통해 확인하기. 둘 다 똑같은 명령어
$ nvcc -V
$ nvcc --version

# nvidia-smi를 통해 확인하기
$ nvidia-smi
```

![cuda3](/assets/images/CUDA-install/cuda3.png)

