---
layout:   post
title:    Window10에서 Virtual Box / Ubuntu 설치 및 사용 방법
category: linux
image:    /assets/img/blog/2024-05-31/thumnail_linux.png
description: >
  How to install Virtual Box and Ubuntu in Virtual Box
---

* this unordered seed list will be replaced by toc as unordered list
{:toc}

## Virtual Box와 Ubuntu OS 설치 준비
Window10 운영체제에서 가상환경을 사용한 리눅스 운영체제를 사용하는 방법인 Virtual Box 설치와 그 과정을 살펴보도록 하겠습니다.

👉[Virtual Box 다운로드 공식 홈페이지](https://www.virtualbox.org/wiki/Downloads)

👉[Ubuntu 22.04 PC Desktop iso 다운로드 공식 홈페이지](https://releases.ubuntu.com/jammy/)

해당 글에서는 Virtual Box 설치와 해당 가상환경에서 Ubuntu를 설치하는 과정까지 다루어보려고 합니다. 리눅스 시스템을 윈도우에서 사용하기 위한 방법으로 WSL을 통한 Ubuntu를 사용을 다뤘었습니다.

그런데 WSL 특성상 윈도우에서 부팅이 되기 때문에 리눅스 시스템에서 컴퓨터를 부팅하고 모든 프로세스를 관리하는 시스템인 systemd 프로세스가 존재하지 않아 'systemctl' 관련 명령어를 사용하지 못하는 등 완벽하게 리눅스 환경을 따라 사용하긴 제한적인 부분들이 존재했습니다.

그런데 Virtual Box를 사용한다면, 가상의 CPU / 메모리 / 하드디스크 / 네트워크 등을 구현하여 가상 컴퓨터 장치 상에서 OS를 올려 운영하는 방식을 취하여 호스트 머신(ex. 자신의 PC)와 완전히 분리된 환경을 만들어 서버 운영 및 기타 환경 구축 개발을 다루는데 용이한 점이 있습니다.

WSL 대신에 Virtual Box도 한 번 활용해보시기 바랍니다.

우선 상단의 두 홈페이지에서 Virtual Box와 Ubuntu 22.04 버전의 iso 이미지 파일을 다운로드 받도록 합니다.

### 1. Virtual Box 다운로드 및 설치

![버추얼박스 다운로드 웹페이지](/assets/img/blog/2024-05-31/virtual_box_1.png?raw=true)

Virtual Box 다운로드 공식 홈페이지를 들어가시면 위와 같은 페이지를 보시게 될텐데 'Windows hosts'를 클릭하셔서 아래와 같이 윈도우에서 설치, 사용 가능한 파일을 다운로드 받도록 합니다.

<p align="center"><img src="https://github.com/user-attachments/assets/1bcfb09b-68c8-4681-89aa-ec745b712275"></p>

다운로드 파일을 실행하셔서 설치를 진행해주도록 합니다.

<p align="center"><img src="https://github.com/user-attachments/assets/1744701b-343e-446d-8dfc-de025795a5c4"></p>

저는 Default로 설정된 조건으로 계속해서 넘기고 설치를 진행했습니다.

![버추얼박스 인스톨러 설치 진행 과정](/assets/img/blog/2024-05-31/virtual_box_4.png?raw=true)

설치를 마무리하고 실행하면 아래와 같이 Virtual Box Manager 실행창이 뜹니다. 

![버추얼박스 매니저 창](/assets/img/blog/2024-05-31/virtual_box_5.png?raw=true)

실행창까지 오셨다면 우선 Virtual Box 설치는 벌써 끝난 겁니다.

이제 Virtual Box에 Ubuntu 이미지 파일을 올려서 리눅스 시스템을 구성해봅시다.

### 2. Ubuntu 이미지 다운로드와 Virtual Box에 Ubuntu 환경 설정

위에서 참조한 'Ubuntu 22.04 PC Desktop iso 다운로드 공식 홈페이지'에 들어가면 아래와 같은 웹사이트로 이동할 겁니다. 상단의 Desktop image 창에 있는 desktop image를 클릭합니다. 여기서 Ubuntu 22.04 버전의 PC용 이미지 iso를 다운로드 받도록 합시다. <u>용량은 4.6GB</u> 정도됩니다. (파일명 : ubuntu-22.04.4-desktop-amd64.iso)

![Ubuntu iso 다운로드 웹페이지](/assets/img/blog/2024-05-31/virtual_box_6.png?raw=true)

iso 이미지 파일까지 설치를 완료했다면 이제 Virtual Box Manager로 다시 가보겠습니다.

우측 상단의 '새로 만들기(New)' 버튼을 클릭하셔서 Virtual Machine을 생성하도록 하겠습니다. 그럼 아래와 같은 새로운 창이 뜰텐데요. ISO image 탭에는 본인이 다운로드 받은 iso 경로를 찾아 iso 이미지를 지정해주시면 됩니다.

이름은 저의 경우 '운영체제 이름 + 버전'으로 지정해둡니다. 그리고 <u>**가장 중요한 점!**</u>

꼭 하단의 'Skip Unattended Installation'에 체크를 해두시길 바랍니다. 이걸 안 해두면 Ubuntu 설치 진행 과정에서 <u>터미널이 실행이 안 되는 문제가 발생</u>하기도 합니다. 꼭 체크해두고 넘어가시기 바랍니다.

![버추얼박스 iso 경로설정](/assets/img/blog/2024-05-31/virtual_box_7.png?raw=true)

이어서 Virtual Machine에 대한 메모리, 디스크 용량 등의 PC 설정을 진행합니다. PC 사양이 괜찮다면, 그리고 진행할 프로젝트의 연산이 크다든지 저의 경우 차후 진행할 2D/3D SLAM을 고려한다면 메모리, 용량 등을 크게 잡고 하시길 바랍니다.

나중에 수정이 된다고는 하는데 Virtual Box에서 수정해도 내부적으로 추가적인 명령어를 통한 수정이 필요한데, 저는 이게 예기치 못한 오류만 잔뜩 일으켜서 다시 갈아엎고 새로 설치한 경우가 많았습니다.(오히려 이게 빠른 경우가 많았습니다..) 

그러니 <u>1. 메모리, 2. 하드 디스크 크기는 꼭 넉넉하게 확보하길 권장</u>합니다.

![버추얼박스 메모리, 프로세서 선택](/assets/img/blog/2024-05-31/virtual_box_8.png?raw=true)
![버추얼박스 디스크 용량 선택](/assets/img/blog/2024-05-31/virtual_box_9.png?raw=true)

저는 최종적으로 아래와 같이 설정을 마무리하도록 하겠습니다.

![버추얼박스 설정 최종](/assets/img/blog/2024-05-31/virtual_box_10.png?raw=true)

Finish 클릭과 함께 좌측 Tools 하단에 여러분이 지정한 이름으로 가상환경이 뜨게 될겁니다. 이걸 더블 클릭해주시면 검은색 새로운 창과 함께 Ubuntu OS 설치 진행을 묻습니다.

![버추얼박스 OS 설치 시작](/assets/img/blog/2024-05-31/virtual_box_11.png?raw=true)

엔터를 입력하시면 Ubuntu를 아시는 분이라면 봤을 법한 GUI와 함께 설치 진행창이 뜹니다.

![Ubuntu OS 설치](/assets/img/blog/2024-05-31/virtual_box_12.png?raw=true)

기본 설정 조건으로 해서 계속해서 넘어가셔서 설치를 진행하시면 됩니다.

![업데이트 및 기타 소프트웨어](/assets/img/blog/2024-05-31/virtual_box_13.png?raw=true)

![설치형식](/assets/img/blog/2024-05-31/virtual_box_14.png?raw=true)

![Ubuntu 설치 진행](/assets/img/blog/2024-05-31/virtual_box_15.png?raw=true)

이제 설치를 마무리하고 재부팅을 안내하면 재부팅합니다.

터미널도 잘 실행되고 이전 글에서 WSL에서 다뤘던 dmesg 명령어를 입력해보면 아래와 Virtual Box에서는 아래와 같이 PC를 통해 실행한 것처럼 Boot부터 커널 단의 이력들을 다 확인하실 수 있습니다.

![VirtualBox dmesg확인](/assets/img/blog/2024-05-31/virtual_box_16.png?raw=true)

WSL로 설치한 Ubuntu를 살펴보면 동일한 명령어임에도 아래와 같이 뜹니다.

![WSL dmesg확인](/assets/img/blog/2024-05-31/virtual_box_17.png?raw=true)

우선 이렇게 해서 Ubuntu 22.04 버전을 VirtualBox에 설치까지 완료가 된 겁니다.

이후는 본인의 상황에 맞춰서 설치 및 업데이트를 진행하면 됩니다. 저는 해당 환경에서 ROS를 사용하는 여러 내용들을 다뤄보려고 하니 많은 관심 부탁드립니다!