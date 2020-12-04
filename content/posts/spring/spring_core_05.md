---
title: "[Spring Core(스프링 프레임워크 핵심기술)] 05. IoC 컨테이너 : @Component와 Component-Scan"
date: 2020-07-09T10:04:44+09:00
categories: ["spring"]
---

# @Component와 Component-Scan

## 컴포넌트 스캔 주요 기능

## @Component

- @Repository
- @Service
- @Controller
- @Configuration

## @Componenet-Scan

- spring 3.1 부터 지원
- @ComponentScan 어노테이션부터 component scan을 시작하여 해당 파일이 있는 패키지 안의 모든 것을 scan한다.

    예를 들어 아래와 같은 패키지 구조를 이루고 있을 떄

![https://user-images.githubusercontent.com/50758600/86435953-297d8200-bd3c-11ea-8295-19df63155d1c.png](https://user-images.githubusercontent.com/50758600/86435953-297d8200-bd3c-11ea-8295-19df63155d1c.png)

```java
@SpringBootApplication
public class DemoApplication {
    @Autowired
    MyService myService;

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

}
```

위와 같이 코드를 작성하면 빈을 찾을 수 없다는 빨간 밑줄이 나오게 된다.

실제 내부 코드를 보면

```java
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
```

다음과 같이 Filter가 존재하는 것을 확인할 수 있다.

이떄 function을 사용하여 빈 등록을 하면 다른 패키지의 빈을 찾을 수 있다.

```java
@SpringBootApplication
public class DemoApplication {
    @Autowired
    MyService myService;

    public static void main(String[] args) {
        new SpringApplicationBuilder()
                .sources(DemoApplication.class)
                .initializers((ApplicationContextInitializer<GenericApplicationContext>)
                                applicationContext -> {
                                    applicationContext.registerBean(MyService.class);
                })
                .run(args);
    }
}
```

위의 코드를 구현해주고 실행하면 정상적으로 MyService도 빈 등록이 되어져 실행되는 것을 알 수 있다.

## 동작 원리

- @ComponentScan은 스캔할 패키지와 애노테이션에 대한 정보
- 실제 스캐닝은 ​ConfigurationClassPostProcessor​라는 ​BeanFactoryPostProcessor​에

    의해 처리 됨.
