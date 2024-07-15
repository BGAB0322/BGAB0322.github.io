---
layout:   post
title:    시리얼 통신, 포트 연결 확인 및 고장 체크하기
category: linux
image:    /assets/img/blog/2023-02-14/thumnail_linux.png
---

* this unordered seed list will be replaced by toc as unordered list
{:toc}

윈도우 운영체제와 달리 Ubuntu라는 Unix 계열의 운영체제가 처음이라면 CLI 환경 작업은 USB 확인조차 어떻게 이루어지는지 알기 어렵거나 어색할 수도 있습니다. 

데스크톱이라면 그나마 마우스로 드래그하는 친숙한 GUI 환경을 통해 확인할 수 있습니다. 하지만 서버를 사용하고 있거나, 터미널을 통해서만 확인해야 하는 상황이 많을 수 있는 것이 유닉스 계열 운영체제의 시스템이기에 특정 상황에서 사용하는 명령어와 일련의 방법들은 숙지해 둘 필요가 있습니다.

## dmesg(diagnostic message)

일전의 Jeston Nano Dev kit에서 커널 부팅 확인했던 것처럼, 시스템의 커널 부팅 메시지를 확인할 수 있는 명령어입니다. 커널에서 출력되는 메세지를 일정 수준 기록하는 버퍼 역할을 수행하며, 커널 부팅 중 에러가 났다면 어느 단계에서 에러가 났는지도 확인할 수 있습니다. 

시스템에서 일어나는 메세지들은 여길 통해서 확인할 수 있는데 그 중에서 포트 연결을 확인하는 범주로 볼 것입니다.

Ubuntu에서는 포트에 장치 이름을 갖게 되는데 '/dev/tty'와 같은 형태로 갖습니다. 그래서 키워드를 'tty'로 잡고 커맨드를 입력해보겠습니다. 본인이 사용할 포트의 고유한 드라이버가 있을 겁니다.

~~~bash
dmesg | grep tty
~~~

![그림1](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-02-14/serial_port_check_1.png?raw=true)

저의 경우 드라이버 이름이 'PL2303'이며 위와 같이 'ttyUSB0'라는 이름으로 연결을 확인했습니다. 상세 내용은 'lsusb'를 입력함으로써 확인하실 수 있습니다.

~~~bash
lsusb
~~~

![그림2](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-02-14/serial_port_check_2.png?raw=true)

## 시리얼 통신 모니터 - minicom

윈도우에선 SerialPortMon같은 에뮬레이터들을 사용하여 시리얼 통신을 확인했다면 Ubuntu에서는 minicom을 사용하고 있습니다. minicom 설치를 안했다면 아래의 커맨드를 입력하여 설치를 진행하시길 바랍니다.

~~~bash
sudo apt install minicom
~~~

![그림3](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-02-14/serial_port_check_3.png?raw=true)

설치를 완료하고 나면 minicom을 실행하기 위해 아래의 명령어를 입력하시면 바로 아래의 사진처럼 특정 위치에 메뉴창 하나가 뜰겁니다.

~~~bash
sudo minicom -s
~~~

![그림4](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-02-14/serial_port_check_4.png?raw=true)

3번째 메뉴인 [Serial port setup]에 커서를 두고 엔터를 입력하시면 아래와 같은 설정창을 볼 수 있습니다.

![그림5](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-02-14/serial_port_check_5.png?raw=true)

본인의 시리얼 포트 장치에 맞게 내용을 바꿔줄 겁니다. 제가 사용한 제품의 USB장치는 위에서 확인했듯이 시리얼 장치는 /dev/ttyUSB0로 연결됐습니다. 만약 장치의 보레이트가 3,000,000bps으로 맞춰야 한다고 가정하고 이에 맞춰 설정을 변경하겠습니다.

방법은 초기에는 커서가 Change which setting? 옆에서 깜박 거립니다. 'a'부터 'g'까지 대/소문자 상관없이 키보드 입력을 해보시면 해당 목록으로 커서가 이동합니다.

'a'를 누르면 아래 사진처럼 첫 번째의 Serial Device로 이동하고 /dev/modem을 지우고 /dev/ttyUSB0를 입력하고 엔터를 치면 다시 초기 위치로 커서가 돌아갑니다.

![그림6](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-02-14/serial_port_check_6.png?raw=true)

이런 식으로 필요한 설정을 변경합니다. 'e'는 보레이트, stop 비트를 수정할 수 있는데 본인에게 맞는 보레이트가 리스트에 없거나 Current에 값을 'a' 또는 'b'키를 입력해서 본인에게 맞는 값을 찾아 넘기면 됩니다.

![그림7](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-02-14/serial_port_check_7.png?raw=true)

계속해서 목록을 넘기다 보니 3,000,000이 나왔군요. 엔터를 치고 설정으로 돌아옵니다.

![그림8](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-02-14/serial_port_check_8.png?raw=true)

나머지 조건도 이런 식으로 변경을 마치고 엔터를 쳐서 제일 처음의 리스트로 돌아옵니다.

![그림9](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-02-14/serial_port_check_9.png?raw=true)

지금까지 수정했던 설정을 유지하고 싶으면 'Save setup as dfl(default)'에 커서를 엔터를 누르시면 본 설정이 기본 설정으로 저장됩니다. 

![그림10](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-02-14/serial_port_check_10.png?raw=true)

여기서 [Exit] 탭으로 가서 엔터를 쳐서 모니터로 돌아갑시다.

![그림11](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-02-14/serial_port_check_11.png?raw=true)

![그림12](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-02-14/serial_port_check_12.png?raw=true)

그럼 위와 같은 창이 뜨는데 위의 창이 통신이 이루어지는 송수신 값을 확인하실 수 있습니다.

이를 이용해서 물리적으로 시리얼 통신 케이블의 고장 유무를 확인할 수도 있습니다. TX와 RX를 같이 연결하셔서 키보드를 입력해보면 송수신(TX/RX)이 동시에 이루어지므로 키입력이 2개 연속으로 뜨는 것으로 체크할 수 있습니다.

참고하시기 바랍니다!