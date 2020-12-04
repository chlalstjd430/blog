---
title: "[Spring Core(스프링 프레임워크 핵심기술)] 07. IoC 컨테이너 : 프로파일"
date: 2020-07-12T12:30:33+09:00
categories: ["spring"]
---
# profile

## ApplicationContext extends EnvironmentCapable

application context는 bean factory뿐만 아니라 다양한 기능들을 제공한다.

그 중에서 구현하고 있는 EnviromentCapable에서 제공하는 기능은 크게 2가지가 있는데, 그 중 하나인 프로파일 기능을 공부해보자.

## 프로파일

해당 기능은 spring의 application context의 environment의 기능 중 하나

### 프로파일 유즈케이스

각각의 환경마다 혹은 특정한 환경에서 다른 빈을 설정하기 위한 요구사항을 충족시키기 위해 사용한다.

- 테스트 환경에서 A라는 빈을 사용하고, 배환경에서 B라는 빈을 쓰고 싶을 때
- 모니터링/테스트/배포 각각 목적에 맞게 빈을 사용하고 싶을 때

### 프로파일 정의하기

간단하게 처음에 enviroment의 profile에 어떠한 정보가 담겨있는지 살펴보자.

[AppRunner.java](http://apprunner.java)

```java
@Component
public class AppRunner implements ApplicationRunner  {
    @Autowired
    ApplicationContext ctx;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        Environment environment = ctx.getEnvironment();
        System.out.println(Arrays.toString(environment.getActiveProfiles()));
        System.out.println(Arrays.toString(environment.getDefaultProfiles()));
    }
}
```

위와 같은 runner를 하나 만들어주고 enviroment에서 어떠한 값이 출력되는지 살펴보자.

(ApplicationContext가 EnvironmentCpapble을 구현하고 있기에 위와 같이 사용 가능하다)

출력 결과는 다음과 같다.

```java
[]
[default]
```

여기서 default profile은 말 그대로 기본적으로 가져가는 프로파일이다.

이제는 새로운 프로파일을 정의해보자. 이를 위해서 아래와 같이 코드를 작성해보자.

BookRepository.java

```java
public class BookRepository {
}
```

TestBookRepository.java

```java
public class TestBookRepository extends BookRepository{
}
```

TestConfiguration.java

```java
@Configuration
@Profile("test")
public class TestConfiguration {

    @Bean
    public BookRepository bookRepository() {
        return new TestBookRepository();
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
        System.out.println(Arrays.toString(environment.getActiveProfiles()));
        System.out.println(Arrays.toString(environment.getDefaultProfiles()));
    }
}
```

코드를 다 작성했다면 실행을 해보자.

우리는 실행에 실패했다는 결과를 확인할 수 있다.

그 이유는 BookRepository 빈 등록을 @Profile("test") 어노테이션을 이용하여 test profile일 때만 사용되도록 설정하였기 때문이다.

그렇다면 우리의 실행 profile을 test로 바꾸어보자.

먼저 우측 상단의 Edit Configurations를 클릭한다.

![https://user-images.githubusercontent.com/50758600/86953066-db7adb00-c18e-11ea-9bb0-205755b8ff6e.png](https://user-images.githubusercontent.com/50758600/86953066-db7adb00-c18e-11ea-9bb0-205755b8ff6e.png)

이후 크게 2가지 방법이 존재하는데, 첫번째는 Active Profiles를 다음과 같이 설정하는 것이다.

![https://user-images.githubusercontent.com/50758600/86953294-2c8acf00-c18f-11ea-87b8-f9ef1c13ad7a.png](https://user-images.githubusercontent.com/50758600/86953294-2c8acf00-c18f-11ea-87b8-f9ef1c13ad7a.png)

혹은 Enviroment의 VM options에 다음과 같이 설정하는 것이다.

![https://user-images.githubusercontent.com/50758600/86953536-87bcc180-c18f-11ea-9ebe-a18e064623c1.png](https://user-images.githubusercontent.com/50758600/86953536-87bcc180-c18f-11ea-9ebe-a18e064623c1.png)

이후 확인해주면 정상적으로 수행되는 것을 확인할 수 있다.

지금까지 코드에서 TestConfiguration.java에 빈등록을 일일이 해주었는데, 이제 쉽게 @Profile 어노테이션으로 설정해주는 방법을 알아보자. 이를 위해서 먼저 [TestConfiguration.java](http://testconfiguration.java) 파일을 삭제한고 기존의 코드를 아래와 같이 바꾸어보자.

TestBookRepository.java

```java
@Repository
@Profile("test")
public class TestBookRepository extends BookRepository{
}
```

이후 실행해보면 정상적으로 실행되는 것을 알 수 있다.

참고로 @Profile("!test")로 설정하면 test가 아닌 모든 프로파일에 적용된다. 즉, default 환경에서뿐만 아니라 profile이 test가 아니면 다 적용 가능하다. 이와 관련된 내용은 [스프링 공식문서](https://docs.spring.io/spring/docs/5.2.7.RELEASE/spring-framework-reference/core.html#beans-definition-profiles-java) 를 참고하면 좋다.
