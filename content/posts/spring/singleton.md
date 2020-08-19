---
title: "Spring Singleton VS Java Singleton"
date: 2020-08-19T13:51:32+09:00
categories: ["스프링"]
---
# Spring Singleton VS Java Singleton(스프링 싱글톤과 자바 싱글톤의 차이)

최근 프로젝트를 하던도중 싱글톤으로 구현해야하는 클래스가 생겼는데, Spring에서 singleton으로 구현하는 것(scope을 singleton으로 빈 등록)과 자바 싱글톤으로 구현하는 것에 어떠한 차이가 있을지 궁금해서 내용을 찾아보았다.

## 결론

- 스프링 싱글톤은 컨테이너 당 하나 생성된다.

- 자바 싱글톤은 클래스당 하나가 생성된다.
