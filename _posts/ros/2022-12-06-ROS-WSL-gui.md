---
layout:   post
title:    Window 10에서 WSL / Ubuntu 설치 및 사용 방법
category: ros
image:    /assets/img/blog/2022-12-06/thumnail_ros.png
---

* this unordered seed list will be replaced by toc as unordered list
{:toc}

## Window 제어판 초기 설정

Window에서 WSL을 사용하기 위한 작업을 살펴보도록 하겠습니다.

우선 설치를 위한 설정 작업이 필요합니다. 제어판에 들어가셔서 [프로그램 및 기능]으로 들어갑니다.

![그림1](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2022-12-06/wsl_gui_1.png?raw=true)

왼쪽 상단의 메뉴를 살펴보시면 [Windows 기능 켜기/끄기]가 있습니다. 클릭해주시면 아래의 사진처럼 목록이 나열해 있는데 이 중 'Linux용 Windows 하위 시스템'을 체크하여 활성화시켜주세요. 

![그림2](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2022-12-06/wsl_gui_2.png?raw=true)

그러면 아래처럼 필요한 파일을 찾아서 설치를 합니다. 설치가 완료되면 재부팅을 안내합니다. 재부팅해주세요.

![그림3](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2022-12-06/wsl_gui_3.png?raw=true)

![그림4](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2022-12-06/wsl_gui_4.png?raw=true)

## 우분투(Ubuntu) 설치

윈도우 검색을 하시거나 직접 찾으셔서 Microsoft Store에 들어갑니다.

![그림5](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2022-12-06/wsl_gui_5.png?raw=true)

Microsoft Store에서 본인에게 맞는 우분투 버전을 설치하도록 합시다. 저는 이후의 터틀봇3 원격을 위해서 Foxy버전에 맞는 우분투 20.04 LTS를 설치하도록 하겠습니다.

![그림6](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2022-12-06/wsl_gui_6.png?raw=true)

ROS2 설치 버전 확인
{:.figcaption}

![그림7](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2022-12-06/wsl_gui_7.png?raw=true)

설치가 완료되면 실행해보도록 합니다. 그럼 '명령 프롬프트'처럼 검은 창이 하나 뜹니다.

![그림8](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2022-12-06/wsl_gui_8.png?raw=true)

설치가 완료되면 아래처럼 본인이 사용할 이름을 설정하라고 뜹니다. 저는 기본 이름으로 ubuntu라고 짓겠습니다.

![그림9](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2022-12-06/wsl_gui_9.png?raw=true)

이어서 비밀번호를 설정하라고 하는데 입력해도 커서 변화가 없을텐데 원래 리눅스 시스템이 그러하니 당황하지 마시고 본인이 원하는 비밀번호를 입력합니다.

![그림10](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2022-12-06/wsl_gui_10.png?raw=true)

정상적으로 완료했다면 아래처럼 Installation successful! 그리고 여러분이 사용하시는 우분투의 버전과 여러 상태 목록이 주륵 나열됩니다.

![그림11](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2022-12-06/wsl_gui_11.png?raw=true)

이렇게 윈도우 환경에서도 리눅스 OS, 우분투를 사용할 수 있도록 설치가 완료되었습니다.

![그림12](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2022-12-06/wsl_gui_12.png?raw=true)

## 우분투(Ubuntu) 설정 팁 - 붙여넣기 기능 추가하는 방법
우분투 창이 뜨면 위의 작업표시줄을 [마우스 우클릭] - [속성]을 클릭해줍니다.

![그림13](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2022-12-06/wsl_gui_13.png?raw=true)

간혹 명령어를 입력하기 보단 복사해야하는 경우가 있을텐데 이를 위한 설정으로 아래와 같이 [Ctrl + Shift + C/V를 복사/붙여넣기로 사용]에 체크를 해줍니다.

![그림14](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2022-12-06/wsl_gui_14.png?raw=true){:.centered}  

윈도우랑은 달리 Ctrl + C/V가 복사/붙여넣기 기능을 하기 위해 위와 같이 설정을 추가적으로 해주면 좀 더 유용하게 사용하실 수 있습니다.

---

## 에러 사례 1 - bash가 실행되지 않음(터미널 창은 열리지만 아무것도 안뜸)

WSL을 설치하고 다음날 부팅하여 다시 Ubuntu를 실행하니 업데이트하라고만 뜨고 아이디가 안 뜨길래 창을 닫고 다시 켜보니 터미널 창에서 아무것도 뜨지 않는 에러가 발생했습니다.

저는 WSL2가 아닌 WSL1을 원했는데 어쩔 수 없이 업데이트를 따랐는데 이때 충돌이 있었나 봅니다. 

그 때 이 방법을 해결 방안으로 참고하셨으면 합니다. 
<br>
<br>

### WSL의 버전을 고정하기

저의 경우 WSL1을 사용하고자 했는데 실수로 업데이트를 하고 이를 지우고 반복하는 과정에서 일이 꼬였습니다. WSL1으로 버전을 고정해서 사용하려면 우선 PowerShell을 관리자로 실행합니다.

![그림15](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2022-12-06/wsl_gui_15.png?raw=true)

PowerShell에서 아래와 같이 명령어를 입력하여 WSL1으로 버전을 유지할 수 있습니다. 이후 재부팅하여 실행해보면 정상적으로 작동하는 걸 확인하였습니다.

~~~js
wsl —set-default-version1
~~~

![그림16](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2022-12-06/wsl_gui_16.png?raw=true)

### 가상메모리 지정크기를 RAM에 맞춰 설정하기

![그림17](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2022-12-06/wsl_gui_17.png?raw=true){:.centered}  

[Window키 + R]로 실행창을 여시고 'SystemPropertiesAdvanced'를 입력하여 [시스템 속성]창을 엽니다.

![그림18](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2022-12-06/wsl_gui_18.png?raw=true)

1. 시스템 속성 창에서 [고급]탭에서 '성능'의 설정을 들어갑니다.
2. 성능 옵션 창에서는 [고급]탭에서 '가상메모리'의 변경을 들어갑니다.
3. 가상 메모리 창에서는 '모든 드라이브에 대한 페이징 파일 크기 자동 관리'에 체크를 해제하고 사용자 지정 크기를 직접 지정해줍니다.

***처음 크기 = 자신의 RAM 용량 X 1.5***<br>
***최대 크기 = 자신의 RAM 용량 X 2.0***<br>

으로 설정합니다. (RAM 용량을 확인하는 방법은 실행창에 dxdiag를 입력하셔서 확인할 수 있습니다.)

![그림19](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2022-12-06/wsl_gui_19.png?raw=true){:.centered}  

이후 재부팅하라는 메시지가 뜨는데 재부팅을 하시고 다시 우분투를 실행보시기 바랍니다.
