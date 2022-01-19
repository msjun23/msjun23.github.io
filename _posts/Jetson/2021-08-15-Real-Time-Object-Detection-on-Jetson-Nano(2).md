---
title: "[Nano] Real Time Object Detection on Jetson Nano(2)"
excerpt: "Object Detection with Jetson Nano"
toc: true
toc_sticky: true
categories:
  - Jetson
tag:
  - Jetson Nano
  - Deep Learning
  - Object Detection
last_modified_at: 2021-08-15T01:00-01:30
---

직전 포스트에서 jetson-inference의 설치를 완료했다.

```
|-build
   \aarch64
      \bin             where the sample binaries are built to
         \networks     where the network models are stored
         \images       where the test images are stored
      \include         where the headers reside
      \lib             where the libraries are build to
```

jetson-inference/build/aarch64/bin 아래에서 많은 예제 파일들을 확인할 수 있을 것이다. 그중 DetectNet을 이용한 Object Detction 예제를 실행해 보겠다. 해당 위치에서 터미널을 실행하고 아래의 명령어를 통해 예제를 확인할 수 있다.

```bash
# Python
$ ./detectnet.py --network=ssd-mobilenet-v2 images/peds_0.jpg output.jpg  # --network flag is optional
```

![Real-Time-Object-Detection-on-Jetson-Nano(2)1](/assets/images/Real-Time-Object-Detection-on-Jetson-Nano(2)/Real-Time-Object-Detection-on-Jetson-Nano(2)1.png)

```bash
# Python
$ ./detectnet.py images/peds_1.jpg output_1.jpg
```

![Real-Time-Object-Detection-on-Jetson-Nano(2)2](/assets/images/Real-Time-Object-Detection-on-Jetson-Nano(2)/Real-Time-Object-Detection-on-Jetson-Nano(2)2.png)

저장된 영상으로도 확인 가능하다.

```bash
# Python
./detectnet.py /usr/share/visionworks/sources/data/pedestrians.mp4 pedestrians_ssd.mp4
```

![Real-Time-Object-Detection-on-Jetson-Nano(2)3](/assets/images/Real-Time-Object-Detection-on-Jetson-Nano(2)/Real-Time-Object-Detection-on-Jetson-Nano(2)3.jpg)

---

다음으로 Jetson 보드에 연결된 카메라를 통해 입력된 영상에서 Object Detection을 수행해보겠다.

예제 코드는 jetson-inference/python/examples/my-detection.py 에서 확인 가능하다. 내용은 아래와 같다.

```python
import jetson.inference
import jetson.utils
 
net = jetson.inference.detectNet("ssd-mobilenet-v2", threshold=0.5)
camera = jetson.utils.videoSource("csi://0")      # '/dev/video0' for V4L2
display = jetson.utils.videoOutput("display://0") # 'my_video.mp4' for file
 
while display.IsStreaming():
  img = camera.Capture()
  detections = net.Detect(img)
  display.Render(img)
  display.SetStatus("Object Detection | Network {:.0f} FPS".format(net.GetNetworkFPS()))
```

한 가지 유의할 점은 Jetson 보드에 연결된 카메라이다. Jetson 보드는 라즈베리 파이 카메라 같은 모듈과 USB 케이블로 연결 가능한 웹캠 모두 연결할 수 있다. 카메라 모듈을 연결한 경우 camera 설정을 다음과 같이 한다.

```python
camera = jetson.utils.videoSource("csi://0")
```

웹캠과 같이 USB 케이블로 연결한 경우는 다음과 같이 설정한다.

```python
camera = jetson.utils.videoSource('/dev/video0')
```

<br><br>입력 영상에서 Object Detection을 진행하는 내용은 아래 github 링크에서 보다 자세히 확인 가능하다.

[github.com/dusty-nv/jetson-inference/blob/master/docs/detectnet-example-2.md](https://github.com/dusty-nv/jetson-inference/blob/master/docs/detectnet-example-2.md)

<br><br>Real-Time Object Detection on Jetson Nano(1, 2)에서 작성한 모든 내용은 NVIDIA Developer공식 유튜브에서 확인 가능하다.

[www.youtube.com/watch?v=bcM5AQSAzUY](https://www.youtube.com/watch?v=bcM5AQSAzUY)
