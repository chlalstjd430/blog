---
title: "카프카 개념"
date: 2020-11-16T22:16:35+09:00
categories: ["kafka"]
---

# 카프카 개념 정리

## 카프카란?

기존에 end-to-end 연결 방식의 아키텍처로 인하여 데이터 연동의 복잡성이 증가되었다. 이에 링크드인에서 데이터가 갑자기 많아지더라도 확장이 용이하고, 데이터를 전송 실시간 처리에 대한 필요성을 느꼈고 아래와 같은 특징을 가진 **카프카**를 개발하였다.

![카프카](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fdm1kl4%2FbtqAavK4BAR%2FEKvMKV4xYnbkDkSki9gV80%2Fimg.png)

- 프로듀서/컨슈머 분리

- 메시지 데이터를 여러 컨슈머에게 허용

- 높은 처리량을 위한 메시지 최적화

- 스케일 아웃 가능

- 관련 생태계 제공

<br><br><br>

## 브로커(broker)

- 실행된 카프카 애플리케이션 서버 중 1대

    - 2대 이상일 수 있지만, 실제 운영 환경에서 그런 경우는 굉장히 드뭄

- 3대 이상의 브로커로 클러스터구성

- 주키퍼와 연동

    - 주키퍼의 역할 : 메타데이터(브로커id, 컨트롤러id 등) 저장

- n개 브로커 중 1대는 컨트롤러(Controller) 기능 수행

    - 컨트롤러
        
        - 각 브로커에게 담당 파티션 할당 수행. 
        
        - 브로커 정상 동작 모니터링 관리. 
        
        - 컨트롤러가 누군지는 주키퍼에 저장.

<br><br><br>

## 토픽 & 파티션(Topic & Partition)

![스크린샷 2020-10-15 오전 11 45 35](https://user-images.githubusercontent.com/50758600/96070874-fcfd0880-0edb-11eb-93c8-07d4d7967c65.png)

- 메시지 분류 단위

- n개의 파티션 할당 가능

    - 파티션 수를 늘리면 처리량도 증가

    - 파티션 증가는 쉬우나 축소는 어려우니 처음 설계할 때 잘 고려할 것

- 각 파티션마다 고유한 오프셋(offset)을 까짐

    - 숫자가 클 수록 최신

- 메시지 처리순서는 파티션별로 유지 관리됨

<br><br><br>

## 프로듀서 & 컨슈머(Producer & Consumer)

![스크린샷 2020-10-15 오후 12 18 13](https://user-images.githubusercontent.com/50758600/96073026-84e51180-0ee0-11eb-8d91-1d8c39ce1c59.png)

브로커가 컨슈머로 보내는 것이 아니다.

1. 프로듀서는 레코드를 생성하여 브로커로 전송

2. 전송된 레코드는 파티션에 신규 오프셋과 함께 기록됨

3. 컨슈머는 쁘로커로 부터 레코드를 요청하여 가젹감(polling)

<br><br><br>

## 카프카 로그와 세그먼트

- 레코드는 **파일 시스템** 단위로 저장된다.

- 메시지가 저장될떄는 세그먼트 파일이 열려있음.

- 섹그먼트는 시간 또는 크기 기준으로 닫힘

- 세그먼트가 닫힌 이후일정 시간(또는 용량)에 따라 삭제 또는 압축

    - 카프카의 데이터는 보통 일정 기간/용량을 옵션을 준다

<br><br><br>

## 컨슈머와 파티션

- **파티션 개수 >= 컨슈머 개수** 로 만들어야한다.

    - 반대의 경우에는 컨슈머가 파티션을 할당받지 못하고 대기하게 된다.

- 파티션 3개, 컨슈머 3개 동작 중 컨슈머 1개가 장애가난 경우

    - **리밸런스** 가 발생하여 파티션 컨슈머 할당 재조정

    - 리밸런스가 일어나는 중에는 할당하는 과정이 중단된다.

- 2개 이상의 컨슈머 그룹이 있을 경우

    - 각 그룹별로 독립적으로 파티션을 읽음.

    - 실제로 elasticsearch/hadoop 컨슈머 그룹을 나누어 저장하는 경우도 있음.

<br><br><br>

## 리더 파티션, 팔로워 파티션

![스크린샷 2020-10-15 오후 12 44 10](https://user-images.githubusercontent.com/50758600/96074642-24f06a00-0ee4-11eb-9280-5a63fcb40db0.png)


- 리더 파티션 : Kafka 클라이언트와 데이터를 주고 받는 역할

- 팔로워 파티션

    - 리더 파티션으로 부터 레코드를 지속 복제(복제하는데 시간이 걸림).

    - 리퍼 파티션의 동작이 불가능할 경우 나머지 팔로워 중 1개가 리더로 선출됨

- ISR(In-Sync Replica) : 파티션의 리더, 팔로워의 레코드가 모두 복제되어 sync가 맞는 살태

    - ISR이 아닌 상태에서 장애가 나면 : unclean.leader.election.enable

<br><br><br>

## kafka 핵심요소 중간정리

- Broker : 카프카 애플리케이션 서버 단위

- Topic : 데이터 분리 단위. 다수 파티션 보유

- Partition : 레코드를 담고 있음. 컨슈머 요청시 레코드 전달

- Offset : 각 레코드당 파티션에 할당된 고유 번호

- Consumer : 레코드를 polling하는 애플리케이션

    - Consumer group : 다수 컨슈머 묶음

    - Consumer offset : 특정 컨슈머가 가져간 레코드의 번호

- Producer : 레코드를 브로커로 전송하는 애플리케이션

- Replication : 파티션 복제 기능

    - ISR : 리더 + 팔로워 파티션의 sync가 된 묶음

- Rack-awareness: Server rack 이슈에 대응

<br><br><br>

## 카프카 클라이언트(Kafka Client)

- kafka와 데이터를 주고받끼 위해 사용하는 java library

- producer, consumer, admin, stream 등 kafka 관련 api 제공

<br><br><br>

## 카프카 스트림즈(Kafka Streams)

- 데이터를 변환하기 위한 목적으로 사용하는 API

- 스트림 프로세싱을 지원하기 위한 다양한 기능을 제공

    - stateful 또는 stateless와 같이 상태기반 스틀미 처리 가능

    - stream api와 DSL을 동시 지원

    - kafka security 완벽 지원

    - 스트림 처리를 위한 별도 클러스터 불필요

<br><br><br>

## 카프카 커넥트(Kafka Conenct)

- 많은 경우 Kafka client를 Kafka로 데이터를 넣는 코드를 작성할떄도 있지만, Kafka connect를 통해 data를 import/export 할 수 있음

- 코드 없이 configuration으로 데이터를 이동시키는 것이 목적

<br><br><br>

## 카프카 미러 메이커(Kafka Mirror maker)

- 특정 카프카 클러스터에서 다른 카프카 클러스터로 Topic 및 Record를 복제하는 Standalone tool

- 클러스터간 토픽에 대한 모든 것을 복제하는 것이 목적




<br><br><br>
<br><br><br>
<br><br><br>


#### 참고

- [아파치 카프카 입문 강의](https://tacademy.skplanet.com/live/player/onlineLectureDetail.action?seq=183)

- [kafka documentation](https://kafka.apache.org/documentation/#introduction)