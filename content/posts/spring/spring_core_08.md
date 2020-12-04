---
title: "[Spring Core(스프링 프레임워크 핵심기술)] 08. IoC 컨테이너 : 프로퍼티"
date: 2020-07-12T12:32:18+09:00
categories: ["spring"]
---
# property

## 프로퍼티

- 다양한 방법으로 정의할 수 있는 설정값
- Environment의 역할은 프로퍼티 소스 실행 및 프로퍼티 값 가져오기
- application에 등록된 여러가지 key-value 쌍으로 제공되는 property에 접근할수 있는 기능

## 우선순위

- StandardServletEnvironment의 우선순위
    - ServletConfig 매개변수
    - ServeltContext 매개변수
    - JNDI(java compiler)
    - JVM 시스템 프로퍼티(-Dkey = "value")
    - JVM 시스템 환경 변수(운영 체제 환경 변수)

## 사용방법

- 환경변수(VMoption)

    ![https://user-images.githubusercontent.com/50758600/87227145-d8bcf780-c3d3-11ea-83d5-b77fa3fc9634.png](https://user-images.githubusercontent.com/50758600/87227145-d8bcf780-c3d3-11ea-83d5-b77fa3fc9634.png)

    다음과 같이 VM options에 설정을 해주고, [AppRunner.java](http://apprunner.java) 파일에서 다음과 같이 코드를 작성해보자.

    ```java
    @Component
    public class AppRunner implements ApplicationRunner  {
        @Autowired
        ApplicationContext ctx;

        @Override
        public void run(ApplicationArguments args) throws Exception {
            Environment environment = ctx.getEnvironment();
            System.out.println(environment.getProperty("app.name"));
        }
    }
    ```

    그리고 실행하면 "spring5" 라는 단어가 출력이 된다.

- properties & @PropertySource

    ![https://user-images.githubusercontent.com/50758600/87227250-c2fc0200-c3d4-11ea-8869-e4310c16e05a.png](https://user-images.githubusercontent.com/50758600/87227250-c2fc0200-c3d4-11ea-8869-e4310c16e05a.png)

    위 사진처럼 새롭게 [app.properties](http://app.properties) 파일을 만들고 그 안에

    ```java
    app.name=spring
    ```

    를 작성하고 밑에 순서대로 진행한다.

    DemoApplication.java

    ```java
    @SpringBootApplication
    @PropertySource("classpath:/app.properties")
    public class DemoApplication {

        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }

    }
    ```

    AppRunner.java

    ```java
    @Component
    public class AppRunner implements ApplicationRunner  {
        @Autowired
        ApplicationContext ctx;

        @Autowired
        BookRepository bookRepository;

        @Override
        public void run(ApplicationArguments args) throws Exception {
            Environment environment = ctx.getEnvironment();
            System.out.println(environment.getProperty("app.about"));
        }
    }
    ```

    이렇게 작성후 실행하면 "spring"이라는 단어가 출력된다.

    만약 이 둘을 혼용해서 사용하면 VMoption이 우선순위이다.

- @Value

    ```java
    .
    .
    .
    @Value("${app.name}")
    String name;
    .
    .
    .
    ```

    이렇게 사용시에 환경변수로 설정한 값을 가지게된다.

## 스프링 부트의 외부 설정 참고

- 기본 프로퍼티 소스 지원
- 프로파일까지 고려한 계층형 프로퍼티 우선 순위 제공
