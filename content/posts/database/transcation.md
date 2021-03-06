---
title: "트랜잭션(Transcation) "
date: 2020-11-09T11:18:39+09:00
categories: ["database"]
---

# 트랜잭션(Transaction)

트랜잭션은 정말 중요하다, 우리가 사이드로 프로젝트를 진행하나 실무에서 적용하나 트랜잭션은 반드시 활용될 것이다. 심지어 면접에서도 이에 대해 물어보는 경우가 많다. 오늘 다시 한번 이 트랜잭션에 대해 정리하는 시간을 가져보자.

<br><br>

## 트랜잭션이란?

트랜잭션은 데이터베이스의 상태를 변화시키기 위해서 수행하나는 작업의 단위이다. 

<br><br>

## 트랜잭션의 특징(ACID)

흔히들 트랜잭션의 특징으로 ACID를 말한다. 이 ACID가 무엇인지 알아보자.

### 원자성(Atomicity)

트랜잭션이 데이터베이스에 모두 반영되던가, 아니면 전혀 반영되지 않아야 한다는 특징

### 일관성(Consistency)

어떤 하나의 트랜잭션이라도 다른 트랜잭션의 연산에 끼어들 수 없다는 특징

### 독립성(Isolation)

어떤 하나의 트랜잭션이라도 다른 트랜잭션의 연산에 끼어들 수 없다는 것

### 지속성(Durability)

트랜잭션이 성공적으로 완료되었을 경우 결과는 영구적으로 반영되어야한다는 것

<br>

## 트랜잭션의 특징(은행원 예시)

특징들만 읽었을 때, 선뜻 완벽히 이해하지 못할 수도 있다. 그렇기에 고전적인 은행원 예시를 활용해보겠다.

### 원자성(Atomicity)

은행에서 작업이 부분적으로 성공하는 일이 없아야 한다. 즉, 송금은 성공했지만 받는 사람의 계좌에 돈이 안들어오는 경우는 없어야한다.

### 일관성(Consistency)

DB의 여러 제약 조건에 맞는 상태를 보장해야한다. 즉, 송금하는 사람의 계좌 잔고가 0보다 작을 수 없다.

### 독립성(Isolation)

송긍하는 사람의 계좌에서 돈은 빠져나갔는데, 아직 받는 사람의 계좌에 돈이 들어가지 않는 상황을 볼 수 없다.

### 지속성(Durability)

은행에서 장애가 나도 성 성공한 거래 내역은 그대로 보장해야한다.


<br><br>

## 독립성(Isolation) 문제점

이러한 트랜잭션의 특징들을 이용하여 사용시에 독립성(Isolation) 관련 문제점에 유의하여 사용해야한다. 독립성 관련 문제는 무엇이 있는지 알아보자.

### Dirt Read

하나의 트랜잭션(T1)이 데이터에 접근하여 값을 'A'에서 'B'로 변경했고 아직 커밋을 하지 않았을 때, 다른 트랜젹선(T2)이 해당 데이터를 Read할 경우 변경된 'B' 데이터를 읽는다. 그러나 이때 T1 이 최종 커밋을 하지 않고 종료된다면 문제가 생기게 된다.

### Non-Repeatable Read

트랜잭션 내에서 같은 쿼리를 두 번 수행했는데, 그 사이에 다른 트랜잭션이 값을 수정 또는 삭제하는 바람에 두 쿼리 결과가 다르게 나타나는 현상을 말한다

### Phantom Read

한 트랜잭션 안에서 일정범위의 레코드를 두번 이상 읽을 때, 첫 번재 쿼리에서 없던 유령 레코드가 두번째 쿼리에서 나타나는 현상을 말한다.

<br><br>

## 트랜잭션 격리 수준

독립성과 관련된 문제점 때문에, ANSI 표준에서 트랜잭션의 격리성과 동시 처리 성능 사이의 Trade-off를 두고 4단계 격리수준을 나누었다. 내려갈수록 격리 수준이 높아져서 언급된 이슈는 적게 발생하지만 동시 처리 성능은 떨어진다.

참고로, 트랜잭션이 발생하면 락(Lock)이 걸리는 R 은 공유락 , CUD는 배타적 락이 걸린다.

### Read Uncommitted(레벨 0)

하나의 트랜잭션에서 커밋하지 않은 데이터에 다른 트랜잭션이 접근 가능하다. 즉, 커밋하지 않은 데이터를 읽을 수 있다.

**장점**

- 동시 처리 성능은 가장 높다

**단점**

- Dirty Read, Non-Repeatable Read, Phantom Read 문제 발생여부가 있다.

### Read Committed(레벨 1)

커밋이 완료된 데이터만 읽을 수 있다.

데이터베이스들은 보통 Read Committed 를 default로 삼는다.

**장점**

- Dirty Read가 발생한 여지는 없다.

**단점**

- Non-Repeatable Read, Phantom Read 발생 여부

### Repeatable Read(레벨 2)

트랜잭션 내에서 한번 조회한 데이터를 반복해서 조회해도 같은 데이터가 조회된다.

**장점**

- Dirty Read , Non-Repeatable Read는 발생하지 않는다.

**단점**

- Phantom Read 발생

### Serializable(레벨 3)

가장 엄격한 격리 수준

**장점**

- 모든 문제점을 예방할 수 있다.
- 두 트랜잭션이 간섭을 안 받는것처럼 실행된다.

**단점**

- 동시 처리 성능은 가장 낮다