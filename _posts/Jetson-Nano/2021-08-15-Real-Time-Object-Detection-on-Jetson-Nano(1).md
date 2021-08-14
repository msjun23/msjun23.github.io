---
title: "Real Time Object Detection on Jetson Nano(1)"
excerpt: "Object Detection with Jetson Nano"
toc: true
toc_sticky: true
categories:
  - Jetson Nano
tag:
  - Jetson Nano
  - Deep Learning
  - Object Detection
last_modified_at: 2021-08-15T01:00-01:30
---

Jetson Nano에서 간단한 Object Detecion 예제를 실행해 보겠다. 다음은 NVIDIA 사이트에 있는 Hello AI World라는 Jetson 프로젝트의 설명이다.
​
**[Hello AI World](https://developer.nvidia.com/embedded/twodaystoademo#hello_ai_world)**
​
-   Get started with deep learning inference for computer vision using pretrained models for image classification and object detection.
-   Realtime acceleration with TensorRT and live camera streaming.
-   Code your own recognition program in C++.
-   For those interested in training their own networks, take the full Two Days to a Demo which includes both training and inference.
​
---
​
### Jetson-Inference
​
먼저 jetson-inference를 설치해야 한다.


<br>1\. git과 cmake가 정상적으로 Jetson 보드에 설치돼 있는지 확인한다.

``` bash
$ sudo apt-get update  
$ sudo apt-get install git cmake  
```

<br>2\. jetson-inference 프로젝트를 설치하고 싶은 디렉터리에 git clone을 해준다.

```bash
$ git clone https://github.com/dusty-nv/jetson-inference
$ cd jetson-inference
$ git submodule update --init
```

마지막에 git submodule update --init 명령 대신 git clone을 수행할 때 --recursive를 동시에 진행해도 좋다.

($ git clone --recursive [https://github.com/dusty-nv/jetson-inference)](https://github.com/dusty-nv/jetson-inference)) 

<br>3\. 파이썬 개발 패키지를 설치한다.

```bash
$ sudo apt-get install libpython3-dev python3-numpy
```

<br>4\. 프로젝트 내에 build 디렉터리를 생성하고 cmake를 실행하여 빌드를 구성한다. cmake가 실행되면 필요한 종속성을 설치하고 DNN모델을 다운로드하는 스크립트가 실행된다.

```bash
$ cd jetson-inference    # omit if working directory is already jetson-inference/ from above
$ mkdir build
$ cd build
$ cmake ../
```

아래 그림과 같은 화면을 터미널에서 확인할 수 있을 것이다.
​
![Real-Time-Object-Detection-on-Jetson-Nano(1)1](/assets/images/Real-Time-Object-Detection-on-Jetson-Nano(1)/Real-Time-Object-Detection-on-Jetson-Nano(1)1.jpg)

위 아래 방향키로 움직이며 Space bar로 다운로드할 DNN 모델을 확인하고, Enter를 눌러 다운로드를 진행할 수 있다. Default로 체크돼있는 모델들만 다운로드하기 위해 추가 선택 없이 바로 OK를 선택하고 진행하도록 한다.

추후에 모델 다운로드 툴을 다시 실행 시키고 싶으면 아래 명령어를 통해 실행할 수 있다.

```bash
$ cd jetson-inference/tools
$ ./download-models.sh
```
​
모델 다운로드를 마치면 아래 그림과 같이 PyTorch를 설치하는 창이 나온다.
​
![Real-Time-Object-Detection-on-Jetson-Nano(1)2](/assets/images/Real-Time-Object-Detection-on-Jetson-Nano(1)/Real-Time-Object-Detection-on-Jetson-Nano(1)2.jpg)

Jetson 보드에서 당장 DNN 모델을 훈련시킬 것이 아니기 때문에 굳이 파이토치를 설치하지 않는다. Tap키를 통해 Quit을 선택하고 Enter를 눌러준다.

마찬가지로 추후에 파이토치 인스톨러를 설치하기 위해선 아래의 명령어를 실행한다.

```bash
$ cd jetson-inference/build
$ ./install-pytorch.sh
```

<br>5\. 모델들의 다운로드가 끝나고 파이토치 설치도 넘어갔다면, 프로젝트를 컴파일하고, 라이브러리 등을 빌드하기 위해 아래의 명령을 실행한다.
​
```bash
$ cd jetson-inference/build          # omit if working directory is already build/ from above
$ make
$ sudo make install
$ sudo ldconfig
```

모든 과정이 끝나면 build 폴더 아래에 다음과 같은 구조를 확인할 수 있을 것이다.
​
```
|-build
   \aarch64
      \bin             where the sample binaries are built to
         \networks     where the network models are stored
         \images       where the test images are stored
      \include         where the headers reside
      \lib             where the libraries are build to
```
​
<br><br>위의 모든 과정은 jetson-inference github에서 보다 자세히 확인할 수 있다.
​
[github.com/dusty-nv/jetson-inference/blob/master/docs/building-repo-2.md](https://github.com/dusty-nv/jetson-inference/blob/master/docs/building-repo-2.md)
