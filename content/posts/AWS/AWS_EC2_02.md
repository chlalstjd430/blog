---
title: "AWS EC2를 이용하여 Spring Boot 배포하기(2)"
date: 2020-05-22T13:59:22+09:00
categories: ["AWS"]
---

# AWS EC2를 이용하여 Spring Boot 배포하기(2) - AWS EC2 서버 접속

<p align = "center">
  <img src="https://media.tenor.com/images/19aba5fb5a891041880efd7efb64017f/tenor.gif" >
</p>

오늘은 AWS EC2 서버에 접속하는 과정을 소개해드리도록 하겠습니다. 먼저 시작하기 전에 해당 글은 **윈도우** 를 이용하여 접속하는 방법을 안내해드리는 점 참고해주세요!


## 1. putty 다운로드 및 키 발급

윈도우에서는 Mac과 같이 ssh 접속하기에 불편한 점이 많아 별도의 클라이언트를 설치해줍니다.

##### 1. [putty 사이트](https://www.putty.org/)에서 다운로드 후 **putty.exe**,**puttygen.exe** 모두 다운로드하였으면, **puttygen.exe** 부터 실행시켜줍니다. 이후 Load를 클릭합니다.

<p align = "center">
  <img src="https://user-images.githubusercontent.com/50758600/82623745-d7fdb580-9c1b-11ea-9b2d-3748f6b413b3.png" >
</p>

##### 2. 이전 AWS 발급한 Pem 키가 저장되어있는 곳을 찾아 클릭해줍시다.

<p align = "center">
  <img src="https://user-images.githubusercontent.com/50758600/82624056-ad602c80-9c1c-11ea-9e66-a766ff9dc1ca.png" >
</p>

그러면 다음과 같이 키가 발급되었다는 팝업창이 나옵니다.

<p align = "center">
  <img src="https://user-images.githubusercontent.com/50758600/82624133-ccf75500-9c1c-11ea-9022-72f1f934431d.png" >
</p>

##### 3. 이후 **Save private Key** 를 클릭해 안전한 장소에 발급된 키를 저장해줍니다.

<p align = "center">
  <img src="https://user-images.githubusercontent.com/50758600/82624244-03cd6b00-9c1d-11ea-991a-0d031bf10830.png" >
</p>

## 2. putty를 이용하여 AWS EC2 서버 접속

##### 1. 먼저 AWS에서 인스턴스와 연결한 탄력적 ip를 알아옵니다. 그리고 다음 사진과 같이 Host Name을 설정해줍니다.(자신의 탄력적 ip를 적으시면 됩니다)

**주의 : 이전에 AWS 보안그룹에 22포트는 내 IP에서만 접근이 가능하도록 설정하였습니다. 설정한 IP의 장소가 맞는지 확인해주세요!**

<p align = "center">
  <img src="https://user-images.githubusercontent.com/50758600/82626422-c23fbe80-9c22-11ea-9ecc-913da387f9ad.png" >
</p>

##### 2. 이후 왼쪽 카테고리의 Connection - SSH - Auth로 이동하여 이전에 발급한 ppk를 Browse..를 클릭하여 불러옵니다.

**주의 : pem키를 찾을 때 모든 파일에서 찾기를 하셔야 pem키가 보입니다.**

<p align = "center">
  <img src="https://user-images.githubusercontent.com/50758600/82625156-645da780-9c1f-11ea-9fe8-52e80d4ac59b.png" width = "49%" >
  <img src="https://user-images.githubusercontent.com/50758600/82625205-85be9380-9c1f-11ea-8c4b-18c8cdf07c90.png" width = "49%" >
</p>

##### 3. ppk를 잘 불러왔으면, 다시 왼쪽 카테고리에서 제일 위에 있는 **Session** 으로 이동합니다. 이후 **Saved Sessions** 에 본인의 서비스명을 입력하고 **Save** 해줍시다. 이후 저장한 세션을 선택 후 **Open** 해줍시다!

<p align = "center">
  <img src="https://user-images.githubusercontent.com/50758600/82626455-d4b9f800-9c22-11ea-9955-3e0e589711d0.png" >
</p>

## 3. AWS EC2 접속

open 해주면 보안 알림창이 처음에 나오는데 그냥 '예'를 눌러주시면 됩니다. 그러면 이후 다음과 같은화면이 나오며 접속이 완료됩니다!

<p align = "center">
  <img src="https://user-images.githubusercontent.com/50758600/82626610-38442580-9c23-11ea-929f-136498f1ec66.png" >
</p>

그럼 다음 포스팅에서 본격적으로 AWS EC2 서버에 Spring Boot를 배포하는 작업을 보여드리겠습니다. 다음 포스팅에서 봐용~!👐

<p align = "center">
  <img src=https://gifimage.net/wp-content/uploads/2018/05/see-ya-gif-12.gif" >
</p>


참고 문헌
- [스프링 부트와 AWS로 혼자 구현하는 웹 서비스](http://www.yes24.com/Product/Goods/83849117?)
