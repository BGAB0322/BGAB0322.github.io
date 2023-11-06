---
layout:   post
title:    ROS2 Rviz2 실행시 error while loading shared libraries libQt5Core.so.5 에러 해결방법
category: ros
image:    /assets/img/blog/2022-12-29/thumnail_ros.png
---

* this unordered seed list will be replaced by toc as unordered list
{:toc}

-사양 확인-
* **Window WSL1**
* **Ubuntu 20.04 LTS**
* **ROS2 Galactic**

Rviz2를 실행하려고 하니 아래와 같이 에러가 계속 발생했습니다.

![그림1](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2022-12-29/ros2_rviz2_error_1.png?raw=true)

위와 같은 에러가 발생했다면 우선 패키지 업데이트 상태를 체크부터 해보시기 바랍니다.

~~~bash
sudo apt update && sudo apt upgrade
~~~

다시 Rviz2를 실행하기 전에 아래의 명령어를 입력해보시기 바랍니다.

## 에러 해결 명령어

~~~bash
sudo strip --remove-section=.note.ABI-tag /usr/lib/x86_64-linux-gnu/libQt5Core.so.5
~~~

정확한 원인으로 예상되는 내용을 찾을 수 없었지만 추측상 WSL1 버전에서 데비안(Debian) 버그로 보고 있습니다. 

혹자는 WSL2로 업그레이드하면서 해결했다는 사례도 있었습니다. 

추가적으로 Window11의 경우 WSL1이 좀 더 예측할 수 없는 에러가 많이 발생하는 것으로 보입니다. 기존에 Window10에서 해결했던 방법들이 Window11에서 똑같은 해결책으로 쓰이지 못하는 경우도 있었습니다.

여하튼 아래의 명령어를 입력하고 나서 다시 Rviz2를 실행해보도록 합니다.

~~~bash
sudo strip --remove-section=.note.ABI-tag /usr/lib/x86_64-linux-gnu/libQt5Core.so.5
~~~

![그림2](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2022-12-29/ros2_rviz2_error_2.png?raw=true)