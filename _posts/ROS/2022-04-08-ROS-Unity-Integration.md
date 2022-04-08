---
title: "[Unity] ROS & Unity Integration"
excerpt: "ROS & Unity tutorial"
toc: true
toc_sticky: true
categories:
  - ROS
tag:
  - ROS
  - Unity
use_math: true
last_modified_at: 2022-04-08T22:00-22:30
---

# ROS & Unity
Unity는 게임 개발, 그래픽 툴로 많이 알려져있다. 하지만 요즘은 로보틱스 분야에서 강화학습 플랫폼으로도 많이 쓰이는 것 같다. 동시에 Unity 자체적으로 ROS와의 연동 또한 적극적으로 지원하는 듯 하다. 그래서 ROS와의 연동을 통해 Unity 활용에 대해 공부해보고자 한다.

Unity는 다음과 같은 튜토리얼 패키지와 ROS 네트워크와 통신할 수 있는 TCP 통신 패키지를 제공한다. 해당 git에 있는 튜토리얼을 기반으로 본 내용을 작성한다. 보다 자세한 내용은 원문을 통해 확인하는 것을 추천한다.

> 현재 페이지의 튜토리얼을 진행하기 위해 Unity-Robotics-Hub를 clone 한 후 **/Unity-Robotics-Hub/tutorials/ros_unity_integration/ros_packages** 에 존재하는 두 ROS 패키지를 catkin_ws/src로 복사, 빌드해야 한다.

- [Unity-Robotics-Hub](https://github.com/Unity-Technologies/Unity-Robotics-Hub)
- [ROS-TCP-Connector](https://github.com/Unity-Technologies/ROS-TCP-Connector)

# Unity - Installing the Unity Robotics packages
유니티 좌측 상단의 **Window -> Package Manager** 를 클릭해 패키지 매니저를 열어준다.

![package_manager](/assets/images/ros-unity-integration/package_manager.png)
![add_package](/assets/images/ros-unity-integration/add_package.png)

좌측 상단의 + 버튼을 누르면 패키지를 세 가지 방법으로 추가할 수 있다. 여기서 제일 아래에 있는 **Add package from git URL...** 을 선택한다. 그 후 다음 세 가지 패키지의 링크를 각각 복사해서 add 한다.

> - ## ROS-TCP-Connector
> ROS와 통신을 가능하게 해주는 패키지이다.
>
> https://github.com/Unity-Technologies/ROS-TCP-Connector.git?path=/com.unity.robotics.ros-tcp-connector
>
> - ## URDF-Importer
> URDF 파일을 유니티로 import 해올 수 있도록 해주는 패키지이다.
>
> https://github.com/Unity-Technologies/URDF-Importer.git?path=/com.unity.robotics.urdf-importer
> - ## Visualizations
> ROS의 rviz와 비슷하게 유니티 환경에서 ROS 네트워크에 돌아다니는 토픽들을 visualize 할 수 있게 해주는 패키지이다.
>
> https://github.com/Unity-Technologies/ROS-TCP-Connector.git?path=/com.unity.robotics.visualizations

위 세 가지 패키지를 모두 추가하면 유니티 좌측 상단에 **Robotics** 메뉴가 생길 것이다.(사실 이 메뉴는 ROS-TCP-Connector만 추가해도 생긴다.)

ROS 네트워크와 통신하기 위해서는 IP 주소를 맞춰줘야 한다. 좌측 상단에 새로 생긴 **Robotics -> ROS Settings** 를 클릭한 후 **ROS IP Address** 에 현재 ROS 네트워크의 주소를 입력하면 된다.

![ROS_setting](/assets/images/ros-unity-integration/ROS_setting.png)

# Unity - msg build
ROS에서 사용할 메시지 토픽을 유니티에서도 사용하기 위해 빌드할 필요가 있다. **Robotics -> Generate ROS Messages...** 를 클릭한다.

![msg](/assets/images/ros-unity-integration/msg.png)

**ROS message path**에서 사용할 .msg 파일이 있는 위치를 지정해준다. 그 후 아래 있는 **Build msgs**를 선택해 유니티에서 사용할 수 있도록 빌드해주면 된다. .srv 파일도 똑같다.

# Unity -> ROS
유니티에서 임의의 객체가 현재 position과 rotation 정보를 publish하고 ROS 네트워크에서 확인하는 예제이다.

유니티 프로젝트 폴더 내의 /Assets/ 아래에 스크립트들을 저장할 Scripts 폴더를 만든다. Scripts 폴더 안에 다음 C# 코드를 저장한다. 파일 명은 튜토리얼과 동일하게 RosPublisherExample로 한다. 해당 내용은 [공식 튜토리얼](https://github.com/Unity-Technologies/Unity-Robotics-Hub/blob/main/tutorials/ros_unity_integration/publisher.md)에서 확인 가능하다.

```csharp
using UnityEngine;
using Unity.Robotics.ROSTCPConnector;
using RosMessageTypes.UnityRoboticsDemo;

/// <summary>
///
/// </summary>
public class RosPublisherExample : MonoBehaviour
{
    ROSConnection ros;
    public string topicName = "pos_rot";

    // The game object
    public GameObject cube;
    // Publish the cube's position and rotation every N seconds
    public float publishMessageFrequency = 0.5f;

    // Used to determine how much time has elapsed since the last message was published
    private float timeElapsed;

    void Start()
    {
        // start the ROS connection
        ros = ROSConnection.GetOrCreateInstance();
        ros.RegisterPublisher<PosRotMsg>(topicName);
    }

    private void Update()
    {
        timeElapsed += Time.deltaTime;

        if (timeElapsed > publishMessageFrequency)
        {
            cube.transform.rotation = Random.rotation;

            PosRotMsg cubePos = new PosRotMsg(
                cube.transform.position.x,
                cube.transform.position.y,
                cube.transform.position.z,
                cube.transform.rotation.x,
                cube.transform.rotation.y,
                cube.transform.rotation.z,
                cube.transform.rotation.w
            );

            // Finally send the message to server_endpoint.py running in ROS
            ros.Publish(topicName, cubePos);

            timeElapsed = 0;
        }
    }
}
```

# ROS -> Unity
ROS에서 색상 정보를 publish하고 그에 따라 유니티의 객체 색이 변하는 예제이다. 위와 동일한 위치에 다음 C# 코드를 저장한다. 파일명은 RosSubscriberExample 로 한다. 이 또한 [공식 튜토리얼](https://github.com/Unity-Technologies/Unity-Robotics-Hub/blob/main/tutorials/ros_unity_integration/subscriber.md)에서 확인할 수 있다.

```csharp
using UnityEngine;
using Unity.Robotics.ROSTCPConnector;
using RosColor = RosMessageTypes.UnityRoboticsDemo.UnityColorMsg;

public class RosSubscriberExample : MonoBehaviour
{
    public GameObject cube;

    void Start()
    {
        ROSConnection.GetOrCreateInstance().Subscribe<RosColor>("color", ColorChange);
    }

    void ColorChange(RosColor colorMessage)
    {
        cube.GetComponent<Renderer>().material.color = new Color32((byte)colorMessage.r, (byte)colorMessage.g, (byte)colorMessage.b, (byte)colorMessage.a);
    }
}
```

# ROS <-> Unity
이제 유니티 환경에 간단한 cube 객체를 만들어준다.

![unity](/assets/images/ros-unity-integration/Unity.png)

큐브 객체의 이름은 ROS cube로 했다. 이 객체에 아까 만들어준 두 C# 스크립트를 drag & drop 혹은 Add component를 통해 추가해준다.

마지막으로 **catkin_ws/src/unity_robotics_demo/scripts/color_publisher.py** 를 살짝 수정해준다. 이건 공식 튜토리얼엔 없는 내용이고, 필자가 임의로 수정한 내용이다. 원래 코드는 한 번만 색상 정보를 publish하고 꺼지는데, 안 꺼지고 계속 랜덤한 색상 정보를 publish하도록 수정했다.

```python
#!/usr/bin/env python

import random
import rospy
import rosgraph
import time
from unity_robotics_demo_msgs.msg import UnityColor


TOPIC_NAME = 'color'
NODE_NAME = 'color_publisher'


def post_color():
    pub = rospy.Publisher(TOPIC_NAME, UnityColor, queue_size=10)
    rospy.init_node(NODE_NAME, anonymous=True)
        
    rate = rospy.Rate(1) # 10hz
    while not rospy.is_shutdown():
        r = random.randint(0, 255)
        g = random.randint(0, 255)
        b = random.randint(0, 255)
        color = UnityColor(r, g, b, 1)

    #wait_for_connections(pub, TOPIC_NAME)
        rospy.loginfo('r: %f, g:%f, b:%f', r, g, b)
        pub.publish(color)

    #time.sleep(0.1)
        rate.sleep()


def wait_for_connections(pub, topic):
    ros_master = rosgraph.Master('/rostopic')
    topic = rosgraph.names.script_resolve_name('rostopic', topic)
    num_subs = 0
    for sub in ros_master.getSystemState()[1]:
        if sub[0] == topic:
            num_subs+=1

    for i in range(10):
        if pub.get_num_connections() == num_subs:
            return
        time.sleep(0.1)
    raise RuntimeError("failed to get publisher")


if __name__ == '__main__':
    try:
        post_color()
    except rospy.ROSInterruptException:
        pass
```

# ROS <-> Unity Test
먼저 터미널에 다음 launch 파일을 실행해 ROS와 유니티 간의 통신을 가능케한다.

```bash
$ roslaunch ros_tcp_endpoint endpoint.launch
```

그 다음 유니티에서 publish하는 pose와 rotation 정보를 확인하기 위해 또 다른 터미널에 다음 명령어를 입력한다.

```bash
$ rostopic echo /pos_rot
```

색상 정보를 publish하기 위해 또 다른 터미널에서 color_publisher를 실행한다.

```bash
$ rosrun unity_robotics_demo color_publisher.py
```

![terminal_play](/assets/images/ros-unity-integration/terminal_play.png)

이제 유니티로 넘어와 상단의 시작 버튼을 클릭하면 ROS와의 통신 결과를 확인할 수 있다.

![unity_play](/assets/images/ros-unity-integration/unity_play.png)

생성한 큐브 객체가 랜덤한 rotation 값으로 회전하며 랜덤한 색상으로 변할 것이다.