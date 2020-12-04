---
title: "[Spring(스프링)] 알고 쓰는 @Transactional"
date: 2020-11-09T14:51:26+09:00
categories: ["스프링"]
---

# 알고 쓰는 @Transactional

스프링으로 프로젝트를 진행하다보면 @Transactional 어노테이션을 많이들 활용한다. 그런데 동아리나 다른 스프링 개발자들 중 해당 어노테이션의 default값이 어떤것을 의미하는지도 모르고 사용하는 분들이 종종 있다. 부끄럽지만 나 역시도 옛날에 그랬다. 당연히 서비스의 메소드들에 써야하는 것인 줄 알았고, 정확히 의미하는 것이 무엇인지도 몰랐다. 이에 보다 확실하게 @Transactional에 대해 알고 쓸 수 있도록 글을 작성해본다.

## @Transactional이란?

스프링에서 @Transactional 어노테이션을 이용하면 트랜잭션의 범위 및 규칙을 정의할 수 있다. 트랜잭션에 대해 잘 모른다몬 [이 글](http://blog.cmstown.com/2020/11/transcation/)을 참고해보자.

기본적으로 @Transactional에서 사용하는 옵션은 아래와 같다.

<br><br>

### propagation(전파옵션)

~~~java
@Transactional(propagation = Propagation.REQUIRED)
~~~

전파옵션이란 트랜잭션 동작 도중 다른 트래잭션을 호출하는 상황에 선택할 수 있는 옵션이다.

#### REQUIRED(defuat)

- 디폴트 속성으로 붐모 트랜잭션 내에서 실행하며 부모 트랜잭션이 없을 겨웅 새로운 트랜잭션을 생성한다.

#### SUPPORTS

- 이미 시작된 트랜잭션(부모 트랜잭션)이 있으면 참여하고 그렇지 않으면 트랜잭션 없이 진행하게 만든다.

#### REQUIRES_NEW

- 부모 트랜잭션 유무에 상과없이 새 트랜잭션을 시작

- 부모 트랜잭션이 존재할 경우 부모 트랜잭션을 잠시 보류

#### MANDATORY

- REQUIRED와 비슷하게 이미 시작된 트랜잭션이 있으면 참여하낟.

- 반면에 트랜잭션이 시작된 것이 없으면 새로 시작하는 대신 예외를 발생시킨다.

- 혼자서는 독립적으로 트랜잭션을 진행하면 안되는 경우에만 사용

#### NOT_SUPPORTED

- 트랜잭션을 사용하지 않게 한다.

- 이미 진행 중인 트랜잭션(부모 트랜잭션)이 있으면 보류시킨다.

#### NEVER

- 부모 트랜잭션이 존재할 경우 exception 발생

#### NESTED

- 이미 진해중인 트랜잭션이 있으면 중첩 트랜잭션을 시작한다.

- 중첩 트랜잭션은 트랜젹션 안에 트랜잭션을 만드는 것.

- 진행인 작업이 실패하더라도 DB에 로그를 남겨야하는 경우에 사용된다.


<br><br>

### isolation(격리수준)

~~~java
    @Transactional(isolation = Isolation.DEFAULT)
~~~

isolation에 대한 내용은 [이 글](http://blog.cmstown.com/2020/11/transcation/)을 읽어보면 도움이된다.

#### DEFAULT

- 기본 격리 수준으로 DB의 isolation level을 따른다.

- mysql의 경우 REPEATABLE_READ이다.

#### READ_UNCOMMITED

#### READ_COMMITED

#### REPEATABE_READ

#### SERIALIZABLE


<br><br>

### readOnly

~~~java
@Transactional(readOnly = false)
~~~

#### true일 경우
]
- readOnly는 트랜잭션을 읽기 전용으로 설정할 수 있다

- 특정 트랜잭션 안에서 쓰기 작업이 일어나는 것을 의도적으로 방지하기 위해 사용할 수도 있다.

- 일반적으로 읽기 전용 트랜잭션이 시작된 이후 insert, update, delete 같은 작업이 진행되면 예외가 발생한다.


<br><br>

### 트랜잭션 롤백(rollbackFor, follbackForClassName)

~~~java
@Transactional(rollbackFor = IllegalArgumentException.class, noRollbackFor = NullPointerException.class)
~~~

- 기존본적으로 런타임 예외가 발생하면 롤백된다.

- checked exception의 경우 발생해도 커밋한다.

    - 체크 예외는 예외적인 상황에서 사용되기보다는 리턴 값을 대신해서 비즈니스적인 의미를 담은 결과를 돌려주는 용도로 많이 사용되기 때문이다.

#### rollbackFor

- 특정 예외가 발생시 강제로 Rollback

#### noRollbackFor

- 특정 예외 발생시 Rollback되지 않음.


<br><br>

### timeout

~~~java
@Transactional(timeout = -1)
~~~

- 지정한 시간 내에 해당 메소드 수행이 완료되지 않는 경우 rollback 수행

- default는 -1로 no timeout을 뜻한다.


<br><br>

앞으로 @Transactional 을 활용하는데 있어서 위에부분만 잘 숙지하면 목적과 의도에 맞게 원하는 트랜잭션을 구현할 수 있을 것이다.


<br><br><br><br><br><br>
<br><br><br>




#### 참고

- https://goddaehee.tistory.com/167
