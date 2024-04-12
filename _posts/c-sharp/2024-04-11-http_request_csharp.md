---
layout:   post
title:    Winform | HTTP 메시지큐 예제(MSMQ)
category: c-sharp
image:    /assets/img/blog/2024-04-11/thumnail_c-sharp.png
---

* this unordered seed list will be replaced by toc as unordered list
{:toc}

## 메시지큐와 MSMQ(Mircrosoft Message Queuing)
메시지큐(Message Queue)는 프로세스 또는 프로그램 간에 데이터를 서로 교환할 때 사용하는 통신 방법 중 하나로, 메시지 지향 미들웨어(MOM, Message Oriented Middleware)를 구현한 시스템을 의미합니다. 

![메시지큐 이미지](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-04-11/http_request_csharp_1.png?raw=true)

메시지 지향 미들웨어란 비동기 메시지를 사용하는 응용 프로그램들 사이에서 데이터를 송수신하는 것을 의미합니다. 
메시지큐의 장점은 아래와 같은데요.

* **비동기(Asynchronous)**
메시지 큐는 생산된 메시지의 저장, 전송에 대해 동기화 처리를 진행하지 않고, 큐에 넣어 두기 때문에 나중에 처리할 수 있습니다.

* **낮은 결합도(Decoupling)**
생산자 서비스와 소비자 서비스가 독립적으로 행동하게 됨으로써 서비스 간 결합도가 낮아집니다.

* **확장성(Scalable)**
생산자 서비스 혹은 소비자 서비스를 원하는 대로 확장할 수 있기 때문에 확장성이 좋습니다.

* **탄력성(Resilience)**
소비자 서비스가 다운되더라도 어플리케이션이 중단되는 것은 아니며, 메시지는 메시지 큐에 남아 있습니다. 그래서 소비자 서비스가 다시 시작될 때마다 추가 설정이나 작업을 수행하지 않고도 메시지 처리를 시작할 수 있습니다.

* **보장성(Guarantees)**
메시지 큐는 큐에 보관되는 모든 메시지가 결국 소비자 서비스에게 전달된다는 일반적인 보장을 제공합니다.

메시지큐를 적용할 수 있는 사례를 살펴보면,

* 작업에 대한 응답이 필요하지 않는 경우
* 동시에 많은 요청이 들어오는 경우
* 오래 걸리는 프로세스 또는 백그라운드 실행이 필요한 경우
* 어플레이션간 통신하기 위한 방법이 필요한 경우

등이 있습니다.

그 중 C#에서는 Winform, WPF 등의 어플리케이션 간에 데이터 전송 및 통신 상태에 따라 원격으로 데이터를 전송하기 위해 사용할 수 있습니다.

다행이게도 C#에서는 Window 환경 내에서 MSMQ를 이를 좀 더 쉽게 구현할 수 있습니다. 그렇다고 마냥 좋다고 볼 수 없는게 **Window OS에서 사용자의 PC에 MSMQ 활성화되어 있어**야 합니다.

그래서 해당 내용은 개인적인 테스트를 위해서 사용되기에 별 문제 없지만 배포를 염두에 두고 있다면 사용 전에 고민이 필요한 사항으로 보입니다.

## MSMQ 설치하기
Window에서 MSMQ를 사용하기 위한 설정 및 설치를 알아보도록 하겠습니다.

![제어판 설정 열기](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-04-11/http_request_csharp_2.png?raw=true)

위와 같이 제어판(Control Panel) 실행하시고, 프로그램 및 기능(Programs and Features)을 클릭해주세요.

![프로그램 및 기능창](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-04-11/http_request_csharp_3.png?raw=true)

그러면 아래와 같이 좌측 상단에 Windows 기능 켜기/끄기(Turn Windows features on or off)를 찾아서 마찬가지로 클릭해서 Windows 기능창을 열어주도록 하겠습니다.

![Window 기능창과 MSMQ 체크](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-04-11/http_request_csharp_4.png?raw=true)

위의 사진처럼 MSMQ(Microsoft Message Queue) Server 탭을 찾아서 전체를 체크해주도록 합니다. MSMQ를 활성화하신 적이 없다면 '확인'을 클릭하는 순간 설치가 진행될 겁니다.

이로써 MSMQ 사용 준비가 끝났습니다.

## C# Winform MSMQ 송수신 예제 작성
메시지큐, MSMQ를 C#에서 한 번 사용해보도록 하겠습니다.

### 메시지큐 송신 프로그램
우선 메시지큐를 통해 데이터를 전송하는 예시부터 살펴보겠습니다. Winform에서 버튼과 텍스트 박스 하나씩 만들어 텍스트 박스의 값을 전달하는 예시를 만들겠습니다.

![Winform UI](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-04-11/http_request_csharp_5.png?raw=true)

~~~csharp
using System.Messaging;
~~~

위와 같이 상단 네임스페이스에 위와 같이 using 지시문을 추가해주도록 합시다.

그럼 해당 파일에서 MessageQueue를 사용할 수 있습니다. 전역 변수로 선언해주도록 하겠습니다.

![메시지큐 선언](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-04-11/http_request_csharp_6.png?raw=true)

이어서 개인(Private) 로컬 환경을 기준으로 MyMessageQueue라는 이름으로 메시지 큐를 생성해주도록 하겠습니다.

![메시지큐 인스턴스 생성 및 경로](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-04-11/http_request_csharp_7.png?raw=true)

~~~csharp
MessageQueue.Create(queuePath);
~~~

경로는 string을 통해 미리 입력해두고 Create() 함수를 호출하여 지정된 경로에 메시지 큐를 생성하려고 합니다. 만약 해당 경로에 큐가 이미 존재한다면 'MessageQueueException' 예외가 발생하게 됩니다.

그래서 try-catch문을 작성하였습니다.

만약 예외의 속성이 MessageQueueErrorCode.QueueExists(큐가 이미 존재함)을 의미하는지 검사하고, 예외 원인이 '큐가 이미 존재함'(QueueExists)라면 기존 큐를 열기 위한 인스턴스를 생성하도록 하고 그렇지 않은 경우에는 오류 메시지를 표시하도록 하였습니다.

![메시지큐 전송 버튼](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-04-11/http_request_csharp_8.png?raw=true)

Winform에 배치한 버튼의 클릭 이벤트를 위와 같이 작성하였습니다.

~~~csharp
public partial class Form1 : Form
{
    private MessageQueue messageQueue;

    public Form1()
    {
        InitializeComponent();

        string queuePath = ".\\Private$\\MyMessageQueue";

        try
        {
            messageQueue = MessageQueue.Create(queuePath);
        }
        catch (MessageQueueException ex)
        {
            if (ex.MessageQueueErrorCode == MessageQueueErrorCode.QueueExists)
            {
                messageQueue = new MessageQueue(queuePath);
            }
            else
            {
                MessageBox.Show($"Error: {ex.Message}");
            }
        }

    }

    private void button1_Click(object sender, EventArgs e)
    {
        //string messageText = textBox1.Text;
        int messageText = Convert.ToInt32(textBox1.Text);

        System.Messaging.Message message = new System.Messaging.Message(messageText);
        messageQueue.Send(message);

        MessageBox.Show("Message Sent!");
    }
}
~~~

textBox1의 문자값, string이 아닌 int 타입으로 전달하는 내용을 예시로 하겠습니다. 그게 아닌 문자열 전달을 원하신다면 주석 처리해둔 내용처럼 string 타입의 데이터를 Message에 담으셔도 됩니다.

전송이 완료되면 메시지박스를 표시하도록 하겠습니다.

이어서 새로운 프로젝트를 생성하여 이 메시지큐를 받을 수 있는 프로그램을 작성해보겠습니다.

---

### 메시지큐 수신 프로그램
수신 프로그램에는 텍스트 박스 하나만 배치해두고 송신 프로그램에서 보낸 메시지큐를 텍스트박스에 출력만 하도록 하겠습니다.

![메시지큐 수신 텍스트박스 UI](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-04-11/http_request_csharp_9.png?raw=true)

동일하게 상단에 using 지시문을 이용해 Messaging을 추가해주시고 아래와 같이 생성자에 코드를 작성하겠습니다.

![메시지큐 수신 설정 코드](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-04-11/http_request_csharp_10.png?raw=true)

코드를 순서대로 설명하자면,

송신 프로그램과 마찬가지로 경로를 동일하게 설정해줍니다. 만약 메시지큐가 경로상 설정되어 있지 않다면 메시지 박스를 표시하고 아니라면 new MessageQueue(queuePath)를 통해 해당 큐에 대한 참조를 생성합니다.

그리고 메시지큐 인스턴스에 대해 Formatter 속성을 설정합니다. 설정은 아래와 같이 XmlMessageFormatter를 사용합니다.

이전에 int 타입으로 메시지큐를 담았던 것 기억하시죠?

~~~csharp
messageQueue.Formatter = new XmlMessageFormatter(new Type[] { typeof(int) });
~~~

만약 텍스트박스의 값을 string으로 전달해서 받으려면 typeof(string)으로 변경해서 사용하면 됩니다.

그리고 ReceiveCompleted 핸들러를 MessageQueue_ReceiveCompleted 메서드에 연결하고 비동기적으로 메시지를 수신하게 되면, 설정하신 콜백 메서드가 호출되어 메시지 수신 완료까지 작업을 정의하실 수 있습니다.

~~~csharp
private void MessageQueue_ReceiveCompleted(object sender, ReceiveCompletedEventArgs e)
{
    MessageQueue mq = (MessageQueue)sender;

    // 주석 처리한 내용은 메시지 타입을 string으로 했을 때 기준으로 작성한 내용입니다!
    //System.Messaging.Message message = mq.EndReceive(e.AsyncResult);

    //string messageText = message.Body.ToString();

    //this.Invoke((MethodInvoker)delegate
    //{
    //    txtReceivedMessage.Text = messageText;
    //});

    //mq.BeginReceive();

    System.Messaging.Message message = mq.EndReceive(e.AsyncResult);

    int messageText = (int)message.Body;

    this.Invoke((MethodInvoker)delegate
    {
        txtReceivedMessage.Text = messageText.ToString();
    });

    mq.BeginReceive();
}
~~~

ReceiveCompleted 핸들러는 위와 같이 작성하겠습니다.

'MessageQueue mq = (MessageQueue)sender;는 이벤트를 발생시킨 메시지 큐의 인스턴스를 얻습니다. 이어서 System.Messaging.Message message = mq.EndReceive(e.AsyncResult);는 비동기 수신 작업을 완료하고, 수신된 메시지를 가져옵니다.

e.AsyncResult가 비동기 작업의 결과를 나타내며, **EndReceive 메서드를 통해 실제 메시지**를 얻습니다.

이제 message의 본문(Body)을 정수(int)로 변환하고 해당 메시지큐 코드가 정수 데이터를 포함하고 있음을 가정하여 진행합니다. 

텍스트 박스 UI에 출력을 위해 Invoke 메서드를 사용해 UI 스레드에서 메서드를 실행하도록 하였습니다.

mq.BeginReceive();는 다음 메시지의 비동기 수신을 시작합니다. 이렇게 함으로써, 메서드가 끝나자마자 다시 메시지를 수신할 준비를 하여 지속적인 메시지 수신이 가능하게 됩니다.

~~~csharp
private void Form1_FormClosing(object sender, FormClosingEventArgs e)
{
    messageQueue.ReceiveCompleted -= MessageQueue_ReceiveCompleted;
    messageQueue.Dispose();
}
~~~

마지막으로 Winform이 종료되면 위와 같이 이벤트 핸들러를 연결해제하고 Dispose() 메서드를 호출하여 messageQueue 객체가 사용 중인 모든 리소스를 해제하도록 합니다.

MessageQueue 객체를 사용하면 외부 시스템 리소스에 연결되어 사용하다 보니 적절한 시점에서 Dispose를 호출하여 리소스 누수를 방지하도록 해야 합니다.

그럼 양측 프로그램을 실행하여 값을 전달해보겠습니다.

![메시지큐 송수신 결과 확인](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-04-11/http_request_csharp_11.png?raw=true)

송신 프로그램에서 문자 입력 후 Send 버튼을 클릭하니 수신 프로그램에도 동일하게 값이 나타납니다.

이 예제를 참고하여 상황에 맞는 메시지큐 송수신 프로그램을 작성해보시길 바랍니다!