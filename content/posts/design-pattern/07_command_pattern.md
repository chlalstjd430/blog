---
title: "[DesignPattern] Command pattern(커맨드 패턴)"
date: 2020-06-13T17:52:37+09:00
categories: ["design pattern"]
---

# Command pattern(커맨드 패턴)

이번에 학습할 디자인 패턴은 **커맨드 패턴** 입니다.


### Command pattern(커맨드 패턴)이란?

위키에서는 다음과 같이 정의합니다.

> 커맨드 패턴(Command pattern)이란 요청을 객체의 형태로 캡슐화하여 사용자가 보낸 요청을 나중에 이용할 수 있도록 매서드 이름, 매개변수 등 요청에 필요한 정보를 저장 또는 로깅, 취소할 수 있게 하는 패턴이다.

> 커맨드 패턴에는 명령(command), 수신자(receiver), 발동자(invoker), 클라이언트(client)의 네개의 용어가 항상 따른다. 커맨드 객체는 수신자 객체를 가지고 있으며, 수신자의 메서드를 호출하고, 이에 수신자는 자신에게 정의된 메서드를 수행한다. 커맨드 객체는 별도로 발동자 객체에 전달되어 명령을 발동하게 한다. 발동자 객체는 필요에 따라 명령 발동에 대한 기록을 남길 수 있다. 한 발동자 객체에 다수의 커맨드 객체가 전달될 수 있다. 클라이언트 객체는 발동자 객체와 하나 이상의 커맨드 객체를 보유한다. 클라이언트 객체는 어느 시점에서 어떤 명령을 수행할지를 결정한다. 명령을 수행하려면, 클라이언트 객체는 발동자 객체로 커맨드 객체를 전달한다.


예제와 함께 커맨드 패턴에 대해 자세히 알아보도록 하겠습니다.

### 만능 버튼 만들기

우리가 만능 버튼을 만들어야한다는 문제에 앞서, 일반적으로 램프와 버튼 관계의 클래스를 만들면 다음과 같이 만들 수 있습니다.

<p align = "center">
  <img src = "https://user-images.githubusercontent.com/50758600/84565023-5673f000-ada1-11ea-9b76-57da5b4026e8.png"/>
</p>

이와 같이 클래스를 만들었을 때, 다음과 같은 문제점을 고민해봅시다.

1. 버튼이 눌렸을 때 램프를 켜는 대신에 다른 기능을 수행하기 위해서는 어떤 변경 작업을 해야 되는가? 예를 들어 버튼이 눌리면 알람을 시작시키려면?

2. 버튼이 눌렸을 때 수행되는 기능을 프로그램이 동작할 때 결정하기 위해서는?

<br><br>

이러한 문제점을 해결하기 위해 커맨드 패턴을 이용해봅시다.


커맨드 패턴에는 총 4가지의 역할이 존재합니다.

1. Invoker

- 기능의 실행을 요청하는 호출자 클래스

2. Command

- 실행될 기능에 대한 인터페이스

- 실행될 기능을 execute 메세드로 표현

3. ConcreteCommand

- 실제로 실행되는 기능을 구현(Command 구현 클래스)

4. Receiver

- ConcreteCommand에서 execute 메소드를 구현할 때 필요한 클래스


그렇다면 위에 클래스에 4가지 역할을 대입하면 다음과 같습니다.

> Invoker - Button <br>
Command <br>
ConcreteCommand - LampOnCommand, AlarmStartCommand  <br>
Receiver - Lamp, Alarm <br>

결론적으로 다음과 같은 클래스 구조가 나옵니다.

<p align = "center">
  <img src = "https://user-images.githubusercontent.com/50758600/84565696-ddc36280-ada5-11ea-8ade-9644121930cb.png"/>
</p>

다음과 같은 클래스 구조를 갖게되면, 동적으로 command를 설정함과 동시에  여러 기능들을 추가적으로 구현하는데 어렵지않게 됩니다.

<br><br><br>

참고
- [JAVA 객체지향 디자인 패턴 ](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788968480911&orderClick=JAj&OV_REFFER=http://click.linkprice.com/click.php?m=kbbook&a=A100532541&l=9999&l_cd1=0&u_id=kacg2ap746029ofs02yqe&l_cd2=0&tu=http%3A%2F%2Fwww.kyobobook.co.kr%2Fproduct%2FdetailViewKor.laf%3FmallGb%3DKOR%26ejkGb%3DKOR%26barcode%3D9788968480911%26orderClick%3DJAj)
