---
title: "[Spring(스프링)] @Order"
date: 2020-12-06T22:22:26+09:00
categories: ["spring"]
---

# @Order

최근 레디스 분산락으로 구현한 AOP와 @Transactional을 동시에 사용할 일이 생겼는데, 이 둘의 AOP 실행 순서에 있어서 구현한 AOP가 우선적으로 처리되어야했다.

이에 @Order값을 확인하고 조정할 필요가있었다. @Order는 **숫자가 작을 수록 우선순위가 높다.** 사용방법 또한 간단한데, **@Order(정수)** 로 사용하면된다. 예제는 아래와 같다.

~~~java
@Aspect
@Order(0)
public class TestAspect {

    public void printOrder(JoinPoint jp){
        System.out.println("order 0");
    }
}
~~~

위의 코드와 같이 어노테이션을 추가하면된다.

<br>

## @Transactional의 order값은?

그럼 내가 겪었던 문제를 해결하기 위해 @Transactional의 order값을 확인해보았다. 확인 결과, **Ordered.LOWEST_PRECEDENCE**로 정수로 환산하면 Integer의 MAX 값과 같았다. 즉, @Transactional의 Order값은 가장 나중에 처리되도록 설정되어있었다. 이로써 내가 구현하고자하는 바가 보장이되었음을 확인할 수 있었다.