---
title: "[ALL] Essential tools for Jetson series"
excerpt: "When you start Jetson series..."
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
last_modified_at: 2022-02-05T23:00-23:30
---

# Thanks for JetsonHacks...

# Install VSCode
[Origin link](https://github.com/JetsonHacksNano/installVSCode)

```
$ git clone https://github.com/JetsonHacksNano/installVSCode.git
$ ./installVSCode.sh

$ code              # Execute
```

# Make NVMe SSD a home directory (For AGX Xavier Developer kit)
[Origin link](https://github.com/jetsonhacks/rootOnNVMe)

먼저 디스크 포맷이 필요하다.
> Disk 프로그램 실행
>
> SSD 포맷
>
> 파티션 할당(용량 전부 사용)
>
> 실행 버튼 눌러서 마운트

```
$ git clone https://github.com/jetsonhacks/rootOnNVMe.git
$ cd rootOnNVMe
$ ./copy-rootfs-ssd.sh
$ ./setup-service.sh
```

마지막으로 
```
$ reboot
```
명령어를 통해 재부팅하면 SSD가 홈 디렉토리로 설정된 것을 확인할 수 있다.

