---
title: "AWS EC2를 이용하여 Spring Boot 배포하기(1)"
date: 2020-05-22T02:26:58+09:00
categories: ["AWS"]
---

# AWS EC2를 이용하여 Spring Boot 배포하기(1)

저는 지금까지 AWS EC2를 이용하여 여러번 서버를 배포해보았었는데요. 이번에 그 과정들을 간단하게 블로그에 담고자 글을 작성합니다.

먼저 시작하기 전에 제가 배포하려는 서비스는 다음과 같습니다.

- 실제 운영할 서비스는 아니고 간단한 토이 프로젝트의 느낌이다.
- 나는 빠르게 프로젝트를 배포하여야한다.
- 나는 현재 돈이 없다..(ㅜㅜ)

이러한 조건의 서비스를 배포하기 위해서 PAAS의 Heroku도 생각했지만, 예전에 Heroku를 사용했을 떄, 서버 region이 외국에 있고 Cold Start 문제가 있어 서비스를 운영하는데 어려움을 많이 느꼇습니다. 그렇기에 AWS에서 제공해주는 프리 티어를 이용하여 간단하게 EC2 서버에 서비스를 배포할 계획을 하였습니다. 그러면 시작해보도록 하겠습니다!

<p align = "center">
  <img src = "https://gifimage.net/wp-content/uploads/2018/04/lets-go-gif-11.gif">
</p>

<br><br><br>

> 본 글은 AWS에 회원가입 모두 완료되었다는 가정하에 진행됩니다.

## EC2 인스턴스 생성하기
---

<br><br>

### 1. 인스턴스 시작

먼저 인스턴스를 만들어주기 위해 EC2 대시보드로 이동합시다!
<p align = "center">
  <img src="https://user-images.githubusercontent.com/50758600/82554742-7a2c8780-9ba1-11ea-8a8b-414a46a3f5d0.png">
</p>

이동하게 되면 위와 같은 화면을 마주하게 되는데요~, 여기서 화면 중앙의 **인스턴스 시작** 버튼을 눌러줍시다.


<br><br>

### 2. 단계 1: Amazon Machine Image(AMI) 선택

<p align = "center">
  <img src="https://user-images.githubusercontent.com/50758600/82557686-2755ce80-9ba7-11ea-96dc-e9a8a7a9b6f0.png">
</p>

AWS EC2에서는 Amazaon Linux, Ubunto, SUSE 등 다양한 OS를 제공하고 있습니다. 저는 이중에서 "Ubuntu Server 18.04 LTS (HVM), SSD Volume Type" 를 선택하겠습니다.

**🤷‍♂️ Why?**

- OS는 Window와 Linux가 대표적인데, 보안적인 부분이나 비용적인 부분에서 Window보다 좋기 때문에 대체적으로 서버는 Linux를 이용합니다.

- 그렇다면 Linux 기반의 OS에도 여러가지가 있는데, 왜 Ubunto인가?

  - 저는 EC2는 프리티어로만 사용할 예정이고 실제 Linux에 대해 전반적으로 지식이 많은게 아닌 초보자이기 때문에, 쉽게 접할 수 있는 Ubuntu를 선택하였습니다.

- Ubuntu는 2년바다 크게 업데이트가 진행된다, 그래서 최근 20.4버전이 나와있다. 그러나 최신 버전이라고 무조건 좋다는 법은 없다. 출시된지 아직 한달도 안되었기 떄문에 그 동안 검증된 18.04 버전을 선택하겠다.


- 사실 내가 사용하려는 수준에서는 이 모든게 크게 상관없을 듯 하다(?)

어찌됐든 AMI른 선택해주었다면 다음 단계로 넘어가보자.

<br><br>

### 3. 단계 2: 인스턴스 유형 선택

<p align = "center">
  <img src="https://user-images.githubusercontent.com/50758600/82557778-55d3a980-9ba7-11ea-9b33-c6949c8cb1b3.png">
</p>


여기서는 t2.micro를 선택하시면 됩니다~


**🤷‍♂️ Why?**

- 앞서 말했다시피 현재 배포할 서비스는 지속적으로 관리할 서비스가 아닙니다. 잘된다면 서비스를 계속 운영할 수 있겠지만 현재 상황에서는 그러한 계획이 없기 떄문에, 무료로 제공되는 "프리티어"를 이용할 것입니다. 그렇기에 선택의 여지가 없습니다..

  (돈 없는 학식은 웁니다 ㅜㅜ)

<p align = "center">
  <img src="https://gifimage.net/wp-content/uploads/2017/10/crying-gif-funny.gif">
</p>

- 참고
  - 해당 유형에 대해 간략하게 설명드리면 "T시리즈"라고 불리는 유형이다. 이 서비스는 **크레딧** 이라는 일종의 CPU를 사용할 수 있는 포인트 개념이 있습니다.  관련된 내용은 해당 블로그를 참고하시면 좋을 것 같습니다.
  - [T2 instance 특징은 무엇인가요?](http://faq.hostway.co.kr/AWS_FAQ/8270)

<br><br>

### 4. 단계 3: 인스턴스 세부 정보 구성

<p align = "center">
  <img src="https://user-images.githubusercontent.com/50758600/82557977-afd46f00-9ba7-11ea-8bf8-45e45bd52264.png">
</p>

해당 사항은 추가적으로 건드리지 않고 그대로 진행하겠습니다!

**🤷‍♂️ Why?**

- 현재 한대의 서버를 가지고 간단한 프로젝트를 배포하기 위해서이니, 별다른 설정을 하지않겠습니다.

- 추가적으로 해당 관련 정보를 수정하기 위해서는 추가적인 공부가 필요할 것 같아 당장 EC2를 이용하기 위해서는 그대로 넘어가도록하겠습니다.

<br><br>

### 5. 단계 4: 스토리지 추가

<p align = "center">
  <img src="https://user-images.githubusercontent.com/50758600/82558135-fcb84580-9ba7-11ea-86b0-560aafb01eb7.png">
</p>

해당 스토리지의 크기를 30으로 설정해줍시다!

**🤷‍♂️ Why?**

- 프리 티어에서는 최대 30GB의 스토리지를 사용할 수 있기 떄문에 우리는 최~대한 이를 활용하기 위해서 최대한으로 설정해줍시다!

<br><br>

### 6. 단계 5: 태그 추가

<p align = "center">
  <img src="https://user-images.githubusercontent.com/50758600/82558443-941d9880-9ba8-11ea-9de5-e1910a7e673b.png" width = "49%">
  <img src="https://user-images.githubusercontent.com/50758600/82558333-633d6380-9ba8-11ea-8749-3d9daedce58b.png" width = "49%">
</p>

태그는 웹 콘솔에서 표기될 태그인 Name 태그를 등록합니다.

(태그 추가 클릭 - 키에 Name입력 - 값에 본인의 서비스를 나타낼 수 있는 값을 입력)

**🤷‍♂️ Why?**

- 여러 인스턴스가 있을 경우 이를 태그별로 구분하기 위해 본인의 서비스를 나타낼 수 있는 값으로 등록을합니다.

<br><br>

### 7. 단계 6: 보안 그룹 구성

<p align = "center">
  <img src="https://user-images.githubusercontent.com/50758600/82558495-bb746580-9ba8-11ea-836f-74de2f80d0c7.png">
</p>

보안 그룹은 사진과 같이 규칙을 추가하겠습니다.

**🤷‍♂️ Why?**

- 22포트

    - 22포트의 경우는 AWS EC2에 터미널로 접속할 떄를 이야기합니다. 해당 포트는 내 IP에서만 접속하게끔 설정을 하였는데요, 이유는 pem키가 없으면 접속이 불가능하나 pem키가 노출되었을 때 서버에서 비트 코인 채굴 서버로 이용될 수 있기 떄문입니다! 그러므로 내 IP로설정하고 장소가 이동될 떄 마다 내 IP를 다시 받아 이용하시는게 보안적으로 좋습니다 :D

    - [말도 안 되는 과금의 추억. 요금 폭탄]([https://sanghaklee.tistory.com/32](https://sanghaklee.tistory.com/32))

- 80,443 포트

    - http,https 통신을 위해 기본적으로 추가해줍니다~.

<br><br>

### 8. 단계 7: 인스턴스 시작 검토

<p align = "center">
  <img src="https://user-images.githubusercontent.com/50758600/82558853-8ae0fb80-9ba9-11ea-8803-8a9f1b444f28.png" width="49%">
  <img src="https://user-images.githubusercontent.com/50758600/82558952-bebc2100-9ba9-11ea-923f-3a1a4e18d3c2.png" width="49%">
</p>

- 해당 단계에서는 그동안 저희가 설정했던 부분들이 일괄적으로 나오게 됩니다. 이상없는지 확인해주고 시작하기 버튼을 클릭합시다!

- 이후 새 키 페어 생성 을 설정하고(기존 키 페어가 없다는 가정하에 진행하겠습니다.) 키 페어 이름을 서비스와 연관지어 짓고, 키 페어 다운로드를 클릭합니다. 이후 키 페어를 안전한 위치에 저장해줍니다.

- 위 과정이 완료되었으면 인스턴스 시작을 클릭합니다!

<p align = "center">
  <img src="https://user-images.githubusercontent.com/50758600/82559712-0d1def80-9bab-11ea-9a02-3ae0c677568c.png">
</p>

다음과 같은 화면이 나오면 다시 EC2 대시보드로 이동해봅시다.

<br><br>

### 9. 인스턴스 생성 확인


<p align = "center">
  <img src="https://user-images.githubusercontent.com/50758600/82559888-59692f80-9bab-11ea-8374-6a309197e80a.png">
</p>


짜잔-! 인스턴스 상태가 초록불과 함께 running이라 나와있으면 해당 인스턴스가 잘 실행되고 있음을 뜻합니다.(인스턴스가 생성되고 실행되는 과정까지 시간이 소요될 수 있습니다.)

잠깐! 저희가 EC2 서버를 실제 접속하기 전에 해야할 일이 있습니다!

<p align = "center">
  <img src="https://thumbs.gfycat.com/TediousGrandAmericanpainthorse-small.gif">
</p>

<br><br>

### 10. 탄력적 IP 할당

인스턴스는 하나의 서버이기 떄문에 IP가 존재합니다. 인스턴스 생성시 항상 새 IP를 할당받고, 같은 인스턴스를 중지하고 다시 시작할 떄도 새 IP가 할당됩니다. 그렇게 되면 매번 접속해야 하는 IP가 변경되어 PC에서 매번 접속시 IP 주소를 확인해야하는 번거로움이 있습니다. 그렇기에  고정 IP를 가지게 해야하는데요, 우리는 이를 위해서 탄력적 IP를 할당합시다.



##### 1. 먼저 **탄력적 IP** 메뉴를 클릭합니다.

<p align = "left">
  <img src="https://user-images.githubusercontent.com/50758600/82560420-69354380-9bac-11ea-84c4-b3ee3cc68e4c.png" width="30%" height="30%">
</p>

##### 2. **탄력적 IP 주소 할당** 을 클릭합니다.

<p align = "left">
  <img src="https://user-images.githubusercontent.com/50758600/82560573-adc0df00-9bac-11ea-86f4-c1de43ba2f02.png" >
</p>

##### 3. 다음 화면에서 할당을 클릭합니다.

<p align = "left">
  <img src="https://user-images.githubusercontent.com/50758600/82584410-6bf56000-9bcf-11ea-8446-47c7be34a17a.png" >
</p>

##### 4. 탄력적 IP를 할당받으신걸 확인하실 수 있습니다. 그러면 이전에 저희가 만든 인스턴스와 탄력적 IP를 연결해보도록 하겠습니다! 먼저 **퍼블릭 IPv4 주소** 밑의 IP를 클릭합시다.

<p align = "left">
  <img src="https://user-images.githubusercontent.com/50758600/82584618-b545af80-9bcf-11ea-970c-c6677ee386e9.png" >
</p>

##### 5. 이후 그대로 **탄력적 IP 주소 연결** 을 클릭합니다.

<p align = "left">
  <img src="https://user-images.githubusercontent.com/50758600/82585719-71ec4080-9bd1-11ea-9622-ed342679f928.png" >
</p>

##### 6. 이후 이전에 만든 인스턴스와 프라이빗 IP 주소를 설정합니다. 처음 AWS EC2 이용하시는 분은 그냥 그대로 설정하시고 연결하시면 됩니다.

<p align = "left">
  <img src="https://user-images.githubusercontent.com/50758600/82585866-abbd4700-9bd1-11ea-8aa7-53c9390e9c83.png" >
</p>

##### 7. 아래와 같이 나오게되면, 탄력적 IP가 해당 인스턴스와 연결되었음을 알 수 있습니다.

<p align = "left">
  <img src="https://user-images.githubusercontent.com/50758600/82586211-2d14d980-9bd2-11ea-8792-b9ca07aa8cab.png" >
</p>

실제로 EC2 인스턴스를 확인하면 탄력적 IP가 연결된것도 확인하실 수 있습니다!

<p align = "left">
  <img src="https://user-images.githubusercontent.com/50758600/82586447-8250eb00-9bd2-11ea-95e0-e622eb5b7000.png" >
</p>


지금까지 EC2 인스턴스 생성과 탄력적 IP 주소 연결까지의 과정을 소개해드렸습니다. 다음 포스팅에는 본격적으로 AWS EC2에 접속하는 방법과 서버에 Spring Boot를 배포하는 과정을 소개해드릴게요. 그럼 다음 포스팅떄 뵙겠습니다 :D

<p align = "center">
  <img src="https://2.bp.blogspot.com/-D4zTIbdUxD0/XLsfrU4cgzI/AAAAAAAAB4s/iKc0CWOrLzEmY2lJtzmSS-mwxlluR3HQQCLcBGAs/s1600/byebyegif.gif" >
</p>



참고 문헌
- [스프링 부트와 AWS로 혼자 구현하는 웹 서비스](http://www.yes24.com/Product/Goods/83849117?)
