---
title: "ROS & Web page"
excerpt: "Check ROS topic on web page"
toc: true
toc_sticky: true
categories:
  - ROS
tag:
  - ROS
  - rosbridge
  - webserver
  - flask
use_math: true
last_modified_at: 2022-04-16T03:00-03:30
---

# CAUTION
나는 웹과 서버, 프론트/백앤드에 전혀 지식이 없습니다. 이론적으로 틀린 내용이 있어도 적당히 필터링 해주시기 바랍니다.

# ROS & Web
ROS는 단일 네트워크 환경으로 많이 쓴다. 하지만 rosbridge라는 것을 활용하여 web을 통한 원격 통신 또한 가능하다. 본 페이지에서는 flask를 사용해 웹 페이지를 생성하고, 이 웹 페이지에 ROS에서 publish하는 topic들을 확인할 것이다. 이 페이지의 마지막에서는, 서울에서 publish하는 topic을 부산에서 웹 페이지를 통해 확인할 수 있을 것이다.

# Flask
먼저 웹 페이지를 생성할 Flask를 설치한다. Flask는 파이썬 웹 프레임워크(Python-web-framework)로, 파이썬 문법을 통해 간단한 웹 페이지를 생성할 수 있다. Flask 외에도 Django 같은 프레임워크도 있고, 이를 가장 많이 쓰는 것 같다. 이에 비해 Flask는 다소 기능은 적지만 간단하고 가볍고 직관적이라고 한다. ~~사실 파이썬 웹 프레임워크는 관심 영역 밖이고 나도 이번 기회에 처음 써보는 거라 대충 넘어간다.~~ 아무튼 flask를 사용해 페이지를 생성해 보겠다.

```bash
$ python3 -m pip install flask      # pip3 install flask
```

위와 같이 간단한 설치를 마치고 다음 파이썬 파일을 생성한다. 보통 이름은 app.py로 하는 것 같다. 워크스페이스는 다음과 같이 만든다.
```bash
└── Flask
  ├── static
  ├  └──
  ├── templates
  ├  └── subscriber.html
  └── app.py
``` 

```python
from flask import Flask, render_template, request

app = Flask(__name__)

@app.route('/') # main url
def home():
    return 'Hello, World!'

host = '0.0.0.0'
port = '9900'

if __name__ == '__main__':
    app.run(host=host, port=port, debug=True)
    
```

Local에서만 값을 확인할거면 애초에 웹 페이지를 만들지도 않았다. 즉, 외부 네트워크에서도 우리가 만든 웹 페이지에 접속할 수 있어야 한다. 이를 위해 ```host = '0.0.0.0'``` 를 추가했다. app.run() 함수를 실행할 때 **host** 인자로 '0.0.0.0'을 주면 외부 네트워크의 접속을 허용한다는 의미이다. 다음으로 **port** 인지는 이름 그대로 접속 포트를 정하는 것이다. 처음에는 '5000', '8080' 등 해봤는데 얘네는 뭔가 역할이 있는 포트인가보다. 외부 네트워크에서 접속이 안됐다. ~~뭐 되면 그냥 해도 되지 않을까 싶다.~~ 그래서 적당히 구글링하다가 '9900'이라는 임의의 포트 번호를 선정했다.

이제 파이썬 파일을 실행한다.
```bash
$ python3 app.py
```
![app_py](/assets/images/ros-web/app_py.PNG)

위 사진처럼 메시지가 뜨고, 생성된 웹 페이지의 주소를 확인할 수 있을 것이다. 주소 형식은 **xxx.xx.xx.xxx/port** 형식일 것이다. 이 주소를 그대로 인터넷 주소창에 적기만 하면 웹 페이지에 접속할 수 있다. 그럼 어디서든 Hello, World! 가 떠있는 화면을 볼 수 있을 것이다.

이제 살짝만 바꿔 ROS 토픽을 띄우는 웹 페이지로 만들어보겠다. 먼저 위 파이썬 코드를 살짝만 수정한다.
```python
from flask import Flask, render_template, request

app = Flask(__name__)

@app.route('/') # main url
def index():
  return render_template("subscriber.html")

host = '0.0.0.0'
port = '9900'

if __name__ == '__main__':
    app.run(host=host, port=port, debug=True)
```

Hello, World! 를 지우고 subscriber.html 파일로 대체했다. 이제 저 subsrciber.html 을 만들면 된다.

# rosbridge
아 맞다. rosbridge를 먼저 설치해준다. 뭐 언제하나 상관은 없다.

```bash
$ sudo apt-get install ros-melodic-rosbridge-server
```

# roslibjs
웹 페이지를 만들고 ROS랑 상호작용하고 다 roslibjs를 통해 이뤄진다고 한다. roslibjs를 쓰기 위해서 npm을 설치해야 한다. npm은 다음 명령어로 설치할 수 있다.

```bash
$ sudo apt-get install npm
```

아마 바로 설치가 안될 수도 있다. 그렇다고 바로 구글링하러가지 않기를 바란다... npm을 설치하는 방법이야 잘 나와있지만 경험상 그렇게 하면 npm을 설치할 때 ROS 패키지가 다 날아가고, 또 ROS를 다시 설치하면 npm 관련이 다 날아간다. npm 설치가 바로 안되면 다음 방법을 통해 설치할 수 있다.

```bash
$ sudo apt-get install curl software-properties-common
$ curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
$ sudo apt-get install nodejs
```

npm을 설치하는 명령어는 없어보이지만 저래하면 암튼 npm이 깔린다. 터미널에서 ```npm -v```을 통해 확인할 수 있다. 정상적으로 버전이 뜨면 된 것이다. 아 그리고 이 방법을 쓰면 이제 ```sudo apt-get install npm``` 으로는 npm 설치가 불가능한 듯 하다. broken package 어쩌고 뜨는데... 꼬우면 우분투 밀면 된다. 암튼 이제 roslibjs을 사용할 수 있다.

토픽 publish, subscribe, 서비스 까지 한 번에 확인할 수 있다. 우리가 할 것은 ROS 네트워크의 토픽을 subscribe해서 웹 페이지에 띄우는 것이므로 필요없는 부분은 날리겠다. 전체 코드와 자세한 설명은 [원본 링크](http://wiki.ros.org/roslibjs/Tutorials/BasicRosFunctionality)에 있다.

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8" />

<script type="text/javascript" src="http://static.robotwebtools.org/EventEmitter2/current/eventemitter2.min.js"></script>
<script type="text/javascript" src="http://static.robotwebtools.org/roslibjs/current/roslib.min.js"></script>

<script type="text/javascript" type="text/javascript">
  // Connecting to ROS
  // -----------------

  var ros = new ROSLIB.Ros({
    url : 'ws://localhost:9090'
  });

  ros.on('connection', function() {
    console.log('Connected to websocket server.');
  });

  ros.on('error', function(error) {
    console.log('Error connecting to websocket server: ', error);
  });

  ros.on('close', function() {
    console.log('Connection to websocket server closed.');
  });

  // Subscribing to a Topic
  // ----------------------

  var listener = new ROSLIB.Topic({
    ros : ros,
    name : '/listener',
    messageType : 'std_msgs/String'
  });

  listener.subscribe(function(message) {
    console.log('Received message on ' + listener.name + ': ' + message.data);
    listener.unsubscribe();
  });
</script>
</head>

<body>
  <h1>Simple roslib Example</h1>
  <p>Check your Web Console for output.</p>
</body>
</html>
```

**Subscribing to a Topic** 부분을 보면 직관적으로 어떻게 하면 원하는 토픽을 웹 페이지로 띄울 수 있는지 감이 올 것이다. 위 내용을 templates/subscriber.html에 저장하겠다.

# Test
이제 다시 파이썬 파일을 실행한다.
```bash
$ python3 app.py
```

그 후 rosbridge_websocket을 실행한다.
```bash
$ roslaunch rosbridge_server rosbridge_websocket.launch
```

다른 터미널에선 임의의 토픽을 publish 한다.
```bash
$ rostopic pub /listener std_msgs/String "Hello, World"
```

이제 웹 페이지에 접속한다.

![page](/assets/images/ros-web/page.jpg)

아무것도 없다. flask에서 웹 페이지를 꾸며주지 않아서 그렇다. 일단은 f12를 눌러서 web console을 켜보자. f12 누르고 뜨는 이상한 창 상단에 뒤적거리다 보면 Console을 발견할 수 있을 것이다.

![console](/assets/images/ros-web/console.PNG)

이렇게 새로고침 할 때마다 Hello, World! 가 콘솔에 출력되는 것을 볼 수 있다. 만약에 새로고침 없이 계속 반복적으로 값을 갱신하고 싶으면 ```listener.unsubscribe();``` 를 지우면 된다. 그리고 이 값들을 웹 페이지 내에 텍스트 박스 등을 통해서 이쁘게 띄우고 싶다면 구글링을 하면 된다.
