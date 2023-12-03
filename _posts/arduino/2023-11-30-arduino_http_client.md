---
layout:   post
title:    ESP8266 HTTP 클라이언트 POST 예제
category: arduino
image:    /assets/img/blog/2023-11-30/thumbnail_arduino.png
---

* this unordered seed list will be replaced by toc as unordered list
{:toc}

이번 포스팅은 ESP8266에서 HTTP POST 요청을 하는 예제 코드를 소개와 함께 리뷰하도록 해보겠습니다. 아래는 제가 설명에 사용할 코드의 레포지토리입니다. 다운로드 받아 전체 소스 코드를 확인하실 수 있습니다.

👉[Arduino HTTP Client Example](https://github.com/BGAB0322/esp8266_http_client_example)

## RESTful API 아두이노 클라이언트 예제 개요

👉[RESTful API란?](https://bgab0322.github.io/blog/arduino/2023-11-15-restful_api/)

이 예제 HTTP 클라이언트를 사용하기 위해서는 아래의 흐름으로 동작한다고 생각하시면 됩니다.

* **WiFi 스캔하여 IP 할당**
* **내부 데이터를 JSON 객체(StaticJsonDocument/DynamicJsonDocument)를 사용하여 문자열로 변환(serializeJson())**
* **특정 End Point로 서버에 POST 메서드 요청**
* **서버는 클라이언트 요청(REQUEST)에 맞춰 응답(RESPONSE)을 보내고 클라이언트는 요청 후 서버의 반응을 확인하는 것을 반복**

아두이노가 가지고 있는 데이터 변화를 서버로 전달하기 위해 데이터를 JSON 형태로 변환하여 전달합니다. 서버측도 아두이노라면 아두이노 서버는 JSON 데이터를 다시 아두이노 코드의 데이터로 변환하여(deserializeJson()) 정보를 수정할 겁니다.

크게 아두이노에서 데이터를 JSON 문자열로 변환하여 송수신한다는 것을 인지하고 코드를 보면서 연습해보면 될 것 같습니다.

## Library & Header

~~~cpp
#include <ESP8266WiFi.h>
#include <WiFiClient.h>
#include <ESP8266HTTPClient.h>
#include <ArduinoJson.h>
~~~

헤더파일은 4가지를 사용합니다. 

* <ESP8266WiFi.h>는 ESP8266 계열 모듈들이 WiFi.begin(), WiFi.status() 등 WiFi 연결에 대응하는 기능들을 사용할 수 있다고 보시면 됩니다.
* <WiFiClient.h>는 HTTP 클라이언트 동작시(ex. http_client.begin(wifi_cleint, URL))파라미터로 wifi_client 객체가 사용됩니다.
* <ESP8266HTTPClient.h>는 ESP8266 계열 모듈들이 HTTP 클라이언트 관련한 기능(begin(), addHeader(), POST(), PUT() 등)을 사용하기 위함 입니다.
* <ArduinoJson.h>은 JSON 형태의 데이터로 변환하기 위해 필요한 라이브러리입니다.

이렇게 4가지를 구성하여 HTTP 클라이언트를 설정하여 JSON 문자열로 데이터를 담아 POST() 요청을 할 것입니다.

~~~cpp
const char* SSID   = "your wifi ssid"; 
const char* PASSWD = "your wifi password";

String SERVER_IP         = "192.168.0.0:80";
String TOKEN_KEY         = "";
String TOKEN_VALUE       = "";
String INTEGER_RESOURCES = "/projects/http_client/data_types/integer";
String ARRAY_RESOURCES   = "/projects/http_client/data_types/array";
String json_to_string    = "";
~~~

SSID와 PASSWD는 여러분이 사용하시려는 WiFi 공유기의 SSID(WiFi이름), 비밀번호에 맞춰 수정하시면 됩니다.

그리고 SERVER_IP는 POST() 요청을 하려는 서버, 그 대상의 URL을 작성해야 합니다. 아두이노로 서버를 따로 만드신다면 서버 설정에서 사용하는 IP 일수도 있고, 특정 웹사이트일 수도 있고 상황에 맞게 변형하셔야 합니다.

TOKEN_KEY, TOKEN_VALUE는 보안이 요구되는 프로젝트라면 별도로 필요할 수도 있습니다. 예제에서는 따로 다루지 않았습니다.

RESOURCES는 2개를 만들어 두었습니다. 하나는 정수형의 데이터를 JSON에 담고, 다른 하나는 정수형의 배열을 JSON에 담아 보내기 때문에 그에 맞춰 End Point의 이름을 설정했습니다. 

이 구성도 상황 및 기준에 따라서 크게 바뀔 수도 있으므로 하나의 예시를 들어 리소스로 데이터를 구분하고 JSON의 키(Key)는 "value"로 정하여 값을 변환하였습니다. 차후 다시 설명드리겠습니다.

json_to_string은 JSON 문자열 형태로 담을 변수를 선언한 것입니다.

## Setup - WiFi

다음은 setup()을 살펴보도록 하겠습니다. WiFi 설정과 관련한 내용을 작성하였습니다.

~~~cpp
void setup()
{
    Serial.begin(115200);
    WiFi.begin(SSID, PASSWD);

    Serial.println("Connecting to Wi-Fi AP...");

    while ((WiFi.status() != WL_CONNECTED) && (retries < 50))
    {
        retries++;
        delay(1000);

        Serial.print(".");

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

WiFi.begin()은 여러분이 앞서 설정한 WiFi의 SSID와 비밀번호입니다. 이를 토대로 네트워크 연결을 합니다.

while() 문에서 '!= WL_CONNECTED', 연결이 이루어지기 전까지 루프를 반복합니다. delay() 함수는 ms 기준이므로 1000ms = 1sec로, 1초마다 연결될 때까지 '.'을 출력하여 로딩 중인 것처럼 표현하고 동시에 retries의 값을 1씩 더합니다.

50초가 넘어갈 때까지 연결이 안 되면, EspClass 객체를 사용해 restart()하면 모듈을 재시작(재부팅)할 수 있습니다. 초과하기 전에 네트워크 연결에 성공하면 어떤 SSID를 사용했고, WiFi.localIP()를 통해 연결된 ip 주소를 출력하도록 하는 조건으로 나뉩니다.

## Loop - 클라이언트 POST 요청 

~~~cpp
void loop()
{
    if (WiFi.status() == WL_CONNECTED)
    {
        WiFiClient wifi_client;
        HTTPClient http_client;

        http_client.begin(wifi_client, "http://" + SERVER_IP + ARRAY_RESOURCES);
        // http_client.addHeader(TOKEN_KEY, TOKEN_VALUE);
        http_client.addHeader("Content-Type", "application/json"); //"text/plain"

        convertJsonArrayDocument();
        // convertJsonDocument();

        int server_response_code = http_client.POST(json_to_string);
        json_to_string.clear();

        if (server_response_code > 0)
        {
            String response = http_client.getString();
            Serial.println(response);
        }
        else
        {
            Serial.print("[ERROR] ERROR NUMBER : ");
            Serial.println(server_response_code);
        }

        http_client.end();
    }
}
~~~

'if (WiFi.status() == WL_CONNECTED)' 연결이 된 상태라면 해당 조건문 안의 내용을 반복합니다.

WiFiClient와 HTTPClient를 선언하는데 이렇게 지역변수로 선언한 이유가 따로 있습니다.

위의 **클라이언트 객체를 전역으로 선언하고 사용했더니 메모리 에러**가 발생하면서 재부팅되는 사례가 있었습니다. 해당 내용과 비슷한 사례로 Arduino 포럼에서 발견했고, 대안으로 위와 같이 지역 선언으로 해결한 내용이었습니다. 저도 이를 따랐습니다.

![그림1](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-11-30/arduino_http_client_1.png?raw=true)

WiFiClient는 HTTPClient를 사용할 때, begin() 함수에 할당되는 객체 인자로 설정되어 있습니다. 그 다음 인자는 URL 내용인데, 저는 앞의 "http://"를 제외하고, 서버 IP와 리소스 내용에 대한 문자열을 상단에 미리 선언하여 사용하도록 하였습니다.

보안을 위한 Token을 추가하신다면, 주석을 제거하고 addHeader() 함수를 사용하시고, 이 예제는 JSON으로 변환한 데이터이므로 Content-Type은 json으로 맞춥니다.

convertJsonArrayDocument() 함수와 convertJsonDocument() 함수는 각각 배열을 JSON 문자열로 변환, 정수를 JSON 문자열로 변환하는 내용을 담고 있습니다. 최종적으로 json_to_string 문자열 변수에 입력하고 이를 POST()로 요청합니다.

이후 .clear()로 문자열을 초기화하고, 서버의 반응을 기다리거나 출력합니다.

에러가 발생한다면 아래의 목록 중 하나이거나, 서버측에서 지정된 에러 코드값이 따로 있을 겁니다. 아래의 에러값은 <ESP8266HTTPClient.h> 헤더 내부 내용입니다. 별도로 참고하시라고 페이지도 첨부해두겠습니다.

👉[ESP8266HTTPClient 헤더 파일 Github 주소 참조](https://github.com/esp8266/Arduino/blob/master/libraries/ESP8266HTTPClient/src/ESP8266HTTPClient.h)

~~~cpp
/// HTTP client errors
#define HTTPC_ERROR_CONNECTION_FAILED   (-1)
#define HTTPC_ERROR_SEND_HEADER_FAILED  (-2)
#define HTTPC_ERROR_SEND_PAYLOAD_FAILED (-3)
#define HTTPC_ERROR_NOT_CONNECTED       (-4)
#define HTTPC_ERROR_CONNECTION_LOST     (-5)
#define HTTPC_ERROR_NO_STREAM           (-6)
#define HTTPC_ERROR_NO_HTTP_SERVER      (-7)
#define HTTPC_ERROR_TOO_LESS_RAM        (-8)
#define HTTPC_ERROR_ENCODING            (-9)
#define HTTPC_ERROR_STREAM_WRITE        (-10)
#define HTTPC_ERROR_READ_TIMEOUT        (-11)
~~~

서버에서 응답이 있다면 if (server_response_code > 0) 조건문 안에서 서버가 보낸 응답의 내용을 출력하도록 합니다. 그 외에는 에러로 판단, 에러 코드값을 출력하도록 합니다. 서버의 반응까지 확인되면 http_client.end()를 통해 http_client를 종료, 초기화합니다.

아래의 내용은 위의 WiFi 연결이 된 상태에서 루프말고도 조건을 추가했는데요. 루프를 돌고 있는 와중에 WiFi로부터 네트워크 연결이 끊어진다면, 재연결을 시도하기 위함입니다.
~~~cpp
else if (WiFi.status() != WL_CONNECTED)
{
    WiFi.reconnect();

    Serial.println("Reconnecting to Wi-Fi AP...");

    while (WiFi.status() != WL_CONNECTED)
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
~~~
여기서는 0.5초간 루프를 반복하며, retries가 50을 초과하면 재시작하도록 설정했습니다. 재연결에 성공하면 다시 연결된 SSID와 IP 주소 출력과 함께 다시 loop문을 반복할 겁니다.

## JSON 변환 함수

~~~cpp
void convertJsonArrayDocument()
{
    StaticJsonDocument<300> doc;

    JsonArray integer_array = doc.createNestedArray("value");

    for (uint8_t i = 0; i < 15; i++)
    {
        integer_array.add(i);
    }

    serializeJsonPretty(doc, json_to_string);
    doc.clear();
    //Serial.print(json_to_string);
}

void convertJsonDocument()
{
    StaticJsonDocument<300> doc;

    doc["value"] = 777;

    serializeJsonPretty(doc, json_to_string);
    doc.clear();
    //Serial.print(json_to_string);
}
~~~

loop문에는 두 함수 중 하나에 주석처리를 해두었습니다. 하나만 써서 확인해보라는 용도이죠.

우선 StaticJsonDocument 객체를 선언하는데 <숫자>를 기입하는데 이는 바이트 단위입니다. Static으로 선언한 경우 **최대 3KB까지** 즉, 3000 바이트까지는 Static을 써도 되지만 그 이상은 DynamicJsonDocument를 권장합니다.

실제로 3000 이상으로 선언해 사용해보면 메모리 오버플로가 발생하여 제대로 동작하지 못하는 걸 확인할 수 있었습니다.

JSON은 보통 키(Key)와 값(Value)으로 이루어진 형태입니다. 이 예제에서는 "value" : "배열 또는 정수값"으로 나오겠죠? 그런데 변수 데이터들을 JSON으로 변환하는 건 어떻게 만드는게 좋을까요?


## ArduinoJson 변환 어시스턴트

이를 위해서 ArduinoJson에서 변환 어시스턴트를 제공합니다. 아래 페이지에서 원하시는 데이터 형태를 기입하여 데이터 변환 형태를 확인할 수 있습니다.

👉[ArduinoJson 변환 어시스턴트 홈페이지](https://arduinojson.org/v6/assistant/#/step1)

![그림2](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-11-30/arduino_http_client_2.png?raw=true)

위의 이미지처럼 여러분의 모듈, 프로세서를 택합니다.

프로세서는 AVR(아두이노 UNO, MEGA 등) / Ameba D / ESP32 / ESP8266 / Nios II / SAMD21 / STM32 /  x86 / x64 가 있습니다. 저는 ESP8266 계열 모듈만 있어서 ESP8266으로 설정하고, 아래 출력은 문자열(String)으로 설정했습니다.

그 다음으로 넘어가보죠.

![그림3](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-11-30/arduino_http_client_3.png?raw=true)

저는 정수형 하나, 배열 하나씩 한 번에 입력한 내용입니다. 단일값은 "key" : "value"로 대응하지만, 배열은 [] 괄호를 추가해주어야 합니다.

이렇게 입력을 마치고 나면, 여러분이 몇 바이트의 데이터가 필요할지 아래처럼 계산을 해줍니다.

![그림4](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-11-30/arduino_http_client_4.png?raw=true)

추천 용량은 384 바이트이군요. 그럼 이걸 아두이노에서 코드로 작성하려면 어떻게 하는지도 알려줍니다.

![그림5](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-11-30/arduino_http_client_5.png?raw=true)

배열 형태로 데이터를 변환하려면 JsonArray를 선언하고 add() 함수로 값을 입력할 수 있습니다. 저의 예제 코드는 for 문으로 정리한 것 뿐이죠.

마지막에 serializeJson()으로 표현하고 예제는 serializeJsonPretty()를 씁니다. 

둘의 차이는 print해보면 알 수 있죠, 아래와 같습니다.

~~~cpp
//serializeJson()
{"value1":777,"value2":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14]}

//serializeJsonPretty()
{
  "value1" : 777,
  "value2" : [ 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14]
}
~~~

이렇게 여러분의 데이터를 JSON으로 변환하는 예제와 용량까지 알아서 해주니, 조금 더 편한게 작성하실 수 있을 겁니다.