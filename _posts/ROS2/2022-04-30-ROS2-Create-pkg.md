---
title: "ROS2 create package"
excerpt: "How to create ROS2 package"
toc: true
toc_sticky: true
categories:
  - ROS2
tag:
  - ROS2
use_math: true
last_modified_at: 2022-04-30T16:00-16:30
---

# Create Package
파이썬 패키지로 간단한 publisher와 subscriber를 만들어 본다. /colcon_ws/src/ros2comm/ 아래에 서버와 클라이언트 역할을 할 두 패키지를 만들 것이다.

```bash
$ cd /colcon_ws/src
$ mkdir ros2comm
$ cd ros2comm

# ros2 pkg create --build-type {빌드 타입} --node-name {노드명} {패키지명}
$ ros2 pkg create --build-type ament_python --node-name serverRun ros2server
$ ros2 pkg create --build-type ament_python --node-name clientRun ros2client
```

아래와 같이 패키지가 생성된다.

![pkg](/assets/images/ros2-create-pkg/pkg.png)

우리가 직접 코드를 짤 부분은 ros2server, ros2client 폴더 내부에 있다.
> /colcon_ws/ros2comm/ros2server/ros2server
>
> /colcon_ws/ros2comm/ros2client/ros2client

# ros2server
먼저 서버 부분에 클래스를 생성하고 노드를 만들어본다.

> /colcon_ws/ros2comm/ros2server/ros2server

해당 위치에 comm.py라는 파일을 만들어 클래스를 생성해준다.
```python
import rclpy
from rclpy.node import Node
from std_msgs.msg import String

class ServerPublisher(Node):
    def __init__(self):
        super().__init__('server_publisher')
        self.publisher = self.create_publisher(String, 'server2client', 10)
        
    def MessagePublish(self, sendMsg):
        msg = String()
        msg.data = sendMsg
        self.publisher.publish(msg)
        self.get_logger().info('Publishing: %s' % msg.data)
        
class ServerSubscriber(Node):
    def __init__(self):
        super().__init__('server_subscriber')
        self.subscriber = self.create_subscription(String, 'client2server', self.callback_subscribe, 10)
        
    def callback_subscribe(self, msg):
        self.get_logger().info('Received Message: %s' % msg.data)     
```

이제 위 클래스를 사용해 subscribe 하는 내용을 메인문에 작성해준다. 아래 코드는 패키지를 만들때 생성한 serverRun.py 에 작성한다.

```python
import rclpy
import ros2server.comm as comm

def main(args=None):
    rclpy.init(args=args)
    print("Server Run!")
    server_subscriber = comm.ServerSubscriber()
    
    rclpy.spin(server_subscriber)
    server_subscriber.destroy_node()
    rclpy.shutdown()

if __name__ == '__main__':
    main()

```

# ros2client
comm.py
```python
import rclpy
from rclpy.node import Node
from std_msgs.msg import String

class ClientPublisher(Node):
    def __init__(self):
        super().__init__('client_publisher')
        self.publisher = self.create_publisher(String, 'client2server', 10)
        
        timer_period = 0.5
        self.timer = self.create_timer(timer_period, self.timer_callback)
        self.i = 0
        
    def timer_callback(self):
        msg = String()
        msg.data = 'Hello Server! %d' % self.i
        self.publisher.publish(msg)
        self.get_logger().info('Publishing: %s' % msg.data)
        self.i += 1
        
class ClientSubscriber(Node):
    def __init__(self):
        super().__init__('client_subscriber')
        self.subscriber = self.create_subscription(String, 'server2client', self.listener_callback, 10)
        
    def listener_callback(self, msg):
        self.get_logger().info('Subscribed: %s' % msg.data)
```

clientRun.py
```python
import rclpy
import ros2client.comm as comm

def main(args=None):
    rclpy.init(args=args)
    client_publihser = comm.ClientPublisher()
    
    rclpy.spin(client_publihser)
    client_publihser.destroy_node()
    rclpy.shutdown()

if __name__ == '__main__':
    main()
```

# Build & Run
```bash
$ cd ~/colcon_ws && colcon build

# terminal 1
$ ros2 run ros2server serverRun

# terminal 2
$ ros2 run ros2client clientRun
```

