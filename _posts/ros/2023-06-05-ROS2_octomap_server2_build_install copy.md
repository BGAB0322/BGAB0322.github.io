---
layout: post
title: ROS2 Octomap Server2 빌드 및 설치 방법
category: ros
image: /assets/img/blog/2023-06-05/thumnail_ros.png
# sitemap: false
---

* this unordered seed list will be replaced by toc as unordered list
{:toc}

ROS1에서 사용하던 때와 달리 ROS2로 넘어오면서 구조적인 변화로 인해 몇몇 패키지는 이전처럼 사용하기 힘들어졌습니다.
그래도 특정 유저들의 노고 덕분에 ROS2에서도 사용할 수 있게 코드를 수정해 깃허브에 배포하는 경우가 제법 있습니다.
그 중 3D 포인트 클라우드를 활용해 Rviz2에 3D 맵핑이 가능하게 해주는 Octomap Server를 빌드 / 설치하는 방법을 다뤄보고자 합니다.

## 설치 구성 OctoMap / Ocotmap Server2

설치는 크게 2가지가 필요합니다.
ROS2에서 Octomap Server2를 사용하기 위해서 OctoMap을 먼저 사용하는 플랫폼에 설치를 완료하고 빌드를 시도해야 합니다.
빌드 이후 ROS2에서 적용하기 위한 과정이 처음 진행하기에는 어떤 파일을 선별하여 옮겨야 하는지 모르기 때문에 생각보다 시간을 많이 잡아 먹을 수도 있습니다.
제 과정을 따라오시면서 쉽게 설치를 진행하리라 생각합니다. 우선 아래의 2개의 사이트에서 파일들을 다운로드 합니다.

### ROS2 Octomap
참고 주소 (https://github.com/OctoMap/octomap)

### ROS2 Octomap Server2
참고 주소 (https://github.com/iKrishneel/octomap_server2)

## ROS2 Octomap 컴파일 및 설치하기
![그림1](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-06-05/octomap_server2_build_1.png?raw=true)

우선 Home (~/) 디렉토리에 octomap-ros2를 설치해주도록 합시다.

![그림2](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-06-05/octomap_server2_build_2.png?raw=true)

### 1. ~/octomap-ros2/octomap 디렉토리로 이동합니다.
~~~bash
cd ~/octomap-ros2/octomap
~~~

### 2. 빌드를 위한 폴더를 생성하고 생성한 'build'폴더로 이동합니다.
~~~bash
mkdir build && cd build
~~~

![그림3](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-06-05/octomap_server2_build_3.png?raw=true)

### 3. cmake 명령어로 makefile 생성 및 make로 빌드를 실행합니다.
~~~bash
cmake .. && make
~~~

위의 명령어 하나로 경로 연결 걱정 없이 cmake 덕분에 쉽게 빌드를 마무리해줍니다. 

![그림4](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-06-05/octomap_server2_build_4.png?raw=true)


### 4. /opt/ros/foxy/share/ 경로에 octomap 폴더를 생성합니다.
Ubuntu(Debian) 설치 기준, foxy 버전에서 작업했습니다.
<span style="color:red"> include/lib/share에 octomap 설치 인식을 위한 파일 복사</span>가 필요합니다.
우선 share 폴더부터 진행해봅니다.

~~~bash
cd /opt/ros/foxy/share/ && sudo mkdir -p octomap
~~~

### 5. '.cmake'파일을 복사합니다.(2개)
~/octomap-ros2/octomap/build/InstallFiles/ 경로로 이동합니다.
아래의 2개의 파일을 /opt/ros/foxy/share/octomap/ 디렉토리에 복사해줍니다. (복사 명령어는 아래를 참고하세요.)

* octomap-config.cmake
* octomap-config-version.cmake

![그림5](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-06-05/octomap_server2_build_5.png?raw=true)

~~~bash
//sudo cp -r '복사할 파일의 이름(또는 디렉토리를 붙여서)' '붙여넣을 디렉토리' //아래는 예시입니다.
sudo cp -r octomap-config.cmake /opt/ros/foxy/share/octomap/
~~~

### 6. '.cmake' 파일을 복사합니다.(1개)
이번엔 ~/octomap-ros2/octomap/build/src/CMakeFiles/Export/share/octomap/ 디렉토리에 있는 

* octomap-targets.cmake

파일을 동일하게 /opt/ros/foxy/share/octomap/ 디렉토리로 cmake 파일을 복사해줍니다.

![그림6](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-06-05/octomap_server2_build_6.png?raw=true)

그럼 총 3개의 cmake 파일을 ROS가 설치된 share 디렉토리로 옮겼습니다.

* octomap-config.cmake
* octomap-config-version.cmake
* octomap-targets.cmake

이 3개의 파일들이 octomap 패키지와 관련한 cmake 경로를 해결해주는 겁니다.

### 7. include의 헤더 파일을 복사합니다. 
~/octomap-ros2/octomap/include/ 디렉토리로 이동하여 octomap 폴더 전체를 /opt/ros/foxy/include 디렉토리로 복사해줍니다.

![그림7](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-06-05/octomap_server2_build_7.png?raw=true)

~~~bash
cd ~/octomap-ros2/octomap/include
sudo cp -r octomap /opt/ros/foxy/include
~~~

![그림8](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-06-05/octomap_server2_build_8.png?raw=true)

ocotmap에 사용되는 헤더파일들을 담고 있습니다.

### 8. 라이브러리(.so)파일을 복사합니다.
마지막으로 ~/octomap-ros2/octomap/lib 디렉토리로 이동하고 아래와 같이 나열된 라이브러리(.so)파일들을 /opt/ros/foxy/lib/ 디렉토리에 복사해줍니다.
* liboctomap.a
* liboctomap.so
* liboctomap.so.1.9
* liboctomap.so.1.9.0
* liboctomath.a
* liboctomath.so
* liboctomath.so.1.9
* liboctomath.so.1.9.0

~~~bash
sudo cp -r liboctomap.a liboctomap.so liboctomap.so.1.9 liboctomap.so.1.9.0 liboctomath.a liboctomath.so liboctomath.so.1.9 liboctomath.so.1.9.0 /opt/ros/foxy/lib
~~~

이로써 octomap 패키지의 기본적인 설치를 현재 ROS2 환경에 설치가 완료된 상황입니다. 이제 본격적으로 Octomap Server2 패키지를 설치해보도록 하죠.

## ROS2 Octomap Server2 패키지 컴파일 및 설치하기
![그림9](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-06-05/octomap_server2_build_9.png?raw=true)

### 1. Home 디렉토리에 폴더를 만듭니다. 
폴더를 만들고 나면 생성 경로로 이동하고 위의 Octomap Server2를 git clone으로 설치하겠습니다.

~~~bash
mkdir -p ~/octomap_ws/src/
cd ~/octomap_ws/src
git clone https://github.com/iKrishneel/octomap_server2.git
~~~

![그림10](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-06-05/octomap_server2_build_10.png?raw=true)

### 2. Octomap Server2 관련 패키지 설치
octomap_server2/ 이동하시고 아래처럼 명령어를 작성해주시기 바랍니다.
octomap에 필요한 패키지들을 추가적으로 설치해주는데 octomap_msgs와 pcl(point cloud library) 패키지가 설치됩니다.

~~~bash
cd ~/octomap_ws/src/octomap_server2/
vcs import . < deps.repos
~~~

![그림11](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-06-05/octomap_server2_build_11.png?raw=true)

### 3. perception_pcl만 유지
(pcl은 설치를 이미 거친 경우로 가정하고) pcl_msgs 폴더를 지웁니다. perception_pcl 폴더에서 pcl_conversions와 pcl_ros를 지웁니다.

![그림12](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-06-05/octomap_server2_build_12.png?raw=true)

### 4. octomap_msgs 디렉토리의 헤더 파일을 복사하기
~/octomap_ws/src/octomap_msgs/include/ 디렉토리에 있는 octomap_msgs 폴더 전체를 /opt/ros/foxy/include 디렉토리로 복사합니다.
(헤더파일 경로 인식을 위해 필요한 과정 중 하나입니다.)

![그림13](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-06-05/octomap_server2_build_13.png?raw=true)

~~~bash
cd ~/octomap_ws/src/octomap_msgs/include/
sudo cp -r octomap_msgs/ /opt/ros/foxy/include
~~~

### 5. colcon build, 패키지 설치하기
이제 정말로 컴파일을 위한 전제 작업이 끝이 났습니다.
이제 octomap_ws 워크스패이스로 돌아가서 빌드, 패키지 설치를 진행하면 됩니다.

~~~bash
cd ~/octomap_ws/
colcon build --symlink-install
~~~

![그림14](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-06-05/octomap_server2_build_14.png?raw=true)

### 6. bash 파일 환경 설정
환경 설정까지 해주면, 드디어 octomap server2 설치를 완료하였습니다.

~~~bash
source ~/octomap_ws/install/setup.bash
~~~

실행은 launch 파일로 하시면 됩니다.

~~~bash
ros2 launch octomap_server2 octomap_server_launch.py
~~~

![그림15](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-06-05/octomap_server2_build_15.png?raw=true)

마지막으로 launch 파일에서 주요한 파라미터 수정만 이야기하고 마무리 하겠습니다.

## launch 파일 파라미터 수정

![그림16](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-06-05/octomap_server2_build_16.png?raw=true)

launch 파일을 살펴보면 argument에 값을 수정/적용하게끔 해두었는데 우선 주요한 파라미터를 살펴보면,

* **input_cloud_topic** -> subscribe하려는 pointcloud3D 메시지를 입력하시면 됩니다. 본인의 3D 데이터 토픽이름을 확인하세요.

* **resolution** -> rviz2에서 디스플레이할 pointcloud의 크기입니다.

* **pointcloud_min_z / pointcloud_max_z** -> 높이에 해당하는 z축 범위를 표현하는 내용인데, 직접 추가하여 작성해야 합니다.

소스파일을 보면 파라미터 중에 포인트 클라우드 x, y, z 범위에 해당하는 값들의 파라미터가 존재합니다.
대표적으로 ROS1에서처럼 z축이 있는데 launch 파일에 추가하여 작성하시면 됩니다.

![그림17](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-06-05/octomap_server2_build_17.png?raw=true)

---

여기까지가 octomap server2를 ROS2에서 사용하기 위한 여정이었습니다.
처음에 octomap 설치 진행부터 버벅거리느라 시간을 제법 잡아먹었습니다.
본인의 사용 목적에 맞춰서 필요하신 분은 참고하여 설치하는데 시간 절약하시기 바랍니다. 감사합니다. 

