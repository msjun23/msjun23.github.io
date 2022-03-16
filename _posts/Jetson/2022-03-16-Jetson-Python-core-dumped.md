---
title: "[ALL] Jetson에서 Python core dumped 해결"
excerpt: "Solution for core dumped situation when you run python"
toc: true
toc_sticky: true
categories:
  - Jetson
tag:
  - Jetson Nano
  - Jetson TX2
  - Jetson Xavier
  - Jetson
use_math: true
last_modified_at: 2022-03-16T16:30-17:00
---

# Python3 core dumped
Jetson 보드에서 파이썬3을 실행 시 다음과 같이 core dumped 상황이 생겼을 때 해결 방법이 될 수 있다.

![jetson-python-core-dumped](/assets/images/Jetson-Python-core-dumped/core_dumped.png)


아래 명령어를 bashrc에 추가한다.

```bash
$ gedit ~/.bashrc
$ export OPENBLAS_CORETYPE=ARMV8 python3
$ source ~/.bashrc
```
