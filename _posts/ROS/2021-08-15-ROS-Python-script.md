---
title: "ROS Melodic Python script 생성하기"
excerpt: "ROS에서 Python script 생성시 유의할 점"
toc: true
toc_sticky: true
categories:
  - ROS
tag:
  - ROS
  - Python3
last_modified_at: 2021-08-13T14:00-14:30
---

# ROS Create Package
이전 포스트인 [ROS Create Package](https://msjun23.github.io/ros/ROS-Create-pkg/)에서 패키지 생성하는 법을 기술했다. 패키지 내에서 코드를 작성시 보통 C++, Python 두 언어를 사용할 것이다. 이전 포스트에서는 C++ 파일만을 생성해 보았다. 여기서는 파이썬 파일을 생성해 보겠다.

<br>

# Create Python code
나는 C++ 보다 파이썬의 경우가 더 쉽다고 느껴진다. 한 가지 유의점은 패키지를 생성할 때 의존 패키지에 rospy를 추가해야 하는 것이다.

```bash
$ catkin_create_pkg rospy # + 기타 의존 패키지
```

<br>

이제 패키지 내부에 scripts 폴더를 생성해준다. scripts 폴더 내에서 작성하고자 하는 파이썬 코드를 작성하면 된다. 코드를 작성할 때 꼭 코드 최상단에 다음과 같은 주석을 넣어줘야 한다.

```python
#!/usr/bin/env python
```

[만일 파이썬3를 사용한다면](https://msjun23.github.io/ros/ROS-python3-setting/) 위 주석에 3만 더해주면 된다.

```python
#!/usr/bin/env python3
```

<br>

# chmod 권한 부여
마지막으로 할 일이 있다. C++ 파일을 생성하고 편집할 때와 달리 CMakeLists.txt나 package.xml 파일을 만질 일은 없다. 다만, 파이썬 파일을 생성 후 chmod 명령어를 사용해 권한을 부여해 주어야 한다.

```bash
$ chmod +x 파일이름.py
```
해당 과정이 없으면 catkin_make를 수행해도 rosrun으로 파이썬 파일을 실행할 수 없다.

<br>

권한 부여까지 마쳤으면 catkin_make를 수행한 후 해당 파일을 실행시킬 수 있을 것이다.
