---
title: "[Spring Core(스프링 프레임워크 핵심기술)] 06. IoC 컨테이너 : 빈의 스코프"
date: 2020-07-09T19:10:24+09:00
categories: ["spring"]
---

# 빈의 스코프

## Scope

- 싱글톤(default)
- 프로토타입
    - Request
    - Session
    - WebSocket
    - ...

Single.java

```java
@Component
public class Single {
}
```

Proto.java

```java
@Component @Scope("prototype")
public class Proto {
}
```

AppRunner.java

```java
@Component
public class AppRunner implements ApplicationRunner  {
    @Autowired
    ApplicationContext ctx;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println("single");
        System.out.println(ctx.getBean(Single.class));
        System.out.println(ctx.getBean(Single.class));
        System.out.println(ctx.getBean(Single.class));

        System.out.println("proto");
        System.out.println(ctx.getBean(Proto.class));
        System.out.println(ctx.getBean(Proto.class));
        System.out.println(ctx.getBean(Proto.class));
    }
}
```

실제로 위와 같이 코드를 작성후 실행하면

```java
single
com.spring.core.autowired.demo.Single@58472096
com.spring.core.autowired.demo.Single@58472096
com.spring.core.autowired.demo.Single@58472096
proto
com.spring.core.autowired.demo.Proto@337bbfdf
com.spring.core.autowired.demo.Proto@52d97ab6
com.spring.core.autowired.demo.Proto@4da6d664
```

single은 하나의 값만 출력되고, proto는 매번 다른 값이 출력되는 것을 확인 할 수 있다.

## 프로토타입 빈이 싱글톤 빈을 참조하면?

Proto.java

```java
@Component @Scope("prototype")
public class Proto {
		@Autowired
		Single single;
}
```

위 코드와 같이 작성할 경우 우리가 의도한대로, Proto는 매번 새로 생성되고, Single은 한번만 생성된다.

## 싱글톤 빈이 프로토타입 빈을 참조하면?

Single.java

```java
@Component
public class Single {
    @Autowired
    Proto proto;

    public Proto getProto(){
        return proto;
    }
}
```

위 코드를 작성하고,

AppRunner.java

```java
@Component
public class AppRunner implements ApplicationRunner  {
    @Autowired
    ApplicationContext ctx;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println("proto by single");
        System.out.println(ctx.getBean(Single.class).getProto());
        System.out.println(ctx.getBean(Single.class).getProto());
        System.out.println(ctx.getBean(Single.class).getProto());
    }
}
```

실제로 위와 같이 코드를 작성후 실행하면

```java
proto by single
com.spring.core.autowired.demo.Proto@4745e9c
com.spring.core.autowired.demo.Proto@4745e9c
com.spring.core.autowired.demo.Proto@4745e9c
```

single안의 proto가 매번 다른 값을 보여주지 않고, 동일한 값을 보여주는 것을 확인할 수 있다.

**이를 해결하는 방법**

1. proxyMode 설정

    위의 코드에서 아래의 파일을 수정해주자.

    Proto.java

    ```java
    @Component @Scope(value = "prototype", proxyMode = ScopedProxyMode.TARGET_CLASS)
    public class Proto {
    }
    ```

    이후 실행하면 모든 인스턴스가 매번 달라지는 것을 알 수 있다.

    여기서 **proxyMode = ScopedProxyMode.TARGET_CLASS**의 의미는, 아래의 그림처럼 Proxy를 거쳐 참조하게끔 하는 것이다.

    ![https://user-images.githubusercontent.com/50758600/86444125-440b2780-bd4b-11ea-94be-0d073b181d75.png](https://user-images.githubusercontent.com/50758600/86444125-440b2780-bd4b-11ea-94be-0d073b181d75.png)

2. ObjectProvider 사용

    Single.java

    ```java
    @Component
    public class Single {
        @Autowired
        ObjectProvider<Proto> proto;

        public Proto getProto(){
            return proto.getIfAvailable();
        }
    }
    ```

    위 방법은 코드를 직접 변경하여 추가적인 작업이 필요하다.
