---
layout:   post
title:    WSL에서 ROS1 ROS2 동시에 사용하기 
category: ros
image:    /assets/img/blog/2024-03-21/thumnail_ros.png
---

* this unordered seed list will be replaced by toc as unordered list
{:toc}

## Ubuntu 20.04 버전과 ROS1, ROS2
보통 가상환경으로도 보편적인 작업들은 충분히 이루어지는 것 같습니다. 학부 때만 해도 ROS1으로 대부분을 테스트했지만 현재는 업계 특성상 ROS2로 대부분 작업이 이루어지는 편입니다.

그래서인지 ROS1과 ROS2를 처음에는 Ubuntu 18.04와 Ubuntu 20.04를 따로 따로 관리하고 최신 버전은 또 22.04까지도 설치하니 제법은 용량을 차지하기 시작했습니다.

그리고 그 환경 세팅에도 여러 차례 거치게 되면서 조금 비효율적인 느낌을 받았습니다.

그래서 ROS1의 최신 버전인 Noetic과 Ubuntu 20.04에서 사용 가능한 ROS2 버전들을 함께 사용하는 방법을 살펴보려고 합니다.

![그림1](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-03-21/rosversion_manage_1.png?raw=true)

보통 ROS의 버전들을 살펴보면 위와 같이 어떤 버전의 Ubuntu를 타겟하는지 적혀 있습니다.

제가 사용하는 환경은 WSL이나 별도의 디스크 공간을 활용해 Window와 Ubuntu를 동시 사용하는 더블 OS환경으로 사용하기도 합니다. Ubuntu 20.04 환경에서 ROS1 Noetic과 ROS2 Foxy(또는 Galactic)을 사용하는 방법을 소개해보려고 합니다.

## 데비안(Debian) 환경에서의 설치를 따를 때
보통 ROS 설치를 따라하시는 방법은 Debian환경을 기준으로 설치를 하셔서 'opt/ros/' 디렉토리로 여러분이 선택한 ROS 버전 이름의 폴더를 생성하여 설치하는 방식을 따를 겁니다.

사실 특별한 방법이라기 보다는 셀 스크립트(Shell Script)의 기능 덕분에 제가 사용하는 방식입니다.

여러분이 앞서 이야기한 데비안 환경에 따른 설치를 하시면 최종적으로 다음과 같은 명령어를 입력하여 설치를 마무리할 겁니다.

~~~bash
source /opt/ros/noetic/setup.bash
~~~

셀 스크립트 중에서 bash를 베이스로 하는 ROS에 맞춰서 이 명령어를 통해 ROS를 사용할 수 있게 됩니다. Ubuntu와 같은 CLI 환경에서는 이러한 셀 스크립트의 사용이 중요합니다.

예를 들면, 아래의 사진을 한 번 보시죠.

![그림2](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-03-21/rosversion_manage_2.png?raw=true)

'/opt/ros' 디렉토리에 foxy와 noetic이 설치되어 있습니다. 'rosversion -d'를 입력하면(별도 설치 필요한 명령어) 현재 활성화된 ROS 버전이 무엇인지 확인하실 수 있습니다.

저는 처음 부팅 시도 때 따로 ROS를 source 연결해두지 않고 별도 단축 명령어(alias)를 설정해뒀습니다.

그래서 처음에 \<unkown\>이라고 표시되는 것이죠. 그리고 사용하려는 ROS 버전에 따라서 foxy 또는 noetic을 단축 명령어로 설정해둬서 입력하여 사용하는 방식입니다.

foxy를 입력하면 source /opt/ros/foxy/setup.bash가 noetic을 입력하면 source /opt/ros/noetic/setup.bash가 실행되면서 사진처럼 버전을 변경하여 사용할 수 있는 것이죠.

![그림3](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-03-21/rosversion_manage_3.png?raw=true)

덕분에 아래처럼 하나의 Ubuntu 버전 아래 ROS1과 ROS2를 동시에 사용하는 모습을 보실 수 있습니다.

![그림4](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-03-21/rosversion_manage_4.png?raw=true)

저는 Window 환경에서는 이렇게 설정을 해두고 ROS1과 ROS2를 오가면서 테스트하는 편입니다. 가상 환경의 제한으로 디버깅이 필요하거나 특정 예외 상황에서는 Ubuntu OS에서 해야할 때도 있지만 Window 내에서 처리할 수 있는 테스트는 이렇게 구성해서 작업을 하는 편입니다.

WSL을 사용한다면 참고하시길 바랍니다!