---
layout:   post
title:    RESTful API란 무엇인가?
category: arduino
image:    /assets/img/blog/2023-11-15/thumbnail_arduino.png
---

* this unordered seed list will be replaced by toc as unordered list
{:toc}

RESTful API를 활용하여 클라이언트 또는 서버를 통해 데이터를 통일된 방식으로 주고 받을 수 있습니다. 저는 이러한 작업 과정이 하드웨어를  연동하여 로우 레벨에서 구성하다 보니 웹분야를 통해 알고 있는 흐름과 조금 다르더군요.

![그림1](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-11-15/restful_api_1.png?raw=true)

그래도 주요한 핵심을 알아두고 있는다면 조금 익숙해질 개념이라고 생각합니다.

## RESTful API란?

우선 API라는 용어부터 짚어봐야 할 것 같습니다.

* **API** 👉 **"Application Programming Interface의 약자로 애플리케이션이나 디바이스가 서로 간에 연결하여 통신할 수 있는 방법을 정의하는 규칙 세트"**

그럼 REST란 용어의 의미는 무엇일까요?

* **REST** 👉 **"REpresentational State Transer의 약자로 자원을 이름으로 구분하여 해당 자원의 상태를 주고 받는 모든 것"**

REST라는 API 작동 방식에 대한 조건을 부과하는 소프트웨어 아키텍처로 이러한 스타일을 따르는 API를 일컬어 'REST API'라고 부르며 REST 아키텍처를 구현하는 웹 서비스를 'RESTful API '라고 합니다. 

그런데 REST API와 RESTful API라는 용어는 같은 의미로 사용할 수 있습니다.

하드웨어에서 데이터 변환과 전달 중심으로 알고 있는 제게 REST API라는 개념이 직접 사용해보기 전까지는 그림이 잘 안그려졌는데요. REST를 조금 더 설계하는 입장에서 정리해보자면 아래와 같습니다.

* **HTTP URI를 통해 자원(Resource)를 명시한다.**
* **HTTP Method(GET, POST, PUT, DELETE 등)를 사용한다.**

이 2가지를 유념하면서 동작 메커니즘을 살펴보겠습니다.

## RESTful API 동작

저는 아두이노와 같은 ESP 보드를 구성해서 REST API를 구성하는 내용을 중점으로 한 번 살펴보려고 합니다.

![그림2](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-11-15/restful_api_2.png?raw=true)

아두이노 개발 모듈과 같은 하드웨어로 관점에서 살펴보면, Wi-Fi 모듈을 활용하여 클라이언트를 구성합니다. HTTP 메서드라 불리는 GET, PUT, POST, DELETE를 사용하여 특정 데이터를 JSON 형태로 변환하여 요청하는 겁니다.

그러면 아래와 같은 흐름이란 걸 알 수 있죠.

![그림3](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-11-15/restful_api_3.png?raw=true)

아두이노에서는 JSON 변환을 위해 StaticJSONDocument / DynamicJSONDocument를 사용하여 데이터를 직렬화(***serializeJson()***)를 통해 정수와 같은 데이터 타입부터 프로그래밍 언어를 특정 조건의 JSON 형태로 담아서 HTTP 메서드로 요청하면 플랫폼, 하드웨어, 프로그래밍 언어 등에 구애받지 않고 데이터를 주고 받을 수 있게 되는 것이죠.

~~~cpp
HTTPClient http_client;

http_client.POST(json);
http_client.PUT(json);
~~~

아두이노 IDE를 통해 코드를 사용한다면 라이브러리를 통해서 조금 더 쉽게 접근하는 것일테지만 더 로우 레벨로 접근하면 AT 커맨드를 아래와 같이 구성해야할 수도 있는 것이죠.

~~~cpp
// HEAD Request
AT+HTTPCLIENT=1,0,"http://httpbin.org/get","httpbin.org","/get",1

// GET Request
AT+HTTPCLIENT=2,0,"http://httpbin.org/get","httpbin.org","/get",1

// POST Request
AT+HTTPCLIENT=3,0,"http://httpbin.org/post","httpbin.org","/post",1,"field1=value1&field2=value2"
~~~

보통 웹개발이라면 이런 식의 접근으로 가지 않을 것이고, PostMan과 같은 특정 API를 통해서 key, value를 설정하여 접근하는 UI 형태가 익숙할 수도 있을 것 같습니다.

![그림4](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-11-15/restful_api_4.png?raw=true)

클라이언트로 이러한 요청이 발생하면 서버에서 데이터를 내부적으로 처리하여 특정 값을 반응(response)합니다. 클라이언트는 내부적으로 아래와 같이 서버로부터의 반응을 기다리고 있죠.

~~~cpp
if (http_response_code > 0)
{
    String response = http_client.getString();
    Serial.println(response);
}
else
{
    Serial.print("ERROR CODE : ");
    Serial.println(http_response_code);
}
~~~

![그림5](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-11-15/restful_api_5.png?raw=true)

개발의 방향에 따라서 디테일은 차이가 날테지만, HTTP를 통해서 특정 데이터를 JSON 형태로 다른 인터페이스 간에도 데이터를 송수신할 수 있다는 개념을 잊지 말아야 합니다.

## RESTful API 장점

* HTTP 표준 프로토콜을 따르는 모든 플랫폼에서 사용이 가능하기에 범용성/확장성을 보장합니다.
* 서버와 클라이언트의 역할을 명확하게 분리할 수 있습니다.
* REST API를 사용하면 HTTP 프로토콜 인프라를 그대로 활용하므로 별도의 인프라를 구축할 필요가 없습니다.

개발에 들어갈 서비스 디자인에서 생길 수 있는 문제를 공통의 규칙을 따름으로써 최소화할 수 있는게 RESTful API의 장점인 것 같습니다.

​---
공통된 규칙을 따른다는게 다른 플랫폼 간에 접근성을 얼마나 높일 수 있는지 알게 되죠. 

정리해보면 단순하게는 HTTP 프로토콜에서 언어, 플랫폼 차이를 극복하기 위한 공통적인 설계 방식이라고 개념을 그리고 있습니다.

웹과는 조금 다른 접근 관점이 있을 것 같아 참고에 도움이 되었으면 합니다.