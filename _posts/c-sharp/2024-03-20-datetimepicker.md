---
layout:   post
title:    DateTimePicker 사용하는 방법 Winform과 WPF
category: c-sharp
image:    /assets/img/blog/2024-03-20/thumnail_c-sharp.png
---

* this unordered seed list will be replaced by toc as unordered list
{:toc}

이번 포스팅은 C#에서 DateTime 형식과 이를 활용한 DateTimePicker 컨트롤의 사용법을 Winform과 WPF를 함께 살펴보고자 합니다.

## DateTime 형식과 DateTimePicker 컨트롤(Winform)

Winform을 프로젝트로 생성하시고 기본 페이지 위에 아래와 같이 도구상자(Toolbox)에서 DateTimePicker 컨트롤을 배치해주도록 하겠습니다.

![그림1](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-03-20/datetime_picker_1.png?raw=true)

컨트롤 2개를 아래와 같이 DatePicker, TimePicker라는 이름으로 설정해주도록 하겠습니다.

![그림2](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-03-20/datetime_picker_2.png?raw=true)

날짜는 기본적인 포맷이라 그러려니 하는데 TimePicker라는 이름으로 지정한 컨트롤은 시간이 출력되었으면 할 겁니다.

컨트롤의 속성(Properties)에서 수정하는 방법도 있습니다.

아래와 같이 포맷은 Long, Short, Time, Custom이 콤보박스에 있을 겁니다.

![그림3](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-03-20/datetime_picker_3.png?raw=true)

각각의 포맷은 현재를 기준으로 아래와 같이 표현된다고 보시면 됩니다.

* **Long** : 2024년 3월 20일 수요일
* **Short** : 2024-03-20
* **Time** : 오후 11:26:42
* **Custom** : 사용자가 정한 형식으로

아시다시피 로직(Form1.cs)에서도 C# 코드로 지정할 수 있습니다. 

예를 들면 포맷을 아래와 같이 Custom으로 지정하고 형식을 지정하는데, 형식 지정은 string 타입에 맞춰서 지정할 수 있습니다.

![그림4](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-03-20/datetime_picker_4.png?raw=true)

만약에 위와 같이 시간 앞에 'T'를 붙여서 형식을 지정하고 실행해보면 아래와 같습니다.

![그림5](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-03-20/datetime_picker_5.png?raw=true)

여기서 HH:mm:ss처럼 DateTime에 대한 정해진 형식이 있습니다. 아래의 표를 한 번 보시죠.

|형식(Format)|결과값(Result)|
|---|---|
|"MM/dd/yyyy"|03/20/2024|
|"dddd, dd MMMM yyyy"|수요일, 20 3월 2024|
|"dddd, dd MMMM yyyy HH:mm:ss"|수요일, 20 3월 2024 12:00:00|
|"MM/dd/yyyy hh:mm tt"|03/20/2024 12:00:00 오후|
|"yyyy-MM-dd THH:mm:ss"|2024-03-20 T12:00:00|

대략적으로 위의 같은 형태로 string 형식을 지정하여 DateTime을 표현하는 여러가지 방법들이 있으며, DateTimePicker 컨트롤의 Value 속성에 날짜/시간값을 가지고 있는데 Value의 속성에는 아래와 같이 특정값을 사용하도록 설정도 가능합니다.

![그림6](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-03-20/datetime_picker_6.png?raw=true)

👉[날짜 및 시간 서식 문자열 참고 .NET 문서](https://learn.microsoft.com/ko-kr/dotnet/standard/base-types/custom-date-and-time-format-strings)

## 시간 비교하기
결국에는 DateTime도 Value를 가지는데 이를 비교 연산으로 활용할 수도 있습니다. 예를 들면, DatePicker.Date를 현재와 비교하는 내용을 작성한다고 보면 TimeSpan 형식을 통해 비교가 가능합니다.

![그림7](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-03-20/datetime_picker_7.png?raw=true)

위와 같이 **TimeSpan 형식**의 변수에 현재시간의 날짜(Now.Date)과 지정된 DatePicker.Value.Date를 빼니 3일에 해당하는 결과가 나오죠.

이를 통해 여러분이 특정 날짜, 시간, 분, 초 비교가 가능하니 여러가지 방법으로 조건을 작성하시면 될 것 같습니다.

## DateTime 형식과 DateTimePicker 컨트롤(WPF)
WPF에도 Winform과 비슷하게 DatePicker 컨트롤이 있습니다. 이름에서 예상되다시피 'Date'Picker이며 Time인 시간에 대한 포맷을 다룰 수 없습니다. 

아래에서 보실 수 있다시피 Format이 Winform과 다르게 Long, Short 밖에 없습니다. 날짜만 표시되며 시간에 대해서는 다룰 수가 없습니다.

![그림8](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-03-20/datetime_picker_8.png?raw=true)

기존 프로토타입을 Winform으로 작성하고 추후 WPF로 수정하려다가 머리 아파오던 순간입니다.

저는 대신해서 아래의 패키지를 추가하여 사용했습니다. **"Extended.Wpf.Toolkit"**을 NuGet을 통해서 설치를 합니다. 내용은 아래와 같습니다.

![그림9](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-03-20/datetime_picker_9.png?raw=true)

설치를 해주시고 WPF에 추가해보겠습니다. 디자인은 조금 마음에 안 드는데 아래와 같습니다.

![그림10](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-03-20/datetime_picker_10.png?raw=true)

날짜에 대한 표시는 WPF의 DatePicker를 사용했고, 시간의 표시는 TimePicker를 사용해보았습니다. 

DateTimePicker 컨트롤을 사용하면 Winform처럼 사용하실 수 있습니다. TimePicker를 사용하면 시간만 표시할 수 있습니다.

두 개의 컨트롤에 현재의 시간을 기입해보겠습니다. 

![그림11](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-03-20/datetime_picker_11.png?raw=true)

그리고 출력을 확인해보면 아래와 같이 표시된다고 보시면 됩니다.

![그림12](https://github.com/BGAB0322/bgab.github.io/blob/main/assets/img/blog/2024-03-20/datetime_picker_12.png?raw=true)

WPF는 당장에 사용하는 방법에 초점을 맞춰 간단히 작성해보려고 합니다.

우선 주의하실 점은 MVVM 패턴을 사용하시려고 한다면 위의 코드를 보시다시피, DatePicker 컨트롤은 **'SelectedDate 속성'**을 사용해서 바인딩하세요.

xctk:TimePicker 컨트롤은 Winform처럼 **'Value 속성'**을 사용해서 바인딩하여 사용하시면 됩니다.

DateTimePicker 컨트롤과 DateTime 형식은 다음과 같이 활용해서 작성해보면 되겠습니다.