---
layout:   post
title:    Boost 라이브러리란 무엇인가 | Boost 라이브러리 윈도우 설치 및 사용 방법
category: c-lang
image:    /assets/img/blog/2023-11-22/thumbnail_c-lang.png
---

* this unordered seed list will be replaced by toc as unordered list
{:toc}

C++ 라이브러리 중 유용하면서도, 자주 마주치게 될 수 있는 라이브러리 중 하나가 바로 Boost 라이브러리입니다.

이번 글에서는 Boost 라이브러리를 소개하고자 합니다.

## Boost 라이브러리란?

![그림1](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-11-22/boost_library_1.png?raw=true)

Boost 라이브러리는 C++라는 프로그래밍 언어의 기능을 확장하고 고품질의 검토를 거친 오픈 소스 C++ 라이브러리 모음입니다.

오픈 소스라고 밝혔듯이 비용이 들지 않는데, C++를 사용하는 거의 모든 분야에서 범용적으로 사용할 수 있는지라 개발자가 하나부터 열까지 테스트를 거칠 필요가 없어 생산 효율도 높습니다.

Boost 라이브러리를 사용하는 것만으로도 앞의 수고를 상당 부분을 덜어내주니 엄청난 효율을 가져다주니 정말 많은 곳에서 이 라이브러리를 확인하실 수 있을 겁니다.

Boost 라이브러리 개발진 중 상당수가 C++ 표준 위원회에 속해 있어 C++ STL(표준 라이브러리, 'std::')이 Boost와 비슷한 구조를 많이 보입니다. Boost 라이브러리의 많은 기능과 컴포넌트가 채택되었죠.

여러 로보틱스 관련 제품군을 보시면 ROS 드라이버에서 많이 이용되니 ROS를 하시는 분들이라면 간혹 이 이름을 보셨을 수도 있을 겁니다.

## Boost 라이브러리의 구성과 특징

Boost 라이브러리는 오랜 역사를 가지고 있습니다. 가장 오래된 버전의 릴리즈(Release) 날짜가 1999년 12월입니다.

긴 역사 덕분인지 C++의 버전에 따른 기능과 여러 템플릿, 라이브러리만 하더라도 상상 이상의 분량을 자랑합니다. 

C++의 표준 라이브러리도 그러할지언데, Boost까지 채택에 들어간다면 상당히 개발자의 머리가 아파올 겁니다.

![그림2](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-11-22/boost_library_2.png?raw=true)

알고리즘, 병렬 프로그래밍, 컨테이너, 확인 및 테스트, 데이터 구조, 도메인, 고차 프로그래밍, 제네릭(Generic) 프로그래밍, 이미지 처리, 입출력, 반복자, 수학 및 산술 연산, 메모리, 파싱(Parsing), 패턴과 관용구, 전처리기 메타 프로그래밍, 시스템, 스트링과 텍스트 처리 등등 나열하기도 힘들 정도로 **광범위하게 라이브러리들을 포함**하고 있습니다.

그래서 로고를 보시면 Boost "Library"가 아니라 Boost "Libraries"로 표현하죠. 정말 다양한 라이브러리를 내포하는데 각각의 라이브러리 성능이 매우 높아 많은 부분에서 개발 구성을 차지할 수 있게 되는 것이죠.

C++ 개발의 방향을 여러 운영체제에서도 동작하도록 원활하게 솔루션을 제공하려고 코드를 구성한다고 가정해보겠습니다.

### 어느 범위까지 라이브러리를 활용할 것인가

Boost 라이브러리의 이식성이 어느 정도냐면, **Unix, Window 운영체제 뿐만 아니라 macOS 같은 거의 최신 운영체제에서도 동작**할 정도로 크로스 플랫폼에 대한 접근성이 매우 뛰어납니다.

그럼 네트워크를 Boost를 통해서 해결하기로 결정했다고 가정해보죠. 여기에 Window, Linux, macOS 운영체제에도 동작하도록 하겠습니다. 그런데 시간이 지나서 보니 특정 Boost 라이브러리가 업데이트를 해서 버전 문제로 변경할 필요가 있어 보입니다.

이렇게 되면 다른 운영체제에 작성한 소스 코드와 Boost 라이브러리 버전은 어떻게 할까요? 정상적으로 동작하려면 동일하게 업데이트가 필요합니다. 급하게 설정한 가정이지만 이런 식으로 종속적으로 변할 가능성도 고려해야 하죠.

### STL 라이브러리 + Boost 라이브러리 = 복잡 ☠☠☠

위와 같은 이유로 종속성을 줄이고자 한다면, **Boost 라이브러리는 Boost 라이브러리대로 STL 라이브러리는 STL 라이브러리대로 필요에 맞게 사용할 많은 요소들이 본인이 해야할 공부**로 돌아옵니다.

비슷해보여도 각각의 라이브러리에 맞게 새로운 기능과 개념이 존재합니다. 

C++ 참 어렵다, 어렵다하는데 이러한 생태계를 마주하고서야 새삼 느끼게 됩니다.

## 개발 설계에 주의가 필요✍✍✍

단점으로 이야기가 빠져 버렸지만 활용하기에 따라서 정말 효율적으로 생산성을 높일 수 있습니다. 기존 표준 라이브러리의 부족함을 보완해주고 있는게 Boost 라이브러리의 존재입니다.

통합의 용이성과 함께 높은 품질과 견고성, 안정성을 갖춘 라이브러리입니다. 

시간과 효율을 따져보고 기간이 넉넉하지 않다면 일부 소스에 채택하여 안정적인 성능을 끌어낼 수도 있고, 상황에 따라 크로스 플랫폼에 비중이 실리고 Boost 라이브러리의 종속성을 줄이고 싶다면 개발 들어가기 앞서 설계에 주의가 필요할 것으로 보입니다.

## Boost 라이브러리 Window 설치
윈도우에서 Boost 라이브러리를 설치하여 Visual Studio에서 사용하는 방법까지 알아보겠습니다.

우선 아래의 공식 홈페이지에서 라이브러리를 다운로드 받도록 합니다.

👉[Boost 라이브러리 공식 다운로드 홈페이지](https://www.boost.org/users/download/)

![그림3](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-11-22/boost_library_3.png?raw=true)

라이브러리는 압축 파일로 되어 있으며, 제가 다운로드 받은 버전 1.82버전입니다.

압축 해제하고 'boost_1_82_0'폴더는 제 바탕화면에 있는 Utils라는 이름의 폴더에 따로 두겠습니다. 이제 Visual Studio에서 해당 라이브러리를 연동해보도록 하겠습니다.

![그림4](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-11-22/boost_library_4.png?raw=true)

간단히 헤더 연동만 보도록 Console App 프로젝트 하나를 생성하도록 하겠습니다.

![그림5](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-11-22/boost_library_5.png?raw=true)

프로젝트 생성 후 상단의 프로젝트(Project) > {프로젝트 이름} 속성(Properties) > 구성 속성(Configuration Properties) > C/C++ > 일반(General) > 추가 포함 디렉터리(Additional Include Directories)에 boost 라이브러리를 설치한 경로를 입력합니다. 

아래의 이미지를 참조하세요.

![그림6](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-11-22/boost_library_6.png?raw=true)

![그림7](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-11-22/boost_library_7.png?raw=true)

저는 바탕화면에 Utils라는 폴더에 라이브러리를 둔 상황입니다. 그에 맞춰서 경로를 설정한 것입니다.

이어서 코드에서 boost관련 헤더를 입력해보니 목록이 뜨는 것을 확인할 수 있습니다.

![그림8](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-11-22/boost_library_8.png?raw=true)

이를 토대로 boost 관련 테스트 코드를 작성해보시길 바랍니다.