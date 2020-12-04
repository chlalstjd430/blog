---
title: "[DesignPattern] Sigleton pattern(싱글톤 패턴)"
date: 2020-06-11T21:51:02+09:00
categories: ["design pattern"]
---

# Sigleton pattern(싱글톤 패턴)

이번에 학습할 디자인 패턴은 **싱글톤 패턴** 입니다.


### Sigleton pattern(싱글톤 패턴)이란?

위키에서는 다음과 같이 정의합니다.

> 싱글턴 패턴(Singleton pattern)을 따르는 클래스는, 생성자가 여러 차례 호출되더라도 실제로 생성되는 객체는 하나이고 최초 생성 이후에 호출된 생성자는 최초의 생성자가 생성한 객체를 리턴한다. 이와 같은 디자인 유형을 싱글턴 패턴이라고 한다. 주로 공통된 객체를 여러개 생성해서 사용하는 DBCP(DataBase Connection Pool)와 같은 상황에서 많이 사용된다.

### Sigleton pattern(싱글톤 패턴) 구현 방법?

싱글톤을 구현하는 방법에는 Eager Initialization, Static Block Initialization, Lazy Initialization, Thread Safe Singleton, Bill Pugh Singleton Implementaion, Enum Singleton 등 다양한 방법이 있습니다. 이중 가장 널리 쓰이는 **Bill Pugh Singleton Implementaion** 으로 구현하는 방법을 소개해드리도록 하겠습니다.

~~~JAVA
public class Singleton {

    private Singleton(){}

    private static class SingletonHelper{
        private static final Singleton INSTANCE = new Singleton();
    }

    public static Singleton getInstance(){
        return SingletonHelper.INSTANCE;
    }
}
~~~

해당 코드는 synchronized를 사용하지 않기 때문에 성능 저하와 멀티 스레드의 환경에서 모두 유용하여 널리 쓰입니다.

간략하게 싱글톤 패턴에 대해 소개해드렸는데, 싱글톤 패턴의 경우 무작정 사용하기보다는 정말 필요한 상황에서만 사용해야 메모리 관리면에서도 효율적으로 관리할 수 있습니다. 이점 참고하셔서 효율적인 코드 작성하셨으면 좋겠습니다. 감사합니다.


<br><br><br>

출처

- [[생성 패턴] 싱글톤(Singleton) 패턴을 구현하는 6가지 방법](https://readystory.tistory.com/116)
