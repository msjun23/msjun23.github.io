---
title: "ROS Melodic Python3 Setting"
excerpt: "How can I use python3 at ROS Melodic?"
toc: true
toc_sticky: true
categories:
  - ROS
tag:
  - ROS
  - Python3
last_modified_at: 2021-08-13T15:30-16:00
---

ROS Melodic은 기본적으로 Python2를 지원한다. 하지만 나와 이 글을 보고있는 여러분은 ROS Melodic에서 Python3를 사용하고 싶다. 방법은 아래와 같다.

```bash
$ sudo apt install python3-pip python3-all-dev python3-rospkg
$ sudo apt install ros-melodic-desktop-full --fix-missing
```

위 줄은 필요한 Python3 패키지들을 다운받는 것이다. 그리고 해당 과정에서 왜인지는 모르겠지만 일부 ROS 패키지들이 삭제되는 것 같았다. Python3 패키지를 설치하고 바로 catkin_make를 실행하면 아마 에러가 뜰 것이다.

이와 같은 상황을 막기 위해 두 번째 명령어를 통해 ROS 패키지를 재설치 해준다.

ROS에서 Python3를 사용하기 위한 준비는 끝이다. 마지막으로 Python3 스크립트 최상단에 다음 주석 한 줄만 추가해 주면 된다. 보면 알겠지만 Python2 스크립트를 사용하기 위한 주석 끝에 3만 붙여주면 된다.

```python
#!/usr/bin/env python3
```
<br><br><br>
+) ~~**혹시나** Python2와 Python3를 동시에 사용할 수 있는지 의문이 들 수 있다. 일단 개인적인 경험으로는 동시에 사용이 가능한 듯 하다. 하지만 나도 배워가는 입장으로써 확실하지는 않다.~~ ROS의 장점 중 하나가 바로 다양한 언어 사용이 가능하다는 것 이다. C++로 짠 코드에서 publish한 topic을 python 코드에서 subscribe하여 사용할 수 있듯이 python2와 python3도 각기 동시에 사용 가능하다. 다만 스크립트 상단의 주석에서 어떤 파이썬 버전을 사용할지 명시하는 것을 유의해야한다.

