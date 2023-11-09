---
layout: post
title: ROS TF tree 보는 방법 / 저장 방법
category: ros
image: /assets/img/blog/2023-04-10/thumnail_ros.png
---

* this unordered seed list will be replaced by toc as unordered list
{:toc}

ROS에서 노드들을 실행하면서 현재의 TF 상황을 파악하려면 필요한 작업 중 하나입니다.
Robot에서 중요한 요소 중 하나가 좌표계입니다. 문득 한 번씩 관련 내용을 확인하거나 저장하려고 보면 명령어가 떠오르지 않는 경우가 있더군요.

## rqt를 사용하여 확인
![그림1](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-04-10/ROS_tf_tree_1.png?raw=true)

rqt의 상단 ***'Plugins 탭'***을 보시면 위와 같이 TF Tree를 보시는 방법도 있지만 가상환경에서 작업하다보면 rqt plugins가 갖춰지지 않는 경우와 마주하기도 합니다. 그럴 때는 아래와 같은 방법이 있습니다.

##  TF tree 저장하는 방법

![그림2](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-04-10/ROS_tf_tree_2.png?raw=true)

위와 같은 TF tree 그림을 '.pdf'형식의 파일로 저장하는 방법이 있습니다.

### ROS1의 경우
~~~bash
rosrun tf view_frames.py
~~~

### ROS2의 경우
~~~bash
rosrun tf view_frames.py
~~~