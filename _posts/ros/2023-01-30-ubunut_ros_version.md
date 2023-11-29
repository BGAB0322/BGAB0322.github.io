---
layout:   post
title:    Ubuntu 버전 확인 & ROS 버전 확인
category: ros
image:    /assets/img/blog/2023-01-30/thumnail_ros.png
---

* this unordered seed list will be replaced by toc as unordered list
{:toc}

## Ubuntu 버전 확인 'lsb_release -a'

보통 작업 간에 Ubuntu 버전까지 터미널을 통해서 체크할 일은 없어 잘 기억나지 않는 명령어입니다.

~~~bash
lsb_release -a
~~~

![그림1](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-01-30/ubunut_ros_version_1.png?raw=true)

GUI가 제한된 환경이나 기타 Unix 및 리눅스 계열의 운영체제를 사용하는 하드웨어로 인해서 종종 사용할 수도 있더군요.

18.04 / 20.04 / 22.04 버전 뒤에 붙는 LTS인지 또는 다른 버전인지 확인 차 사용하면 좋을 명령어입니다.

## ROS 버전 확인 'rosversion -a'

플랫폼에서 여러 ROS 버전을 사용하시는 분들이라면, 알아두면 좋은 명령어입니다. 다만, Ubuntu에서 기본적으로 설치되어 사용할 수 있는게 아니라 설치가 별도로 필요할 수 있습니다.

~~~bash
rosversion -a
~~~

![그림2](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-01-30/ubunut_ros_version_2.png?raw=true)

해당 명령어를 사용할 수 없다면, 아래의 명령을 실행해 설치를 진행해주도록 합시다.

~~~bash
sudo apt install python3-rospkg
~~~

rosversion -d 를 입력해서 <unknown>이 뜨는 경우가 있습니다. 이는 ROS 설치 환경변수를 설정하지 않았을 때 발생하는 현상일 수 있습니다.

보통 ROS 설치를 따라하고 마지막에 'source /opt/ros/foxy/setup.bash'를 입력하는 걸 봤을 겁니다. ~/.bashrc에 이를 입력해두지 않았거나, 따로 설정 안 하신 분들은 setup.bash를 실행하지 않아 발생했으니 자신의 ROS 설치 경로를 찾아가 확인 작업이 필요합니다.