---
layout:   post
title:    ROS2 [ERROR]: PluginlibFactory: The plugin for class 'rviz_common/Time' failed to load. 해결 방법
category: ros
image:    /assets/img/blog/2023-06-07/thumnail_ros.png
---

* this unordered seed list will be replaced by toc as unordered list
{:toc}

-사양 확인-
* **Window WSL1**
* **Ubuntu 20.04 LTS**
* **ROS2 Foxy**

코드를 수정하면서 테스트를 하던 와중에 Rviz2를 실행하니 아래와 같은 에러가 발생하기 시작했습니다.

![그림1](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-06-07/ros2_plugin_fail_1.png?raw=true)

무엇이 원인이었는지 찾다보니 결국은 해답을 찾았습니다. 평소에 사용하질 않으니 잊고 있던 파일의 존재였죠.

## default.rviz 파일에 사용할 수 없는 코드를 저장한 것이 원인

Rviz2를 실행하면 기본적으로 HOME/.rviz2(~/.rviz2) 디렉토리에 있는 'default.rviz'를 통해서 창이 실행되는데, default.rviz로 켜둔 Rviz2에서 특정 설정을 변경하게 되면?

보통 Rviz2 종료를 하게 되면, 현재 상태에 대한 내용을 저장할지 물어봅니다. **defulat.rviz에 변경된 사항을 저장하면서 저장된 내용으로 실행이 이루어지면서 발생**한 문제였습니다.

해당 에러 코드는 아래와 같으며, Foxy에서 이용 가능한 패널이 아닌데 default에 저장되면서 문제가 생겼습니다. 제 기억으로 다른 패키지를 막 섞어 사용하다가 설정이 따라간 것으로 보입니다.

~~~bash
- Class: rviz_common/Time
    Experimental: false
    Name: Time
    SyncMode: 0
    SyncSource: ""
~~~


default.rviz 파일에서 패널 목록에 있는 상단의 코드를 지우고 다시 Rviz2를 실행해보니 실행 간에 떴던 에러 코드가 해결되었습니다.

![그림2](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-06-07/ros2_plugin_fail_2.png?raw=true)

은근하게 '.rviz'파일의 존재를 잊고 있을 때 문제가 간혹 발생합니다. 보통 코드 수정은 소스 코드(C/C++/Python)나 launch 파일에서 주로 하다보니 단서를 놓치는 경우가 있습니다. 특히 Rviz 출력에 에러가 해결 안 되는 경우 .rviz의 내용도 의심해보시기 바랍니다.

👉[해당 에러 해결 참조 사이트](https://github.com/ros2/rviz/issues/679)