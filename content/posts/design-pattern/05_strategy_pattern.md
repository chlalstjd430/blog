---
title: "[DesignPattern] Strategy pattern(전략 패턴)"
date: 2020-06-11T21:16:48+09:00
categories: ["design pattern"]
---

# Strategy pattern(전략 패턴)

이번에 학습할 디자인 패턴은 **전략 패턴** 입니다.


### Strategy pattern(전략 패턴)이란?

위키에서는 다음과 같이 정의합니다.

> 전략 패턴(strategy pattern) 또는 정책 패턴(policy pattern)은 실행 중에 알고리즘을 선택할 수 있게 하는 행위 소프트웨어 디자인 패턴이다.

예제를 보고 이해해봅시다.

### 예제 - 로봇 만들기

우리는 공격과 이동 기능이 있는 TaekwonV, Atom 로봇을 만든다고 가정하자, 그렇다면 다음과 같은 클래스 구조를 생각할 수 있다.

<p align = "center">
  <img src = "https://user-images.githubusercontent.com/50758600/84384787-7aacc100-ac29-11ea-95aa-4ca9b0b86383.png" width = "50%">
</p>

해당 클래스를 보며 다음과 같은 문제에 대해 생각해보자.

1. 새로운 로봇을 만들어 기존의 공격 또는 이동 방법을 추가하거나 수정하려면 어떻게 할까? 예를 들어 새로웃 로봇(선가드)를 만들어 태권V의 미사일 공격 기능을 추가하려면?

2. 기존 로봇의 공격 또는 이동 방법을 수정하려면 어떤 변경 작업을 해야하는가?

먼저 기존의 클래스 구조를 따라서 새로운 로븟을 만들려면

<p align = "center">
  <img src = "https://user-images.githubusercontent.com/50758600/84385392-7503ab00-ac2a-11ea-887e-7f8fc4c3bedf.png" width = "50%">
</p>

다음과 같은 클래스를 추가하면 된다. 그러나 로봇이 추가될 때 마다 attack과 move 메소드를 수정해주어야 하고, 로봇들이 같은 기능을 할 경우 코드의 중복이 많이 생길 것이다.

이에 우리는 전략 패턴을 이용해보자.

### 예제 - 로봇 만들기(전략 패턴 이용)

<p align = "center">
  <img src = "https://user-images.githubusercontent.com/50758600/84385585-cdd34380-ac2a-11ea-99a9-6c0df430f986.png" width = "50%">
</p>

- Robot의 멤버로 MovingStrategy와 AttackStrategy가 멤버 변수가 사진에서 누락되었습니다.

기존의 클래스 구조와 비교를 하면 MovingStrategy, AttackStrategy와 같은 인터페이스를 만들고, 해당 클래스들을의 구체 클래스를 만드는 것을 확인하실 수 있습니다. 이후 Robot클래세 setter을 이용하여 move와 attack의 동작을 달리 할 수 있게 하였습니다.

이를 통해 추후 각 로봇들이 추가됨에 따라 반복되는 기능은 쉽게 구현할 수 있고, 기능들을 동적으로 변경할 수 있게 되었습니다.


<br><br><br>

참고
- [JAVA 객체지향 디자인 패턴 ](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788968480911&orderClick=JAj&OV_REFFER=http://click.linkprice.com/click.php?m=kbbook&a=A100532541&l=9999&l_cd1=0&u_id=kacg2ap746029ofs02yqe&l_cd2=0&tu=http%3A%2F%2Fwww.kyobobook.co.kr%2Fproduct%2FdetailViewKor.laf%3FmallGb%3DKOR%26ejkGb%3DKOR%26barcode%3D9788968480911%26orderClick%3DJAj)
