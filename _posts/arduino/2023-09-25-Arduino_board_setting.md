---
layout:   post
title:    아두이노 | ESP8266 WiFi 보드 설정 방법
category: arduino
image:    /assets/img/blog/2023-09-25/thumnail_arduino.png
---

* this unordered seed list will be replaced by toc as unordered list
{:toc}

아두이노에서 WiFi 관련 기능을 사용할 수 있는 ESP 보드를 사용하는 경우 어떻게 사용하는지에 대하여 통합 개발 환경(IDE) 설정을 다뤄보도록 하겠습니다.

## ESP란?

ESP라는 이름이 들어가는 칩을 다양한 종류의 개발 보드로 나와있습니다. 아두이노에서 대표적인 ESP 칩은 'ESP8266'이라는 칩을 사용한 개발 보드일 것 같습니다.

![그림1](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-09-25/arduino_board_setting_1.png?raw=true)

ESP8266은 중국의 'Espressif Systems에서 생산하는 저가형 와이파이 마이크로 칩입니다. 저 또한 단순히 WiFi / TCP 통신을 통한 프로그래밍이 필요한 사이드 프로젝트를 위해 학교에서 조금 사용해보았던 것이 전부였습니다.

![그림2](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-09-25/arduino_board_setting_2.png?raw=true)

그런데 이게 현업에서 활용할 기회가 생기기도 해서 또다시 여러모로 흥미로운 나날이 이어지고 있습니다. 아두이노에서 이러한 Wi-Fi 모듈을 통합 개발 환경에서 사용하려면 초기 설정 한 가지가 필요합니다.

그와 관련한 내용을 간단히 설명해보도록 하겠습니다.

## 통합 개발 환경 설치(IDE) 및 ESP 개발 보드 추가

👉[아두이노 공식 다운로드](https://www.arduino.cc/en/software)

우선 위의 공식 사이트에 접속하여 본인에게 맞는 운영체제에 다운로드를 하도록 하겠습니다.

![그림3](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-09-25/arduino_board_setting_3.png?raw=true)

다운로드를 완료하고 인스톨러를 통해 설치까지 마무리하도록 하겠습니다. 다음은 아두이노 IDE를 실행하셔서 좌측 상단의 [File] 탭 → [Preferences] 탭을 클릭해주세요.

![그림4](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-09-25/arduino_board_setting_4.png?raw=true)

그럼 아래의 이미지와 같이 Preferences라는 이름의 UI 새창이 나타날 겁니다. UI 하단에 'Additional boards manager URLs란에 아래의 주소를 복사해서 입력해주도록 합니다.

~~~bash
https://arduino.esp8266.com/stable/package_esp8266com_index.json
~~~

![그림5](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-09-25/arduino_board_setting_5.png?raw=true)

주소를 입력하시고 'OK'를 클릭하시는 것까지 완료하시면, 이제 보드 선택에서 WiFi 관련 개발 보드가 추가되면서 아래의 이미지처럼 보드들을 선택이 가능하게 됩니다.

![그림6](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-09-25/arduino_board_setting_6.png?raw=true)

WiFi 개발 보드 설정은 이게 전부이며 필요에 따라서 추가적인 라이브러리는 '라이브러리 매니저'를 통해서 또는 직접 소스 파일을 찾아 추가하시면서 개발 의도에 맞게 접근하시면 됩니다.

직접 소스 파일을 추가하는 내용은 다른 포스팅에서 다루도록 하겠습니다.

## NodeMCU = Wi-Fi 모듈(ESP8266) + CPU

NodeMCU를 사용하여 테스트를 이것저것 해오면서 칩의 구성 요소에 대해 들여다 볼 기회가 생겼습니다.

NodeMCU를 PC에 연결했다고 가정하고, 아래의 사진을 참고하시기 바랍니다.

![그림7](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-09-25/arduino_board_setting_7.png?raw=true)

USB 컨버터의 존재 덕분에 우리는 보드에 코드 업로드를 쉽게 이루어지도록 하여 원하는 동작을 이끌어낼 수 있습니다. 중간에 레귤레이터를 통해서 전압을 조절하고 ESP-12E에 프로그램을 업로드하는 건데요. ESP-12E 칩이 AT 커맨드를, CPU를 통해서 코드를 업로드합니다.

사실 ESP-12E만 따로 소스를 업로드한다고 보면 NodeMCU가 회로적으로 얼마나 편리하고 간결하게 만들어진건지도 알 수 있게 되더군요.

만약 개발의 방향성이 NodeMCU를 사용하는게 아니라 요구에 따라 ESP-12E를 커스터마이징하여 사용하는 걸 원한다면? 

![그림8](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-09-25/arduino_board_setting_8.png?raw=true)

그럴수록 하드웨어의 구조를 이해하고, 새로운 부분의 코드를 발견하더라도 어떤 코드로 구성을 하여 움직일지 최대한 베이스를 다듬어가면서 나아가야 하죠. 모르는 부분이 있다면, 그리고 그게 자주 마주치는게 느껴지면 꼭 확실히 잡고 가는 습관이 필요한 것 같습니다.

뭐든 기본, 기초를 탄탄하게 잡는 것이 항상 1순위!