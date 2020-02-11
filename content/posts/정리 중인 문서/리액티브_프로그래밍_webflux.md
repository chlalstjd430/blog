---
title: "리액티브"
date: 2020-02-06T17:44:27+09:00
categories: ["리액티브 프로그래밍"]
tags: ["spring webflux"]
---

# Spring Webflux
***

최근 교내 커뮤니티 동아리 유어슈에서 Spring Webflux를 이용하여 프로젝트를 하였다. 사실 기존 동아리 스펙이 webflux로 정해져서 어쩔수 없이 새로운 프로젝트에 사용하여서 webflux가 왜 필요한지? 어떠한 장점이 있는지? 등 이해도가 부족하다는 생각이 들었다. 그래서 앞으로 webflux를 정리하고 reactive programming을 이해하하는 과정을 문서로 담아 정리하려고 한다.

#### 연관된 나의 글

- [리엑티브 프로그래밍이란?](https://chlalstjd430.github.io/2020/01/%EB%A6%AC%EC%95%A1%ED%8B%B0%EB%B8%8C-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D_001/)

### Spring Webflux란?

reactive-stack 웹 프레임워크인 Spring Webflux는 Spring 5버전부터 추가되었다. Webflux는 non-blocking을 지향하고  Reactive Streams back pressure을 지원한다. 그리고 Sprinb MVC와 Spring Webflux는 같이 사용할 수 있다.

![co-exist Spring5](https://docs.spring.io/spring/docs/current/spring-framework-reference/images/spring-mvc-and-webflux-venn.png)

**Spring 웹 요청을 처리하는 방식**

1. 요청 매핑

  - 웹 요청을 어느 핸들러에게 보낼지 결정

  - URL, 헤더

  - @RequestMapping

2. 요청 바인딩

  - 핸들러에 전달할 웹 요청 준비

  - 웹 URL, 헤더, 쿠키, 바디 등

3. 핸들러(controller) 실행

  - 전달 받은 요청 정보를 이용해 로직을 수행하고 결과를 리턴

4. 핸들러 결과 처리(응답 생성)

  - 핸들러의 리턴 값으로 웹 응답 생성

~~~java
@RestController
public class MyController {
  @GetMapping("/hello/{name}") // 1. 요청 매핑
  String hello(@PathVariable String name){ // 2. 요청 바인딩
    return "Hello! " + name; // 3,4. 해들러 실행 후 결과 처리
  }
}
~~~

**함수형 WebFlux가 웹 요청을 처리하는 방식**

1. 요청 매핑

  - RouterFunction

2. 요청 바인딩

  -HandlerFUnction

3. 핸들러 실행

  -HandlerFUnction

4. 핸들러 결과 처리(응답 생성)

  -HandlerFUnction

### spring webflux를 사용하는 이유

- 비동기-논블럭킹 리엑티브 개발에 사용

- 효율적으로 등장하는 고성능 웹 애플리케이션 개발

- 서비스간 호출이 많은 마이크로서비스 아키텍처에 적합

- ~~새로운 방식으로 개발해보고 싶은 호기심 충족~~

  - 발표자 토비님께서는 이를 권장하지 않는다 하셨다.

- thread pool hell 발생을 저하

  - 일반적으로 spring은 멀티 스레드 기반으로 동작하고, 만약 blocking IO로 진행된다면 준비된 스레드보다 더 많은 요청이 들어올 경우 thread pool hell이 발생합니다. 하지만 webflux를 사용할 경우 비동기 non-blocking IO로 서버의 코어 갯수만큼 스레드가 할당되어 사용되기 때문에, thread pool hell이 발생을 줄일 수 있습니다.

- 검증된 JVM과 방대한 자바 생태계를 이용할 수 있는 장점이 있습니다.

#### webflux 작동원리




#### 지원 웹 서버/컨테이너

- Sel

<br><br><br><br><br>

참고자료

- [스프링캠프 강의 - 토비](https://www.youtube.com/watch?v=2E_1yb8iLKk&t=536s)
# 20.02.07 아직 작성이 완료되지 않은 문서입니다 :D
