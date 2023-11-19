---
layout:   post
title:    ROS 에러'rcutils_expand_user failed' / 'Failed to initialize logging Failed to get logging directory'
category: ros
image:    /assets/img/blog/2023-05-02/thumnail_ros.png
---

* this unordered seed list will be replaced by toc as unordered list
{:toc}

ROS2를 일반적으로 사용할 수 있는 Ubuntu와 제한된 하드웨어 환경에서 작업을 하다 보면, 갖가지 문제에 시달렸습니다.

그 중 하나는 ROS2CLI의 ros2 node list나 ros2 topic list 같은 일반적인 명령어를 요구할 때, logging 데이터를 불러오는데 실패했다고 에러가 뜨는 상황이었습니다.

~~~bash
>>> [rcutils|error_handling.c:108] rcutils_set_error_state()
This error state is being overwritten:

'rcutils_expand_user failed' at {특정 경로}

with this new error message:

'Failed to get logging directory, at {특정 경로}

rcutils_reset_error() should be called after error handling to avoid this.
<<<
Failed to initialize logging: Failed to get logging directory, at {특정 경로}
~~~

이는 본인의 HOME 디렉토리(/~)에 log를 저장할 공간이 없는데 logger를 호출하면서 생기는 문제입니다.

일반적으로 Ubuntu 환경에서 ROS를 설치해서 사용하면 ROS1이든 ROS2이든, HOME 디렉토리를 기준으로 ~/.ros라는 경로가 생성되어 있을 겁니다.

## ROS1 / ROS2 Log 디렉토리

**ROS1**
![그림1](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-05-02/ros2_logging_error_1.png?raw=true)

**ROS2**
![그림2](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-05-02/ros2_logging_error_2.png?raw=true)

위의 이미지를 보시다시피 .ros 폴더 내부에는 log 폴더가 있으며, 이곳에서 여러분의 ROS에서 사용되는 log 기록을 남기게 되며, ROS를 사용한다면 이러한 메커니즘이 기본적으로 존재합니다.


## 해결방법
정리하면 처음의 에러는 이러한 log 기록을 위한 폴더의 부재가 원인이므로, 본인이 사용하는 현재 디렉토리를 기점으로 log를 저장할 경로를 생성해야 합니다.

그리고 이 경로에 대한 내용이 셸스크립트를 통해 적용하지 못했다면, 이에 대한 환경변수를 적용해줍니다. 

예를 들면, 만약 /main/setup 디렉토리가 HOME 디렉토리(~/)이고 이 디렉토리에 log 폴더 이름을 'my_log'로 설정했다고 가정하겠습니다. 그럼 ~/.bashrc에 디렉토리에 대해서 아래와 같이 작성해두시면 되겠습니다.

~~~bash
export ROS_HOME=/main/setup/my_log
~~~

커맨드로 직접 'export ROS_HOME=/main/setup/my_log' 입력할 수도 있고, ~/.bashrc 같은 bash 사용을 연결할 수 있다면 위와 같이 작성해두면 log에 대한 에러는 더 이상 발생하지 않을 겁니다.

log 디렉토리에 대한 추가적인 내용은 아래의 공식 홈페이지를 통해 참고하시기 바랍니다.

👉[Logging Directory 공식 문서](https://docs.ros.org/en/foxy/Tutorials/Demos/Logging-and-logger-configuration.html#id9)

![그림3](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-05-02/ros2_logging_error_3.png?raw=true)

---
ROS의 전체적인 구성을 제한된 환경 속에서 하나씩 직접 구성해보니 환경과 구조 이해가 얼마나 얕았는지 알 수 있었습니다. 사소하다고 넘겨버린 내용들이 알고 보면 조그만 관심을 기울이면 쉽게 해결했을 단서들이 상당하게 존재했습니다. 

의식을 하더라도 한 번에 구조를 디테일하게 살펴보는 건 힘들지라도 차근차근 다져야 할게 참 많은게 ROS인 것 같습니다.