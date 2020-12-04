---
title: "[캐시(Cache)] redis VS ehcache VS memcached"
date: 2020-12-04T16:40:20+09:00
categories: ["backend"]
---

최근 캐시와 관련된 개발을 하게 되었는데, 접해보지 못했던 부분이라 기본적인 베이스를 학습하고자 글을 정리해본다.

<br><br>

# 캐시(Cache)

캐시란 **지역성을 데이터를 미리 복사해 임시 저장을하여 더 빠른 속도로 데이터에 접근하는 장소**이다.

<center>
    <img src="https://upload.wikimedia.org/wikipedia/commons/c/c6/%EB%A9%94%EB%AA%A8%EB%A6%AC%EA%B3%84%EC%B8%B5%EA%B5%AC%EC%A1%B0%EA%B7%B8%EB%A6%BC1.png" width="40%" height="40%" text-align="center">
</center>

위의 **모든 계층구조에는 캐시가 존재**하고 계층 구조가 위로 올라갈수록 속도는 빠르고 용량이 작다. 그러므로 속도와 용량을 모두 확보하기에는 너무 많은 비용이들어 데이터 저장 공간은 속도와 용량에 따라 특성에 맞게 사용하여야한다.

<br>

## 캐시의 작동 방식

- 원본 데이터와는 별개로 자주 쓰이는 데이터들을 복사해둘 캐시 공간을 마련한다.

    - 캐시 공간은 상수 시간 등 낮은 시간 복잡도로 접근 가능한 곳을 사용

- 데이터 요청시 원본 데이터를 찾기 전에 캐시의 내부부터 탐색

    - 캐시에 원하는 데이터가 없거나 **(Cache miss)** 너무 오래되었을 경우 **(Expiration)** 원본 데이터를 탐색하여 데이터를 가져온다.

        - 원본 데이터에서 데이터를 가져올 때 캐시에도 해당 데이터를 복사하거나 혹은 갱신한다.

    - 캐시에 원하는 데이터가 있으면 데이터 탐색을 진행하지않고, 캐시로부터 해당 데이터를 제공한다. **(Cache hit)**

- 캐시 공간은 비교적 작으므로 안 쓰는 데이터부터 삭제하여 공간을 확보한다. **(Eviction)**

<br>

## Local Cache VS Global Cache

**Local Cache**

<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FonbQD%2FbtqzVomflDx%2FjhJswhk8MhXGkcq4nXeka0%2Fimg.png
" width="60%" height="60%" text-align="center">


- 서버마다 캐시를 따로 저장한다.

- 다른 서버의 캐시를 참조하기 어렵다.

- 서버 내에서 작동하기 때문에 속도가 빠르다.

- 로컬 서버 장비의 Resource를 이용한다. (Memory, Disk)

<br>

**Global Caache**

<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FeatCQz%2FbtqzU9XaJE9%2FgKccWIike5HO15fzhzr1s1%2Fimg.png
" width="60%" height="60%" text-align="center">

- 여러 서버에서 Cache Server에 접근하여 사용하는 캐시

- 데이터를 분산하여 저장할 수 있다.

    - Replication : 데이터 복제

    - Sharding : 데이터 분산 저장

- Local Cache에 비해 상대적으로 느리다

    - 네트워크 트래픽 때문에

- 별도의 Cache Server를 이용하기 때문에 서버간 데이터 공유 원할

<br>

## EHcache

- EHcahce는 오픈 소스 기반의 Local Cache이다.

- Java의 대표적인 캐시 엔진중 하나

- 속도가 빠른 경량 Cache이다.

- Disk, Memory에 저장이 가능한 형태

- 서버간 분산 캐시를 지원한다(동기/비동기 복제)

    - 대규 서비스에서 캐시 서버 여럿을 클러스터로 묶을 수 있는 기능 제공

- Java 메소드에 캐싱을 적용함으로써 캐시에 보관된 정보로 메소드의 실행 횟수를 줄일 수 있다.

- ehcache는 데몬을 가지지 않고 Spring 내부적으로 동작하여 캐싱 처리를 한다.

    - 서버 애플리케이션과 라이프사이클이 같다

<br>

## Redis

- 다양한 데이터 구조를 지원하며 multilingual에서 유리하다.

- In-Memory Databases

- 기본적으로 single thread

    - 데이터 손실 없이 클러스터링을 통해 수평 확장 가능.

- redis 메모리가 날라가도 스냅샷을 제공하여 복구가 가능하다.

- 다양한 API를 제공한다.

    - Eviction 정책을 6가지 제공한다.

- 관리하는 데이터를 복제할 수 있다.

<br>

## Memcache

- HTML과 같이 상대적으로 작고 정적인 데이터를 캐싱할 때 좋다.

- 메모리 관리가 redis만큼 정교하지는 않지만, 메타 데이터에 대한 메모리 리소스를 비교적 적게 소비하여 간단한 사용에 적합하다.

- 멀티 스레이드이다.

    - 쉽게 확장할 수 있지만 해싱 사용 여부에 따라 캐시된 데이터의 일부 또는 전부를 잃는다.

- ket bane울 250bytes로 제한하고 일반 문자열로만 작동한다.


<br><br>

대략적으로 3개에 대해 정리가 되는 것 같은데, 아직 명확하게 어떤 상황에서 무엇이 적합한지 잘 모르겠다..더 딥다이빙해봐야겠다..

<br><br><br><br><br>

**참고** 

- [우아한 tech youtube](https://www.youtube.com/c/%EC%9A%B0%EC%95%84%ED%95%9CTech/search?query=%EC%BA%90%EC%8B%9C&ab_channel=%EC%9A%B0%EC%95%84%ED%95%9CTech)

- https://deveric.tistory.com/71

- https://postitforhooney.tistory.com/entry/DBRedisRedis%EC%97%90-%EB%8C%80%ED%95%B4%EC%84%9C-%EA%B3%B5%EB%B6%80%ED%95%98%EA%B8%B0-Redis-vs-Ehcache-vs-Memcached-%EB%B9%84%EA%B5%90%ED%95%98%EB%A9%B0-%ED%8C%8C%EC%95%85%ED%95%98%EA%B8%B0