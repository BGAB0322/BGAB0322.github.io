---
layout:   post
title:    ROS2 토픽 데이터 분석 및 엑셀 파일로 저장하기 (by RPLiDAR A1)
category: ros
image:    /assets/img/blog/2023-02-01/thumnail_ros.png ##
---

* this unordered seed list will be replaced by toc as unordered list
{:toc}

이번 글은 RPLiDAR A1을 사용하여 라이다의 데이터를 분석에 용이한 엑셀 파일로 변환하는 방법에 대해 이야기하고자 합니다. 

-사양 확인-
* **Jetson Nano 2GB Dev Kit**
* **Ubuntu 20.04 LTS**
* **ROS2 Foxy**

![그림1](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-02-01/topic_to_excel_1.png?raw=true)

## ROS2 노드
RPLiDAR A1 노드를 실행해보도록 하겠습니다. 
~~~bash
ros2 launch rplidar_ros2 view_rplidar_launch.py
~~~

![그림2](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-02-01/topic_to_excel_2.png?raw=true)

Rviz2에서 메세지는 관측되고 있는 포인트 갯수를 확인할 수 있습니다.

![그림3](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-02-01/topic_to_excel_3.png?raw=true)

현재 실행 중인 노드 구성에 대하여 'rqt_graph'로 확인해 보았습니다. RPLiDAR A1 'Launch'파일을 통해 실행한 노드들을 보면, 2D 클라우드 포인트 데이터를 퍼블리시하는 /rplidar_scan_publisher가 있습니다.

![그림4](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-02-01/topic_to_excel_4.png?raw=true)

그리고 바로 위로 /transform_listener 가 있습니다. 그럼 RPLiDAR 퍼블리셔는 놔두고 Rviz2만 종료해보았습니다.

![그림5](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-02-01/topic_to_excel_5.png?raw=true)

/transform_listener는 없고 RPLiDAR 퍼블리셔만 남아 있습니다. 

Rviz2를 다시 실행해보니 아래와 같이 /transform_listener 노드가 다시 생성되었습니다.

![그림6](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-02-01/topic_to_excel_6.png?raw=true)

처음 Launch 파일을 실행했을 때처럼 RPLiDAR의 Scan Laser 메세지를 찾아서 Rviz2 설정에 추가해서 다시 노드 그래프를 살펴보면 /scan 토픽을 전달하고 있는 모습을 다시 볼 수 있습니다.

![그림7](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-02-01/topic_to_excel_7.png?raw=true)

transform_listener는 많은 양의 /tf 토픽을 서브스크라이브 하는 역할이라고 문서에서 다룹니다. 즉 Rviz2에서 RPLiDAR A1을 좌표계인 /laser를 기준으로 한 내용을 읽어들이고 있는 것입니다.

그래서 RPLiDAR A1의 포인트 클라우드 좌표계를 기준으로(/transform_linstener) 포인트클라우드 데이터(/scan)를 퍼블리시 중인 /rplidar_scan_publisher 노드를 Rviz2가 서브스크라이브하여 출력 중입니다.

CLI 명령을 통해 노드 목록을 확인해보면 아래와 같습니다.

![그림8](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-02-01/topic_to_excel_8.png?raw=true)

## 토픽과 메세지

~~~bash
ros2 node info /rplidar_scan_publisher
~~~

RPLiDAR 퍼블리셔 노드의 세부 정보를 살펴보겠습니다. 퍼블리셔에서 /scan 토픽, 즉 라이다의 센세 데이터들을 LaserScan 메세지 담아서 퍼블리시 하고, 메세지 타입의 상세를 살펴보면 sensor_msgs/msg/LaserScan입니다.

이는 sensor_msgs라는 패키지에서 구성하는 내용으로 2D 포인트 클라우드 메세지 타입으로 많이 사용하는 메세지 형태 중 하나입니다. 아래와 같이 확인할 수 있습니다.

![그림9](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-02-01/topic_to_excel_9.png?raw=true)

LaserScan 메세지에 내포하고 있는 변수의 구성을 살펴보면, 각도 최대/최소, 스캔 시간, 최대/최소 거리, 거리 데이터배열, intensity(반사되어 돌아오는 신호 강도) 배열로 구성되어 있습니다.

~~~bash
ros2 interface show sensor_msgs/msg/LaserScan
~~~
위의 CLI 명령어로 메세지 구성을 살펴보면 메세지가 포함하고 있는 변수의 구성을 아래와 같이 확인하실 수 있습니다.

![그림10](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-02-01/topic_to_excel_10.png?raw=true)

이제 해당 메세지의 데이터 값을 출력해보기 위해 토픽 echo를 사용해보면, 엄청난 속도로 값들이 나열됩니다. 덕분에 확인이 힘들고, 터미널에 표시되는 내용이 금방 잘리게 됩니다.

~~~bash
ros2 topic echo /scan
~~~

![그림11](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-02-012023-02-01/topic_to_excel_11.png?raw=true)

어쨌든 잠깐 echo 실행을 멈추고 거리데이터를 드래그해서 편집기에 붙여넣기 해보았습니다.

![그림12](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-02-01/topic_to_excel_12.png?raw=true)

포인트 갯수가 이상하게도 128개 밖에 없습니다. 아무 생각없이 데이터를 echo 했을 때는 몰랐지만, 생각해보면 스캐닝 방식의 라이다인데 수가 너무 적습니다.

데이터 시트를 확인해보면 알겠지만 360°도 회전하면서 측정되는 rplidar의 각도 resolution은 1°입니다. 그렇다면 데이터가 360개가 되야하는데 말이죠, 앞선 Rviz2에서도 200개 가량의 포인트가 측정되고 있다는 걸  확인도 했구요.

![그림13](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-02-01/topic_to_excel_13.png?raw=true)

그럼 나머지 데이터는 어떻게 출력할 수 있을까요?

## 토픽의 전체 데이터 엑셀파일로 저장하는 방법

~~~bash
ros2 topic echo --full-length --csv ($토픽이름) > ($원하는 엑셀 파일 이름.csv)
~~~
![그림14](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-02-01/topic_to_excel_14.png?raw=true)

이 명령어를 입력하여 엔터를 쳐보시면 따로 작동하는게 없어보이는데 사실 이 명령어를 실행한 순간부터 데이터를 엑셀파일로 정렬하여 저장하고 있습니다. 아래에 보다시피 Home 경로에 scan.scv라고 이미지를 보다시피 제가 지정한 이름을 토대로 파일을 저장하였습니다.

![그림15](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-02-01/topic_to_excel_15.png?raw=true)

이를 윈도우에서 따로 살펴보았습니다. 뭔가 값이 엄청 많아 보이는데 중감쯤에서 0.15, 12라는 값이 눈에 띕니다.

앞에서 메세지 내부 파라미터를 살펴봤던 min / max_range 값에 해당하는 내용입니다. 

![그림16](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-02-01/topic_to_excel_16.png?raw=true)

RPLiDAR A1의 스펙을 확인해보면 이는 측정거리임을 알 수 있는 것이죠.

![그림17](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-02-01/topic_to_excel_17.png?raw=true)

그럼 데이터 구성이 어떻게 되는지 대략 파악했으니 시작, 끝나는 지점을 셀 삽입으로 분리해놓고 거리데이터들을 쉽게 드래그할 수 있도록 정리해보았습니다.

![그림18](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-02-01/topic_to_excel_18.png?raw=true) | ![그림19](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-02-01/topic_to_excel_19.png?raw=true)
---|---|

한 줄 전체를 드래그해서 살펴보니 데이터 갯수가 360개입니다.

![그림20](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-02-01/topic_to_excel_20.png?raw=true)

각 resolution이 1°, 360°이니 포인트 데이터는 360개의 데이터라는걸 드디어 확인했군요. 

![그림21](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-02-01/topic_to_excel_21.png?raw=true)

inf는 측정하지 못한 값으로 표현한 값이며, 이제 이 값들을 이용해 의도하는 분석 내용으로 데이터들을 다듬어 갈 수 있습니다.

