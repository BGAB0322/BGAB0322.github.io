---
layout:   post
title:    OpenCV 라이브러리란 무엇인가 | OpenCV 라이브러리 윈도우 설치 및 사용 방법
category: c-lang
image:    /assets/img/blog/2024-02-27/thumbnail_c-lang.png
---

* this unordered seed list will be replaced by toc as unordered list
{:toc}

OpenCV(Open Source Computer Vision)는 강력한 이미지 처리 기능과 함께 머신 러닝도 활용이 가능한 오픈 소스 라이브러리입니다. 

C++, Python, Java 등 다양한 프로그래밍 언어에도 사용되며, 해당 라이브러리를 활용하여 이미지나 동영상에 물체검출, 특징추출, 패턴인식, 얼굴 인식 등의 기능을 구현할 수 있습니다.

![그림1](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-02-27/OpenCV_library_1.png?raw=true)

새로운 OpenCV 알고리즘 개발은 대부분 C++를 기준으로 개발되고 있으며, 언어의 다양성 뿐만 아니라 OS, 하드웨어 CPU까지 다양한 곳에서 적용이 가능하다는게 OpenCV의 무서운 확장성으로 보입니다.

ROS 관련 프로젝트를 진행하시는 분들이라면 OpenCV를 접해본 경험이 있을 겁니다. 오픈소스답게 국내외의 다양한 예제와 단순히 파일을 사용함으로써 검출, 추출, 인식 기능을 구현하기에도 유용한게 활용하기 나름인 라이브러리인 듯 합니다.

## Window에서 설치

👉[OpenCV 라이브러리 공식 다운로드 홈페이지](https://opencv.org/releases/)

우선 공식 사이트에서 다운로드를 하도록 합니다. 위의 Releases로 이동하셔서 Window 용 라이브러리를 다운로드 하겠습니다. 

![그림2](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-02-27/OpenCV_library_2.png?raw=true)

저는 아래처럼 4.8.0버전으로 다운로드를 진행하겠습니다.

![그림3](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-02-27/OpenCV_library_3.png?raw=true)

실행 파일 형태로 다운로드를 받습니다.

![그림4](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-02-27/OpenCV_library_4.png?raw=true)

다운로드 받은 파일을 실행해 주도록 합니다.

![그림5](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-02-27/OpenCV_library_5.png?raw=true)

추출 원하는 경로를 지정합니다. 라이브러리, 헤더 등의 파일을 설치합니다.

![그림6](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-02-27/OpenCV_library_6.png?raw=true)

opencv라는 이름으로 폴더를 생성하였습니다.

![그림7](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-02-27/OpenCV_library_7.png?raw=true)

이제 비주얼 스튜디오에서 빌드하는 방법을 살펴보겠습니다.

## Visual Studio 적용 / 빌드 방법

![그림8](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-02-27/OpenCV_library_8.png?raw=true)

빈 프로젝트를 생성하도록 하겠습니다.

![그림9](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-02-27/OpenCV_library_9.png?raw=true)

프로젝트에서 [디버그 Debug] → [디버그 속성 Debug Properties]을 클릭하여 속성 창을 열어줍니다.

![그림10](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-02-27/OpenCV_library_10.png?raw=true)

[구성 속성 Configuration Properties] → [C / C++] → [일반 General] → [추가 포함 디렉터리 Additional Include Directories] 순으로 이동합니다.

![그림11](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-02-27/OpenCV_library_11.png?raw=true)

저의 경우 아래와 같은 경로로 include 폴더가 있습니다.

![그림12](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-02-27/OpenCV_library_12.png?raw=true)

아래와 같이 작성해 include 경로를 설정했습니다.

![그림13](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-02-27/OpenCV_library_13.png?raw=true)

다음은 라이브러리 경로를 설정합니다. [구성 속성 Configuration Properties] → [링커 Linker] → [추가 라이브러리 디렉터리 Additional Library Directories]순입니다.

![그림14](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-02-27/OpenCV_library_14.png?raw=true)

아래와 같은 경로로 lib 경로가 존재합니다. 해당 경로를 복사합니다.

![그림15](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-02-27/OpenCV_library_15.png?raw=true)

아래와 같이 붙여 넣어줍니다.

![그림16](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-02-27/OpenCV_library_16.png?raw=true)

그 다음은 [구성 속성 Configuration Properties] → [디버깅 Debugging] → [환경 Enviroment]순입니다. PATH=저장경로\opencv\build\x64\vc16\bin;%PATH%과 같은 방식으로 입력합니다.

![그림17](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-02-27/OpenCV_library_17.png?raw=true)

저의 기준으로 보면 아래와 같이 입력합니다.

~~~bash
PATH=C:\Users\user\Downloads\opencv\build\x64\vc16\bin;%PATH%
~~~

![그림18](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-02-27/OpenCV_library_18.png?raw=true)

마지막입니다. [구성 속성 Configuration Properties] → [링커 Linker] → [입력 Input] → [추가 종속성 Additional Dependency] 순으로 이동합니다.

![그림19](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-02-27/OpenCV_library_19.png?raw=true)

우측의 아래 화살표를 클릭하고 <Edit...> 를 클릭해줍니다.

![그림20](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-02-27/OpenCV_library_20.png?raw=true)

라이브러리 파일을 지정해주는 Debug모드라면 opencv_world480d.lib를 release 모드라면 d가 빠진 lib파일을 입력합니다.

![그림21](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-02-27/OpenCV_library_21.png?raw=true)

저는 디버그 모드이므로 아래와 같이 파일을 입력합니다.

![그림22](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-02-27/OpenCV_library_22.png?raw=true)

여기까지 오셨으면 모든 설정은 완료한 겁니다.

## OpenCV 코드 include 추가해보기

이제 다시 코드로 넘어가봅시다. include 헤더파일을 작성해 보면 opencv가 뜨는 걸 확인할 수 있구요.

![그림23](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-02-27/OpenCV_library_23.png?raw=true)

opencv.hpp를 include하고 빌드해보니 정상적으로 빌드도 됩니다.

![그림24](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-02-27/OpenCV_library_24.png?raw=true)

---

영상처리와 관련한 라이브러리 중에서도 OpenCV를 짧게 다뤄보았습니다. 회사에서는 여건에 맞게(특히 라이센스 비용 문제) 라이브러리가 사용될텐데, OpenCV 외에도 Cognex, HALCON 등의 비전 라이브러리가 있는데 영상 처리를 시작함에 있어 OpenCV는 훌륭한 라이브러리임에는 분명합니다.

처음 개발이 진행된게 Intel에 의해서 1998년도에 개발된 라이브러리다 보니 다양한 참고 자료와 그동안의 변화가 많기도 합니다.

그래서 OpenCV를 활용한 예제들도 천천히 하나씩 다뤄보도록 하겠습니다.