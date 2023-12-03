---
layout:   post
title:    ESP8266 HTTP 서버 예제
category: arduino
image:    /assets/img/blog/2023-12-03/thumbnail_arduino.png
---

* this unordered seed list will be replaced by toc as unordered list
{:toc}

이번 포스팅은 ESP8266에서 HTTP POST 요청을 하는 예제 코드를 소개와 함께 리뷰하도록 해보겠습니다. 아래는 제가 설명에 사용할 코드의 레포지토리입니다. 다운로드 받아 전체 소스 코드를 확인하실 수 있습니다.

👉[Arduino HTTP Server Example](https://github.com/BGAB0322/esp8266_http_server_example)

## RESTful API 아두이노 서버 예제 개요

👉[RESTful API란?](https://bgab0322.github.io/blog/arduino/2023-11-15-restful_api/)

이 예제 HTTP 서버를 사용하기 위해서는 아래의 흐름으로 동작한다고 생각하시면 됩니다.

* **WiFi 스캔하여 IP 할당(예제는 고정 IP를 지정하는 방식)**
* **내부 데이터를 구조체로 선언하고, 클라이언트의 요청으로 JSON 데이터 수신**
* **클라이언트의 HTTP 메소드 요청에 따라 서버 핸들러 함수를 실행**
* **POST 요청은 JSON 데이터를 변환하여(deserialize) 구조체 변수에 입력하고 클라이언트에는 반응상태 코드(RESPONSE STATUS CODE)를 전달**
**(핸들러 GET 요청은 현재 데이터들의 값이 무엇인지 알 수 있도록 추가함)**

HTTP 클라이언트 요청에 따라 데이터를 변환 및 입력해두는, HTTP 서버 예제 코드 리뷰입니다. 클라이언트 요청 리소스와 데이터는 이전 클라이언트 예제 포스팅을 이어지는 글입니다.

클라이언트와 연관성을 보시려면 이전 글을 참고해주시기 바랍니다.

👉[Arduino HTTP Client Example](https://bgab0322.github.io/blog/arduino/2023-11-30-arduino_http_client/)

클라이언트가 GET 메서드 요청을 보내면 구조체에 입력된 데이터를 JSON으로 변환하여(serialize) 전달합니다.

클라이언트가 POST 메서드 요청을 보내면 수신한 JSON 데이터를 변환하여(deserialize) 구조체 특정 변수에 입력합니다.

이 큰 흐름을 인지하고 코드를 보면서 연습해보면 될 것 같습니다.

## Library & Header

~~~cpp
#include <ESP8266WiFi.h>
#include <ESP8266WebServer.h>
#include <ArduinoJson.h>
~~~

헤더파일은 3가지를 사용합니다. 

* ESP8266WiFi.h는 ESP8266 계열 모듈들이 WiFi.begin(), WiFi.status() 등 WiFi 연결에 대응하는 기능들을 사용할 수 있다고 보시면 됩니다. 클라이언트와 달리 mode() 함수로 모드를 택하고 config()를 통해서 고정 IP 할당 등이 추가됩니다.
* ESP8266WebServer.h는 HTTP 서버 동작과 관련한 헤더인데, on() 함수에 콜백함수와 HTTP 메서드를 연결하여 클라이언트 요청에 맞는 반응(RESPONSE)을 구성할 수 있습니다. handleclient() 함수로 클라이언트 요청에 따라서 on() 함수에 할당한 내용에 따라 함수를 실행합니다.
* ArduinoJson.h은 JSON 형태의 데이터로 변환하기 위해 필요한 라이브러리입니다.

이렇게 3가지로 구성하여 HTTP 서버를 설정하여 클라이언트 요청에 따른 데이터 처리를 할 것입니다.

~~~cpp
#define PORT_NUMBER 80

const char* SSID   = "your wifi ssid"; 
const char* PASSWD = "your wifi password";

IPAddress IP_ADDRESS(192, 168, 0, 0);
IPAddress GATE_WAY(192, 168, 0, 1);
IPAddress SUBNET_MASK(255, 255, 255, 0);

ESP8266WebServer http_server(PORT_NUMBER);

String TOTAL_RESOURCES   = "/projects/http_client/data_types/total_data";
String INTEGER_RESOURCES = "/projects/http_client/data_types/integer";
String ARRAY_RESOURCES   = "/projects/http_client/data_types/array";

uint8_t retries;

struct server_parameter
{
    uint8_t one_data;
    uint8_t array_data[15];
} server_param;
~~~

SSID와 PASSWD는 여러분이 사용하시려는 WiFi 공유기의 SSID(WiFi이름), 비밀번호에 맞춰 수정하시면 됩니다.

IPAddress 클래스를 이용해서 특정 ip를 할당하여 생성자를 만들 수 있습니다. 정의는 아래와 같습니다.

~~~cpp
IPAddress::IPAddress(uint8_t first_octet, uint8_t second_octet, uint8_t third_octet, uint8_t fourth_octet)	
~~~

그래서 3가지 ip주소, 아두이노 IP / 게이트웨이 / 서브넷마스크를 선언합니다.

서버 클래스는 ESP8266WebServer이며, 인자는 포트 번호를 할당합니다. PORT_NUMBER = 80으로 입력한 내용입니다. 리소스 문자열은 이전 클라이언트 POST 예제에서 POST 요청 이후 수정된 데이터를 확인할 수 있도록 GET 요청에 대한 핸들러도 추가하였습니다.

그래서 정수형 데이터 하나, 정수형 배열 하나를 POST 요청하던 내용에 맞춰 데이터를 입력해두도록 구조체를 선언하고, GET 요청시 이 두 가지 데이터를 한 번에 확인할 수 있도록 하였습니다.

## Setup - WiFi / HTTP Server

다음은 setup()을 살펴보도록 하겠습니다. WiFi 설정과 관련한 내용을 작성하였습니다.

~~~cpp
void setup()
{
    Serial.begin(115200);
    Serial.println("Connecting to Wi-Fi AP...");

    WiFi.mode(WIFI_STA);    
    WiFi.config(IP_ADDRESS, GATE_WAY, SUBNET_MASK);
    WiFi.begin(SSID, PASSWD);

    initParameter();

    while (WiFi.status() != WL_CONNECTED)
    {
        delay(1000);

        Serial.print(".");
        retries++;

        if (retries > 50)
        {
            Serial.print("[ERROR] Failed to Connect ");
            Serial.println(SSID);

            EspClass esp_module;
            esp_module.restart();
        }       
        else if (WiFi.status() == WL_CONNECTED)
        {
            Serial.print("Connected to ");
            Serial.print(SSID);
            Serial.print("--- IP: ");
            Serial.println(WiFi.localIP());
            retries = 0;
        }
    }    
}
~~~

시리얼 통신은 115200으로 설정했습니다. 시리얼 모니터도 이 값으로 변경해야 출력을 제대로 확인할 수 있습니다. 

클라이언트는 begin() 함수로 끝나지만 서버는 조금 더 내용이 추가되었습니다.

~~~cpp
WiFi.mode(WIFI_STA);
~~~

우선 AP라는 용어부터 살펴봐야 합니다. AP는 Acess Point의 줄임말로 간단하게 생각하면 여러분이 연결하려는 WiFi 공유기에 해당한다고 보시면 됩니다. 다른 장치가 접속(Access)할 수 있는 곳(Point)인 것이죠.

여기서 WIFI_STA는 STA 모드에 해당하는 값으로 AP(WiFi공유기)에서 송신하는 데이터 신호들을 받을 수 있는 모드인 Station Mode로 설정하였습니다. 설명을 덧붙여 AP모드와 STA+AP모드가 추가적으로 존재하는데 AP 모드의 경우 사용하는 아두이노 보드가 WiFi 공유기처럼 AP가 되어 동작하여 다른 외부장치를 연결할 수 있도록 하는데 아두이노와 외부 장치 간에는 통신이 가능하지만 인터넷은 사용이 안 됩니다. 그래서 이를 동시에 가능하게끔 STA+AP모드가 존재합니다.

WiFi.begin()은 여러분이 앞서 설정한 WiFi의 SSID와 비밀번호입니다. 이를 토대로 네트워크 연결을 합니다. 그런데 그 전에 config()함수로 고정적으로 IP를 할당하여 사용할 수 있도록 하였습니다.

사용하시려는 예시를 들자면, 윈도우 '실행'창에서 'cmd'를 입력하여 커맨드창을 실행합니다. 이후 'ipconfig'를 입력하여 본인이 사용하는 인터넷 ip를 확인하시고, 기본 게이트웨이의 주소를 GATE_WAY(ex. 192.168.25.1)에 입력하시고, 여러분이 사용하시려는 고정 IP 주소를 따로 정하시면(ex. 192.168.25.77) 됩니다.

![그림1](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-12-03/arduino_http_server_1.png?raw=true)

그리고 initParameter()라는 함수가 있는데 이 함수는 초기 구조체 선언과 초기화를 위해서 둔 함수입니다.

~~~cpp
void initParameter()
{
    server_param.one_data = 0;

    for (uint8_t i = 0; i < 15; i++)
    {
        server_param.array_data[i] = 0;
    }
}
~~~

단일 정수형 데이터와 정수형 배열에 맞게 0으로 모두 초기화하는 내용입니다.

while() 문에서 '!= WL_CONNECTED', 연결이 이루어지기 전까지 루프를 반복합니다. delay() 함수는 ms 기준이므로 1000ms = 1sec로, 1초마다 연결될 때까지 '.'을 출력하여 로딩 중인 것처럼 표현하고 동시에 retries의 값을 1씩 더합니다.

50초가 넘어갈 때까지 연결이 안 되면, EspClass 객체를 사용해 restart()하면 모듈을 재시작(재부팅)할 수 있습니다. 초과하기 전에 네트워크 연결에 성공하면 어떤 SSID를 사용했고, WiFi.localIP()를 통해 연결된 ip 주소를 출력하도록 하는 조건으로 나뉩니다.

~~~cpp
http_server.on(TOTAL_RESOURCES,   HTTP_GET,  getMethodTotalDataHandle);  
http_server.on(INTEGER_RESOURCES, HTTP_POST, postMethodIntegerHandle);
http_server.on(ARRAY_RESOURCES,   HTTP_POST, postMethodArrayHandle);

http_server.begin();
Serial.println("HTTP SERVER ON");
~~~

끝에는 서버의 설정입니다. on() 함수는 클라이언트의 요청에 따라 어떠한 처리를 할 것인지 연결하는 내용입니다. 클라이언트 예제에서 정수형 단일 값 하나, 정수형 배열 값 하나씩 POST 요청을 하는 내용에 맞춰서 on() 함수에 연결하고 각각 다른 함수로 만들어 연결하였습니다. 추가로 GET() 요청도 추가했는데 POST 요청으로 변경된 데이터가 잘 입력되었는지 확인할 수 있도록 추가한 부분입니다.

이후 begin() 함수를 통해서 서버를 연결합니다.

## Loop - 서버의 클라이언트 요청 처리
~~~cpp
void loop()
{
    if (WiFi.status() == WL_CONNECTED)
    {
        http_server.handleClient();
    }
    else
    {
        WiFi.reconnect();

        Serial.println("Reconnecting to Wi-Fi AP...");

        while(WiFi.status() != WL_CONNECTED)
        {
            Serial.print(".");
    
            delay(500);
            retries++;

            if (retries > 50)
            {
                Serial.print("[ERROR] Failed to Connect ");
                Serial.println(SSID);

                EspClass esp_module;
                esp_module.restart();
            }
            else if (WiFi.status() == WL_CONNECTED)
            {
                Serial.print("Reconnected to ");
                Serial.print(SSID);
                Serial.print("--- IP: ");
                Serial.println(WiFi.localIP());
                retries = 0;
            }
        }
    }
}
~~~

'if (WiFi.status() == WL_CONNECTED)' 연결이 된 상태라면 해당 조건문 안의 내용을 반복합니다.

클라이언트와 달리 함수 하나만 있습니다.

~~~cpp
http_server.handleClient();
~~~

이 함수는 WiFi가 연결되어 있는 동안 동작하도록 설정해두었습니다. 클라이언트에서 요청이 있는 경우, 클라이언트에서 설정한 리소스와 서버의 ip 주소가 있을 겁니다, 그 설정에 맞춰서 서버는 반응상태 코드(RESPONSE STATUS CODE)를 클라이언트로 전달하고 전달받은 JSON데이터는 내부 변수에 할당하게끔 동작을 거칩니다.

그 외의 코드는 연결이 끊겼을 때를 대비한 동작입니다. 클라이언트와 동일하니 설명은 생략하겠습니다.

## 클라이언트 요청 처리 함수

~~~cpp
void postMethodIntegerHandle()
{
    StaticJsonDocument<300> doc;
    String post_body = http_server.arg("plain");

    Serial.print("HTTP Method: ");
    Serial.println(http_server.method());

    DeserializationError error_number = deserializeJson(doc, post_body);

    if (error_number)
    {
        Serial.println("[ERROR] Failed Parsing JSON Body");
        http_server.send(400);
    }
    else
    {
        if (http_server.method() == HTTP_POST)
        {
            server_param.one_data = doc["value"];

            http_server.send(201);
        }
    }
}

void postMethodArrayHandle()
{
    StaticJsonDocument<300> doc;
    String post_body = http_server.arg("plain");

    Serial.print("HTTP Method: ");
    Serial.println(http_server.method());

    DeserializationError error_number = deserializeJson(doc, post_body);

    if (error_number)
    {
        Serial.println("[ERROR] Failed Parsing JSON Body");
        http_server.send(400);
    }
    else
    {
        if (http_server.method() == HTTP_POST)
        {
            for (uint8_t i = 0; i < 15; i++)
            {
                server_param.array_data[i] = doc["value"][i];
            }

            http_server.send(202);
        }
    }
}
~~~

클라이언트의 POST 요청을 처리하는 두 함수입니다. 동일한 부분부터 살펴봅시다.

~~~cpp
StaticJsonDocument<300> doc;
String post_body = http_server.arg("plain");

Serial.print("HTTP Method: ");
Serial.println(http_server.method());

DeserializationError error_number = deserializeJson(doc, post_body);

if (error_number)
{
    Serial.println("[ERROR] Failed Parsing JSON Body");
    http_server.send(400);
}
~~~

서버 라이브러리에서 클라이언트 요청 데이터를 처리하기 위한 content-type을 "plain"으로 지정하고 POST body를 가져올 수 있습니다. method()함수는 GET / PUT / POST와 같은 요청에 대한 return 정수형 값을 반환합니다. POST의 경우 3을 리턴합니다.

앞에서 선언한 JSON, doc를 통해 post_body 문자열에 데이터를 변환합니다. 여기서 에러가 발생하면 400번을 클라이언트로 전달하고 에러를 출력하도록 하였습니다. 그 다음 JSON 파싱이 정상적으로 된 경우는 else 조건문으로 빼두었습니다.

둘 다 동일하게 POST 요청을 받아서 동일하고 구조체 데이터에 각각 정수 단일, 정수 배열에 입력하는게 차이입니다.

GET() / POST() 단일 / POST() 배열 순으로 200, 201, 202번을 클라이언트에 전달하도록 하였습니다. 이 코드 번호는 아래와 같이 구분되어 사용됩니다.

**1. Informational responses (100 – 199)**
**2. Successful responses (200 – 299)**
**3. Redirection messages (300 – 399)**
**4. Client error responses (400 – 499)**
**5. Server error responses (500 – 599)**

더 자세한 내용은 아래의 사이트를 참조하도록 하세요.

👉[서버 RESPONSE STATUS CODE 분류 기준](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)

## 클라이언트와의 요청 / 응답 예시