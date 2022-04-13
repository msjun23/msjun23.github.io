---
title: "[ALL] Jetson Xavier에서 YOLOv5 사용하기"
excerpt: "YOLOv5 with Jetson AGX Xavier"
toc: true
toc_sticky: true
categories:
  - Jetson
tag:
  - Jetson Xavier
  - Jetson
  - YOLOv5
  - Deep Learning
use_math: true
last_modified_at: 2022-04-05T17:30-18:00
---

# YOLOv5 & Jetson AGX Xavier
[Yolov5](https://github.com/ultralytics/yolov5)는 오리지널 레포지토리에 잘 정리되어있어 사용하는데 큰 어려움이 없었다. 다만 Jetson 보드에서 사용할 경우 파이썬 버전 이슈 때문에 문제가 발생할 수 있을 것 같다. Yolov5는 기본적으로 파이썬 3.7.0 이상의 버전을 필요로한다.

하지만 아직 모든 Jetson 보드(Nano, TX2, AGX Xavier)는 기본적으로 파이썬 3.6.9 기반으로 동작한다. 그리고 Yolov5를 사용하기 위해 Jetson AGX Xavier의 파이썬 버전을 강제로 3.7.0으로 업그레이드 하였더니 자잘한 이슈가 생겼었다. (~~순전히 필자의 잘못이었을지도~~)

그래서 일단 파이썬 버전을 건들지 않고 Yolov5를 사용하기로 했다. 결론적으로 3.6.9에서 Yolov5를 사용할 수 있다.

# Python dependencies
일단 파이썬 관련 패키지 및 라이브러리들을 설치해야 한다.

```bash
$ sudo apt update
$ sudo apt upgrade -y
```

```bash
$ sudo apt install -y python3-pip python3-venv python3-dev libpython3-dev
$ sudo apt install -y libopenblas-base
$ sudo apt install -y gfortran libopenmpi-dev liblapack-dev libatlas-base-dev
```

```bash
$ python3 -m pip install -U pip protobuf numpy pandas scipy sklearn scikit-image
$ python3 -m pip install matplotlib==3.3.4
```

matplotlib의 경우는 파이썬 3.6.9에서 지원하는 최신 라이브러리가 3.3.4이기 때문에 해당 버전으로 설치한다.

# Pytorch Installation
이제 Pytorch를 설치해야 한다. 이때 주의할 점은 일반적인 방법으로 설치하면 안된다. [Nvidia 포럼](https://forums.developer.nvidia.com/t/pytorch-for-jetson-version-1-10-now-available/72048)에서 제공하는 방법으로 설치해야 한다.

## Pytorch v1.8.0 Install
현재 Jetpack 버전과 Yolov5에서 요구하는 pytorch 버전을 고려하여 1.8.0버전을 설치했다.
```bash
$ wget https://nvidia.box.com/shared/static/p57jwntv436lfrd78inwl7iml6p13fzh.whl -O torch-1.8.0-cp36-cp36m-linux_aarch64.whl
$ sudo apt-get install python3-pip libopenblas-base libopenmpi-dev 
$ pip3 install Cython
$ pip3 install numpy torch-1.8.0-cp36-cp36m-linux_aarch64.whl
```

torchvision도 같이 설치해준다. 이때 본인이 설치한 pytorch 버전에 맞추어 torchvision을 설치해야 한다.
```bash
$ sudo apt-get install libjpeg-dev zlib1g-dev libpython3-dev libavcodec-dev libavformat-dev libswscale-dev
$ git clone --branch <version> https://github.com/pytorch/vision torchvision   # see below for version of torchvision to download
$ cd torchvision
$ export BUILD_VERSION=0.x.0  # where 0.x.0 is the torchvision version  
$ python3 setup.py install --user
$ cd ../  # attempting to load torchvision from build dir will result in import error
$ pip install 'pillow<7' # always needed for Python 2.7, not needed torchvision v0.5.0+ with Python 3.6
```

> PyTorch v1.0 - torchvision v0.2.2
>
> PyTorch v1.1 - torchvision v0.3.0
>
> PyTorch v1.2 - torchvision v0.4.0
>
> PyTorch v1.3 - torchvision v0.4.2
>
> PyTorch v1.4 - torchvision v0.5.0
>
> PyTorch v1.5 - torchvision v0.6.0
>
> PyTorch v1.6 - torchvision v0.7.0
>
> PyTorch v1.7 - torchvision v0.8.1
>
> PyTorch v1.8 - torchvision v0.9.0
>
> PyTorch v1.9 - torchvision v0.10.0
>
> PyTorch v1.10 - torchvision v0.11.1

터미널에서 파이썬 인터프리터를 실행하고 다음과 같이 입력했을 때 문제없이 결과가 출력된다면 pytorch 설치를 성공한 것이다.

```bash
$ python3

>>> import torch
>>> print(torch.__version__)
>>> print('CUDA available: ' + str(torch.cuda.is_available()))
>>> print('cuDNN version: ' + str(torch.backends.cudnn.version()))
>>> a = torch.cuda.FloatTensor(2).zero_()
>>> print('Tensor a = ' + str(a))
>>> b = torch.randn(2).cuda()
>>> print('Tensor b = ' + str(b))
>>> c = a + b
>>> print('Tensor c = ' + str(c))

>>> import torchvision
>>> print(torchvision.__version__)
```

# Test YOLOv5
Yolov5를 실행할 준비를 마쳤다. 이제 공식 github에서 Yolov5 패키지를 받아온다.

```bash
$ git clone https://github.com/ultralytics/yolov5.git
$ cd yolov5/
```

Yolov5 테스트는 다음 코드로 쉽게 해 볼 수 있다. 다음에 있는 입력 소스 중 원하는 입력 소스를 사용하면 된다. 추론 결과는 **yolov5/runs/detect/exp~/** 아래에 저장된다.
```bash
python3 detect.py --source 0  # webcam
                          img.jpg  # image
                          vid.mp4  # video
                          path/  # directory
                          path/*.jpg  # glob
                          'https://youtu.be/Zgi9g1ksQHc'  # YouTube
                          'rtsp://example.com/media.mp4'  # RTSP, RTMP, HTTP stream
```

# Usage of YOLOv5
## Train
자신만의 데이터(custom data)를 가지고 Yolov5 모델을 학습시킬 수 있다. 데이터셋을 준비하는 과정은 다음 [링크](https://github.com/ultralytics/yolov5/wiki/Train-Custom-Data)에서 확인 가능하다.

데이터셋을 준비했으면 다음 명령어를 통해 학습을 수행할 수 있다.
```bash
$ python3 train.py --data ${custom_data}.yaml --weights yolov5s.pt
```

다음과 같이 이미지 크기나 batch size, epochs 등을 조절할 수도 있다.
```bash
# Train YOLOv5s on COCO128 for 3 epochs
$ python3 train.py --img 640 --batch 16 --epochs 3 --data coco128.yaml --weights yolov5s.pt
```

학습된 결과는 **yolov5/runs/train/exp~/weights/** 에 저장된다. 해당 폴더 내부의 best.pt가 가장 성능이 좋았던 순간의 weight 파일이고, last.pt가 마지막 학습 결과이다.

## Detect
다음과 같이 학습한 결과인 weight를 사용해 결과를 확인할 수 있다. weight 파일명은 임의로 설정한다.
```bash
$ python3 detect.py --source ${} --weights yolov5s.pt                 # PyTorch
                                           yolov5s.torchscript        # TorchScript
                                           yolov5s.onnx               # ONNX Runtime or OpenCV DNN with --dnn
                                           yolov5s.xml                # OpenVINO
                                           yolov5s.engine             # TensorRT
                                           yolov5s.mlmodel            # CoreML (MacOS-only)
                                           yolov5s_saved_model        # TensorFlow SavedModel
                                           yolov5s.pb                 # TensorFlow GraphDef
                                           yolov5s.tflite             # TensorFlow Lite
                                           yolov5s_edgetpu.tflite     # TensorFlow Edge TPU
```

# YOLOv5 & TensorRT
Jetson AGX Xavier의 기본 성능도 충분히 좋지만 보다 더 높은 성능을 끌어내기 위해 TensorRT를 사용해 모델을 최적화할 수 있다. Yolov5는 TensorRT를 비롯해 다른 최적화 모델들로의 변환을 보다 쉬운 방법으로 제공한다. 제공하는 최적화 엔진이나 모델들에 대한 리스트는 아래 링크에서 확인할 수 있다.

[TFLite, ONNX, CoreML, TensorRT Export](https://github.com/ultralytics/yolov5/issues/251)

마지막 인자인 --device는 gpu를 사용해 작업을 수행하겠다는 의미이다.

```bash
python3 export.py --weights yolov5s.pt --include engine --device 0
```

위 스크립트를 실행하면 원하는 새로운 weight 파일을 얻을 수 있다. 사용하는 방법은 .pt 형식의 weight를 사용하는 것과 동일하다.
```bash
python3 detect.py --weights yolov5s.pt                 # PyTorch
                            yolov5s.torchscript        # TorchScript
                            yolov5s.onnx               # ONNX Runtime or OpenCV DNN with --dnn
                            yolov5s.xml                # OpenVINO
                            yolov5s.engine             # TensorRT
                            yolov5s.mlmodel            # CoreML (MacOS only)
                            yolov5s_saved_model        # TensorFlow SavedModel
                            yolov5s.pb                 # TensorFlow GraphDef
                            yolov5s.tflite             # TensorFlow Lite
                            yolov5s_edgetpu.tflite     # TensorFlow Edge TPU
```
