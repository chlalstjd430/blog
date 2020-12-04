---
title: "[Spring Core(스프링 프레임워크 핵심기술)] 02. 스프링 IoC 컨테이너와 빈"
date: 2020-07-01T01:40:21+09:00
categories: ["spring"]
---

# 스프링 IoC 컨테이너와 빈


## IoC(Inversion of Control)

의존 관계 주입(DI, Dependency Injection)이라고도 하며, 어떤 객체가 사용하는 **의존 객체를 직접 만들어 사용하는게 아니라, 주입 받아 사용하는 방법**을 말 함.

## 1. 스프링 IoC 컨테이너

- BeanFactory

    - 스프링 IoC의 가장 최상위 인터페이스

- 애플리케이션 컴포넌트의 중앙 저장소

- **빈 설정 소스**로 부터 **빈 정의**를 읽어들이고, **빈을 구성하고 제공**한다


## 2. Bean

- 스프링 IoC 컨테이너가 관리 하는 객체

    - 단순 클래스가 모두 빈이 아니라, @Repository, @Service 등 실제로 IoC 컨테이너가 관리하는 객체들을 빈이라고 한다.

- 의존성 주입을 하기 위해서는 빈이되야 한다.

- Spring IoC 컨테이너에 등록된 빈의 장점

    - 의존성 관리

    ```java
    TestServcie {
    	private TestRepository repository = new TestRepository();
    }
    ```

    ```java
    @Service
    TestService {
    	@Autowired
    	private TestRepository repository;
    }
    ```

    두가지 코드 중에서 위의 경우에는 TestRepostiroy를 테스트해야 할 경우 해당 객체를 수정할 수 없다. 그러나 아래의 코드는 주입을 하기 때문에 수정이 가능하여 용이하다.

    - Scope

        Scope에는 다음과 같이 2가지가 있다.

        - 싱글톤(Spring IoC에서 관리하는 빈들은 기본적으로 싱글톤이다) : 하나의 객체 생성

        - 프로포토타입 : 매번 다른 객체 생성

    - lifecycle interface

## 3. ApplicationContext

- BeanFactory
    - 메시지 소스 처리 기능(i18n)
    - 이벤트 발행 기능
    - 리소스 로딩 기능
