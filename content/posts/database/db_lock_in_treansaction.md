---
title: "InnoDB Lock과 Isolation"
date: 2020-11-19T16:32:20+09:00
categories: ["database"]
---

# InnoDB Lock과 Isolation

앞서 [게시글](http://blog.cmstown.com/2020/11/transcation/)을 통해 Transaction과 Isolation에 대해 공부한 내용을 정리해보았다. 그런데 개발도중 이 isolation level을 이해하며 적용하는데, 문득 어떻게 각 isolation level 어떻게 isolation 문제점을 보장해주는 궁금해졌다. 즉 isolation에 따라 어떻게 트랜잭션이 
데이터를 보호해주는지 정확히 파악할 필요가 있었다. 이에 좋은 블로그의 글을 보게되었고, 여기에 내 종합적인 의견들을 반영해서 정리해보고자한다.

설명에 앞서 트랜잭셔이나 독립성에 대해 잘 모른다면, [이 글](http://blog.cmstown.com/2020/11/transcation/)을 읽고 시작하면 좋을 것 같다.

<br><br><br>

# InnoDB LOCK

InnoDB는 transaction의 ACID 원칙과 동시성을 최대한 보장하기 위해 다양한 종류의 lock을 사용한다. lock은 한 트랜잭션이 commit/rollback 되기전까지 진행된다. lock의 종류는 아래와 같다.

- Shared and Exclusive Locks

- Intention Locks

- Record Locks

- Gap Locks

- Next-Key Locks

- Insert Intention Locks

- AUTO-INC Locks

이중에서 isolation과 관련된 **Shared and Exclusive Locks, Record Locks, Gap Locks** 에 대해 집중적으로 살펴보자. 이 외 다른 부분은 [공식 문서(MySQL 5.6 기준)](https://dev.mysql.com/doc/refman/5.6/en/innodb-locking.html)을 읽어보자.

<br>

## Shared and Exclusive Locks

InnoDB에서는 row-level locking으로 shared locks(S lock)과 exclusive locks(X lock)를 제공한다.

### Shared Lock(S Lock)

-  S lock은 read에 대한 lock이다. 

- SELECT 쿼리는 lock을 사용하지 않고 DB를 읽어들인다. 하지만 **'SELECT ... FOR SHARE'나 일부 SELECT 쿼리** 는 read 작업을 수행할 때 InnoDB가 각 row에 S lock을 건다.

### Exclusive lock(X lock)

- X lock은 write에 대한 lock이다.

- **'SELECT ... FOR UPDATE'나 'UPDATE','DELETE'** 등의 쿼리를 사용할 때 row에 걸리는 lock이다.

S lock과 X lock의 사용되는 규칙은 아래와 같다.

1. 여러 transaction은 동시에 하나의 row에 **S lock**을 걸 수 있다.

    - S lock이 걸려있는 row를 다른곳에서도 동시에 읽을 수 있다.

2. S lock이 걸려있는 row에 다른 transaction의 **X lock**을 걸 수 없다.

    - S lock이 걸려있는 row를 다른 transaction이 수정/삭제 할 수 없다.

3. X lock이 걸려있는 row에는 다른 transaction이 **S lock**과 **X lock**을 걸 수 없다.

    - x lock이 걸린 row는 읽기,수정,삭제 모두 불가능하다.

<br>

## Record Locks

record lock은 row 자체가 아닌 DB의 index record에 걸리는 lock이다. record lock에도 S lock과 X lock이 존재한다.

### 예시

먼저 다음과 같이 두개의 트랜잭션이 존재한다 가정하자.

~~~sql
## Transaction A
SELECT user FROM t WHERE user_id = 1 FOR UPDATE; ## - (1)
~~~

~~~sql
## Transaction B
DELETE FROM user WHERE user_id = 1;
~~~

이후 두 트랜잭션이 다음과 같이 수행된다고 가정하자.

~~~sql
## Transaction A Start
A(1) 수행
## Transaction B Start
B(1) 수행
## Transaction A commit
## Transaction B commit
~~~

이 상황에서 A(1)이 수행될 때, **user_id=1** 값에는 X lock이 걸린다. transaction a가 아직 커밋 전에 B(1) 수행한다면 index가 **user_id=1**에 접근할 수 없다. 

<br>

## Gap Locks

Gap Lock은 DB index record의 gap에 걸리는 lock이다. 즉 index에 범위가 'user_id <= 10'로 주어질 때, user_id=1,2...10의 index에 대해 lock을 거는 개념이다. Record Lock에서 범위가 추가되는 개념이다.

### 예시

현재 user테이블에 user_id는 1만 있다고 가정해보자.
~~~sql
## Transaction A
SELECT user FROM t WHERE user_id BETWEEN 1 AND 10 FOR UPDATE; ## - (1)
~~~

~~~sql
## Transaction B
DELETE FROM user WHERE user_id = 3;
~~~

이후 두 트랜잭션이 다음과 같이 수행된다고 가정하자.

~~~sql
## Transaction A Start
A(1) 수행
## Transaction B Start
B(1) 수행
## Transaction A commit
## Transaction B commit
~~~

A(1) 수행 결과 user_id=1,2...10까지의 인덱스에 lock을 거므로, B(1) 수행시 접근이 불가능하다.

<br><br><br>

# Isolation과 Lock

그렇다면 이 lock과 isolation의 관계는 어떻게 될까? isolation에 대한 설명은 [이 글](http://blog.cmstown.com/2020/11/transcation/)에서 먼저 설명했기에 추가적인 설명은 하지않겠다. 그렇다면 isolation level에 따라 어떻게 lock을 주는지 살펴보자.

<br>

## Read Uncommitted

- Read Uncommited는 S-lock을 사용하지 않는다. 즉, X-Lock이 걸린 데이터를 읽을 수 있다는 것을 뜻한다.

<br>

## Read Committed

- Read Committed에서는 S-lock을 사용한다. 그렇기에 X-Lock이 걸린 데이터를 읽으려 할 때 S-lock을 걸려고 시도한다. 즉, X-Lock이 걸린 데이터를 읽을 수 없다는 것을 뜻한다.

- lock을 사용하는 SELECT, UPDATE, DELETE 쿼리에 대해 **record lock**만 사용하고 gap lock은 사용하지 않는다.

<br>

## Repeatable Read

- Repeatable Read에서는 lock을 사용하는 SELECT, UPDATE, DELETE 쿼리에 대해 **gap lock**과 **next-key lock**까지 사용된다.

<br>

## Serializalbe

- SELECT 쿼리가 전부 **SELECT ... FOR SHARE**로 변경된다. 즉, SELECT 쿼리 진행시 범위에 해당하는 index row에 S lock 걸린다.

<br>

대략적으로 lock과 isolation의 관계에 대해 살펴보았다. isolation이 높을 수록 lock도 많이 걸고 복잡해보일 수 있고 반대로 isolationn이 낮을 경우 많은 isolation 문제점이 발생할 것 같다. 동시성과 isolation 문제점을 모두 동시에 다 해결할 수는 없다. 그렇기에 우리가 사용하는 수준에서 적절한 isolation level을 취해 사용하는 것이 굉장히 중요한 것 같다.


<br><br><br><br><br><br><br><br><br><br>
### 출저

- https://suhwan.dev/2019/06/09/transaction-isolation-level-and-lock/

- https://dev.mysql.com/doc/refman/5.6/en/innodb-locking.html

- https://kuaaan.tistory.com/98