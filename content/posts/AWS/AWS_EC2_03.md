---
title: "AWS EC2를 이용하여 Spring Boot 배포하기(3)"
date: 2020-05-22T16:46:31+09:00
categories: ["AWS"]
---

# AWS EC2를 이용하여 Spring Boot 배포하기(3)


<p align = "center">
  <img src="https://thumbs.gfycat.com/BewitchedShabbyAtlanticblackgoby-size_restricted.gif" >
</p>

안녕하세요! 오늘은 지난 포스팅에 이어서 서버에서 Spring Boot를 배포하는 과정을 안내해드리도록 하겠습니다. 우리는 github 레포지토리가 존재한다는 가정하에 진행하겠습니다.

## 1. 호스트 네임 변경하기

EC2 서버에 접속하면, "ubuntu@ip - ip주소"가 등장하는데 ip주소만 봤을 때, 어떠한 서비스인지 알 수 없으니 호스트 네임을 변경해주도록 합시다.

먼저,

> sudo vim /etc/cloud/cloud.cfg

라는 명령어를 이용하여 cloud.cfg를 열어줍니다. 이후 방향키를 내려가며 "preserve_hostname: false"을 true로 변경해줍니다.

- 참고 : [vi 에디터 사용법](https://mrkzet.tistory.com/23)

이후

> sudo hostnamectl set-hostname "호스트이름"

명령어를 이용해 호스트이름을 설정해주고 "reboot" 명령어를 이용해 서버를 재시작해주시면 다음과 같이 정상적으로 호스트이름이 설정된 것을 확인하실 수 있습니다.

<p align = "center">
  <img src="https://user-images.githubusercontent.com/50758600/82638182-4bfe8480-9c41-11ea-88a0-5b7d8302f5e9.png" >
</p>

<br><br>

## 2. Java 8 설치

먼저 서버에 Java 8을 설치해주어야한다(나는 배포 할 프로젝트가 8버전이다)

- [Ubuntu에서 Java 8 설치](https://github.com/chlalstjd430/TIL/blob/master/Backend/Ubuntu%20Java8%20%EC%84%A4%EC%B9%98.md) 글을 참고해주세요!
<br><br>

## 3. MySQL 설치

이후 MySQL도 설치해 봅시다.

- [Ubuntu에서 MySQL 설치](https://github.com/chlalstjd430/TIL/blob/master/Backend/Ubuntu%20Java8%20%EC%84%A4%EC%B9%98.md) 글을 참고해주세요!

<br><br>

## 3. git clone 하기

EC2에는 기본적으로 git이 설치가 되어있습니다, 그렇기에 바로 git clone을 시도해줍니다.

> git clone 본인 repository

**🙋‍♂️주의 : Permission denied(publickey)가 나오게 되면 [해당 블로그](https://zeddios.tistory.com/120) 글을 참고해 키를 등록해주세요**

이렇게 하게 되면 root안에 방금 clone한 폴더가 등장합니다.

이후

> dir

명령어를 사용해 clone한 폴더가 생겼는지 확인하고,

> cd 폴더명 // 현재 위치를 [폴더명]으로 이동 <br>
> sudo chmod 777 ./gradlew  // ./gradlew에게 권한을 부여 <br>
> sudo ./gradlew build // gradle build <br>

명령어를 순차적으로 실행하고, jar파일의 이름을 찾아

> $ java –jar [이름].jar &

명령어를 실행하면 서버에 배포가 됩니다.

지금까지 AWS EC2에 Spring Boot 배포하는 것을 소개해드렸는데, 사실 제 블로그 글에 부족한게 많습니다. 사실 추후에 RDS를 사용하여 배포하는 것을 보여드리기 위해 지금은 중간중간 생략한 과정도 많아 그냥 참고용으로만 봐주시면 감사하겠습니다. 추가적으로 궁금하신 사항들은 저에게 물어보셔도되고 훌륭한 다른 포스팅들을 검색하셔도 됩니다.

추후 RDS를 이용하는 법에 대해서도 간단히 소개해드릴 예정이니 그때 뵙겠습니다. 그럼 20000!!

<p align = "center">
  <img src="https://media3.giphy.com/media/UvVxNZEziZY4AhOs7A/giphy.gif" >
</p>


참고 문헌
- [스프링 부트와 AWS로 혼자 구현하는 웹 서비스](http://www.yes24.com/Product/Goods/83849117?)
