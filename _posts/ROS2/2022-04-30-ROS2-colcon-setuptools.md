---
title: "ROS2 build & python3 setuptools"
excerpt: "Python3 setuptools error when doing colcon build"
toc: true
toc_sticky: true
categories:
  - ROS2
tag:
  - ROS2
  - colcon
  - python
use_math: true
last_modified_at: 2022-04-30T15:00-15:30
---

# ROS2 build
> ROS2는 colcon 빌드 시스템을 사용한다.

다음과 같이 colcon_ws에 임의의 새로운 패키지를 생성하면 패키지 내에 setup.cfg라는 파일이 생성될 것이다.

```bash
$ ros2 pkg create --build-type ament_python --node-name serverRun ros2server
```

![pkg](/assets/images/ros2-setuptools/pkg.png)

setup.cfg를 열어보면 아래와 같이 쓰여있다.
```bash
[develop]
script-dir=$base/lib/ros2server
[install]
install-scripts=$base/lib/ros2server
```

이 상태에서 *colcon build* 를 하면 아래와 같은 에러가 뜰 수 있다.
```bash
Starting >>> ros2server
/home/msjun-ubuntu/.local/lib/python3.8/site-packages/setuptools/dist.py:757: UserWarning: Usage of dash-separated 'script-dir' will not be supported in future versions. Please use the underscore name 'script_dir' instead
  warnings.warn(
/home/msjun-ubuntu/.local/lib/python3.8/site-packages/setuptools/dist.py:757: UserWarning: Usage of dash-separated 'install-scripts' will not be supported in future versions. Please use the underscore name 'install_scripts' instead
  warnings.warn(
--- stderr: ros2server                   
/home/msjun-ubuntu/.local/lib/python3.8/site-packages/setuptools/dist.py:757: UserWarning: Usage of dash-separated 'script-dir' will not be supported in future versions. Please use the underscore name 'script_dir' instead
  warnings.warn(
/home/msjun-ubuntu/.local/lib/python3.8/site-packages/setuptools/dist.py:757: UserWarning: Usage of dash-separated 'install-scripts' will not be supported in future versions. Please use the underscore name 'install_scripts' instead
  warnings.warn(
/home/msjun-ubuntu/.local/lib/python3.8/site-packages/setuptools/command/install.py:34: SetuptoolsDeprecationWarning: setup.py install is deprecated. Use build and pip and other standards-based tools.
  warnings.warn(
---
Finished <<< ros2server [2.28s]

Summary: 1 package finished [2.42s]
  1 package had stderr output: ros2server
```

출력은 stedrr 지만 실제 노드를 실행하는데 문제는 없다. 그렇다면 왜 이런 에러가 뜨는 것인가?

# setuptools
파이썬 라이브러리인 *setuptools* 가 최신 버전이면 위와 같은 에러가 뜰 수 있다. 에러메시지의 내용을 보면 **script-dir**, **install-scripts** 에 **-** (대쉬) 기호가 들어가는데 이는 이후 버전에서 더 이상 지원하지 않을 것이라 한다. 그래서 **_** (언더바) 로 바꾸라고 경고하는 것이다. 그것 외에도 이유를 알수 없는 setuptools 관련 에러가 하나 더 있다. 빌드하고 노드를 실행하는데는 문제없지만 굉장히 불편한 것도 사실이다.

하지만 setuptools의 버전을 낮춰주는 것만으로 위와 같은 에러메시지를 없앨 수 있다.

```bash
# Downgrade setuptools
pip install setuptools==45.2.0
```

위 명령어를 통해 setuptools를 45.2.0 버전으로 낮추면 더 이상 쓸데없는 에러메시지를 볼 필요가 없다.




