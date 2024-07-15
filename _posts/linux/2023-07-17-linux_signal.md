---
layout:   post
title:    Linux와 Window에서 Ctrl + C로 루프 종료 예제 / Linux signal / Window conio
category: linux
image:    /assets/img/blog/2023-07-17/thumnail_linux.png
description: >
  루프 종료 예제
---

* this unordered seed list will be replaced by toc as unordered list
{:toc}

## Linux에서 Ctrl + C 루프 종료 예제
linux 계열의 운영체제에는 시그널(Signal)이라는 인터럽트 개념의 이벤트가 존재합니다. 이 시그널을 통해 프로세스에게 이벤트가 발생했음을 알리는 것이죠.​

ROS를 실행하고 있다가 종료를 위해 Ctrl+C를 눌렀던 경험이 있을 겁니다. 그럼 "user interrupted with ctrl + c라는 문구와 함께 프로세스를 종료합니다. 이 개념이 사실 linux 운영체제에서 비롯된 내용입니다.​

단순한 프로세스는 Ctrl + C로 종료하는데 문제 없지만 코드가 복잡해지면서 이를 코드로 보완하는 방법이 있습니다. 아래는 printf 뿐인 while 루프이지만, 단순화하면 아래와 같이 코드를 적용할 수 있습니다. 

~~~cpp
#include <cstdio>
#include <signal.h> //signal 헤더

bool signal_handle = false;

void interruptSignal(int signal)
{
  (void)signal;
  signal_handle = true;
}

int main()
{
  signal(SIGINT, interruptSignal);

  while (true && !signal_handle)
  {
    printf("Hello World!\n");
  }
}
~~~

시그널 핸들러로 'interruptSignal'이라는 이름으로 함수를 만들었습니다. while 루프 이전에 signal() 함수를 사용합니다. ​

~~~cpp
void (*signal (int sig, void(*func)(int)))(int) 
~~~

이러한 구조의 개념인데 우선 첫 번째 인자를 살펴보면 SIGINT가 있습니다.
Linux의 표준 시그널 1~31번 중 2번으로 터미널에 Ctrl + C를 입력하면 발생하는 시그널입니다.<br>

![그림1](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-07-17/linux-signal_1.png?raw=true)
도서 '기초 리눅스 API' 중 발췌
{:.figcaption}

그 다음 인자는 int 타입의 함수를 포인터 형태로 받는데 여기에 앞에서 언급한 handler 함수를 할당하고 이를 토대로 while 루프에서 시그널 입력이 되면 빠져나올 수 있도록 만든 코드입니다.

## Window에서 Ctrl + C로 루프 종료 예제
이와 비슷한 개념으로 Window에도 적용할 수 없나 찾다보니 불완전하지만 아래와 같은 방식으로 비슷한 프로세스를 만들어 보았습니다.

~~~cpp
#include <iostream>
#include <windows.h> //ConsoleHandler()
#include <conio.h> //_getch()

uint8_t ascii_number = 0;

BOOL WINAPI ConsoleHandler(DWORD dwType)
{
  if (dwType == CTRL_C_EVENT)
  {
    ascii_number = _getch();
    printf("Interrupted Ctrl+C\n");
  }
  return TRUE;
}

int main()
{
  while (true && ascii_number != 3)
  {
    if (!SetConsoleCtrlHandler((PHANDLER_ROUTINE)ConsoleHandler, TRUE))
    {
      return EXIT_FAILURE;
    }

    std::cout << "Hello World!\n";
  }
}
~~~

_getch() 함수는 conio.h 헤더에서 비롯한 함수인데 키보드 입력에 따른 값을 할당할 수 있습니다. Ctrl + C의 경우 3으로 이를 아스키코드로 값을 할당하니 정수 타입의 변수 이름을 ascii_number로 하여 예제를 작성해보았습니다. 

Ctrl + C에 대한 handler는 window.h 헤더에 있는 setConsoleCtrlHandler()와 ConsoleHandler()함수를 사용한 내용입니다.

이 코드가 불완전하다는 점은 아래와 같은 예외 처리로 인한 것인데요. 

![그림2](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-07-17/linux-signal_2.png?raw=true)

위의 내용을 예외처리하여 계속 디버깅 실행을 해보면 문제 없이 루프를 반복하고, Ctrl + C 입력을 해보면 디버깅이 중지되는 걸 확인할 수 있습니다.

![그림3](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2023-07-17/linux-signal_3.png?raw=true)

---

위의 코드는 아래의 레포지토리에서 사용하실 수 있습니다!

[Loop Exit Repository](https://github.com/BGAB0322/loop_exit_simple_code "loop exit 레포지토리")