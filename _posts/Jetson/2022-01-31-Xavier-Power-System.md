---
title: "[Xavier] About Jetson AGX Xavier Power mode"
excerpt: "Change power mode and fan mode at Xavier"
toc: true
toc_sticky: true
categories:
  - Jetson
tag:
  - Jetson Xavier
use_math: true
last_modified_at: 2022-01-31T03:00-03:30
---

# Power Mode
Jetson AGX Xavier 뿐 아니라 Jetson board 들은 Power mode 를 바꿀 수 있다. 화면 우측 상단을 보면 아래와 같이 Power 모드를 변경 가능하다.

![power_mode](/assets/images/Xavier-Power-System/power_mode.png)

설치과정에서 파워 모드를 임의로 변경하지 않았다면 처음 Xavier를 부팅했을 때는 **7:MODE 15W DESKTOP**으로 선택되어 있을 것이다. 나는 가장 고성능으로 최적화 해주는 MAXN 모드로 변경해 주었다.

이렇게 UI를 통해 변경할 수 있고, 터미널을 통해서도 가능하다.

먼저 터미널을 통해 현재의 파워 모드를 확인할 수 있다.

```
$ sudo nvpmodel -q
```

![mode0](/assets/images/Xavier-Power-System/mode0.png)

다음으로 파워 모드를 변경하는 것은 아래 명령어를 통해 가능하다. **-m** 뒤에 원하는 파워 모드 번호를 명시하면 된다.

```
$ sudo nvpmodel -m 7
```
![mode7](/assets/images/Xavier-Power-System/mode7.png)

# Fan Mode
Jetson AGX Xavier의 팬 모드 또한 변경할 수 있다. 팬 모드에는 **quiet** 와 **cool** 모드 두 가지가 있다. 먼저 quiet모드는 일정 온도까지는 팬이 돌지 않다가 임계점을 넘어서면 팬이 돌기 시작한다. 다음으로 cool모드는 항시 팬이 돌며 온도를 낮게 유지해준다.

처음 Xavier를 부팅했다면, default로 quiet 모드가 설정되어있어 팬이 돌지 않을 것이다. 팬이 항시 돌기를 원한다면 팬 모드를 바꿔야 한다.

아래 두 명령어 중 원하는 모드를 입력하여 팬 모드를 설정할 수 있다.

```
$ sudo nvpmodel -d cool       # cool mode
$ sudo nvpmodel -d quiet      # quiet mode
```

추가로 파워 모드나 팬 모드를 변경 후에는 안정적으로 reboot 하는 것을 추천한다.
