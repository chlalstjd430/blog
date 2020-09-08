---
title: "[Docker/도커] 05.도커 컨테이너 배포 - 도커 컨테이너 다루기"
date: 2020-09-05T21:49:33+09:00
categories: ["Docker"]
tags: ["Devops"]
---

더 자세한 내용은 부디 [책](http://www.yes24.com/Product/Goods/70893433)을 참고하길 부탁드립니다.

# 목차

1. [[Docker/도커] 01. 도커의 기초 - 도커란 무엇인가?](http://blog.cmstown.com/2020/09/docker_01/)

2. [[Docker/도커] 02. 도커의 기초 - 도커를 사용하는 의의 / 도커 설치하기](http://blog.cmstown.com/2020/09/docker_02/)

3. [[Docker/도커] 03.도커 컨테이너 배포 - 컨테이너로 애플리케이션 실행하기](http://blog.cmstown.com/2020/09/docker_03/)

4. [[Docker/도커] 04.도커 컨테이너 배포 - 도커 이미지 다루기](http://blog.cmstown.com/2020/09/docker_04/)

5. 👉 [Docker/도커] 05.도커 컨테이너 배포 - 도커 컨테이너 다루기 👈

6. [[Docker/도커] 06.도커 컨테이너 배포 - 운영과 관리를 위한 명령](http://blog.cmstown.com/2020/09/docker_06/)

7. [[Docker/도커] 07.도커 컨테이너 배포 - 도커 컴포즈로 여러 컨테이너 실행하기](http://blog.cmstown.com/2020/09/docker_07/)

8. [[Docker/도커] 08.컨테이너 실전 구축 및 배포](http://blog.cmstown.com/2020/09/docker_08/)

<br><br><br>

# 🐳 05.도커 컨테이너 배포 - 도커 컨테이너 다루기 🐳

이번 글에서는 도커 컨테이너를 다루는 방법을 알아보자. 겉에서 본 도커 컨테이너는 가상 환경이다. 파일 시스템과 애플리케이션이 함께 담겨 있는 박스라고 보면 된다. 보다 쉽게 이해하기 위해 도커 명령을 실제 실행해보며 컨테이너에 대해 알아보자.

## 도커 컨테이너의 생애주기

도커는 크게 실행 중, 정지, 파기의 3가지 상태를 갖는다. 각 컨테이너는 같은 이미지로 생성했다고 하더라도 별개의 상태를 갖는다.

#### 실행 중 상태

docker container run 명령 실행 이후 Dockerfile에 포함된 내용이 실행되면 이 애플리케이션이 실행 중인 상태가 컨테이너의 실행 중 상태가 된다. 

HTTP 요청을 받는 서버 애플리케이션임면 오류로 인해 종료되지 않는 한 실행 중 상태가 지속된다. 이에 비해 ㅁ명령이 바로 실행되고 끝나는 명령행 도구 등의 컨테이너는 실행 상태가 길지 않다.

실행이 끝나면 정지 상태가 된다.

#### 정지 상태

실행 중 상태에 있는 컨테이너를 사용자가 명시적으로 정지하거나 컨테이너에서 실행된 애플리케이션이 종료 된 경우 자동으로 정지 상태가 된다.

정지시킨 컨테이너는 다시 실행할 수 있다.(docker container ls -a 명령으로 정지된 컨테이너 확인 가능)

#### 파기 상태

정지 상태의 컨테이너는 명시적으로 파기하지 않는 이상 디스크에 그대로 남아 있다. 그러기에 명시적으로 파기를 해야 파기 상태가 된다.

## docker container run - 컨테이너 생성 및 실행

docker container run 명령은 도커 이미지로부터 컨테이너를 생성하고 실행하는 명령이다.

> docker container run [options] 이미지명[:태그] [명령] [명령인자...]

> docker container run [options] 이미지ID [명령] [명령인자...]

그 동안 앞에서 사용했던 예제를 다시 사용하면 다음과 같다.

~~~~
$ docker container run -d -p 9001:8080 chlalstjd430/echo:latest
0f6830630de986faa6955f808bff6e7e408179c7a157209d1f5902a4aaff5669
~~~~
### docker contatainer run 옵션

#### -d

백그라운드 실행을 위한 명령

#### -p 

[포트 포워딩](http://blog.cmstown.com/2020/09/docker_03/)을 위해 사용한다.

#### -e

컨테이너 내에서 사용할 환경변수 설정

#### -it

-i 와 -t 명령을 합친 것으로 실행된 bash shell에 입/출력 가능

#### --name

컨테이너에 대한 이름을 설정. 따로 정의하지 않으면 자동으로 유명 과학자나 해커들의 이름을 조합하여 생성된다.

##### –rm

프로세스 종료시 컨테이너 자동 제거

##### -v

호스트와 컨테이너 간에 디렉터리나 파일을 공유하기 위해 사용하는 옵션

<br>

## docker container ls - 도커 컨테이너 목록 보기

docker container ls 명령은 컨테이너 목록을 보여주는 명령이다.

> docker container ls [options]

~~~~
$ docker container ls
CONTAINER ID        IMAGE                      COMMAND                  CREATED             STATUS              PORTS                     NAMES
0f6830630de9        chlalstjd430/echo:latest   "go run /echo/main.go"   36 minutes ago      Up 36 minutes       0.0.0.0:9001->8080/tcp    nice_noether
32617814466b        example/echo:latest        "go run /echo/main.go"   8 hours ago         Up 8 hours          0.0.0.0:32768->8080/tcp   naughty_kalam
4a671813692e        example/echo:latest        "go run /echo/main.go"   8 hours ago         Up 8 hours          0.0.0.0:9000->8080/tcp    inspiring_poitras

~~~~

docker container ls 명령 수행시 위와 같이 나오는데, 각 항목의 의미는 다음과 같다.

항목 | 내용 
---|:---:
`CONTAINER_ID` | 컨테이너를 식별하기 위한 유일한 식별자
`IMAGE` |  컨테이너를 만드는 데 사용된 도커 이미지
`COMMAND` | 컨테이너에서 실행되는 애플리케이션 프로세스
`CREATED` | 컨테이너 생성 후 경과된 시간
`STATUS` | Up(실행 중), Exited(종료) 등 컨테이너의 실행 상태
`PORTS` | 호스트 포트와 컨테이너 포트의 연결 관계(포트 포워딩)
`NAMES` | 컨테이너의 이름

#### 컨테이너 ID만 추출하기

~~~
$ docker container ls -q
0f6830630de9
32617814466b
4a671813692e
~~~

#### 컨테이너 목록 필터링하기

> docker container ls --filter "필터명=값"

이미지로 릴터링  
~~~
$ docker container ls --filter "ancestor=chlalstjd430/echo"
CONTAINER ID        IMAGE                      COMMAND                  CREATED             STATUS              PORTS                     NAMES
0f6830630de9        chlalstjd430/echo:latest   "go run /echo/main.go"   44 minutes ago      Up 44 minutes       0.0.0.0:9001->8080/tcp    nice_noether
32617814466b        example/echo:latest        "go run /echo/main.go"   8 hours ago         Up 8 hours          0.0.0.0:32768->8080/tcp   naughty_kalam
4a671813692e        example/echo:latest        "go run /echo/main.go"   9 hours ago         Up 9 hours          0.0.0.0:9000->8080/tcp    inspiring_poitras
~~~

이름으로 필터링
~~~
$ docker container ls --filter "name=nice"
CONTAINER ID        IMAGE                      COMMAND                  CREATED             STATUS              PORTS                    NAMES
0f6830630de9        chlalstjd430/echo:latest   "go run /echo/main.go"   45 minutes ago      Up 45 minutes       0.0.0.0:9001->8080/tcp   nice_noether
~~~

#### 종료된 컨테이너 보기

-a 옵션을 사용하면 이미 종료된 컨테이너를 포함한 컨테이너 목록을 볼 수 있다.

~~~
$ docker container ls -a
CONTAINER ID        IMAGE                      COMMAND                  CREATED             STATUS                    PORTS                     NAMES
0f6830630de9        chlalstjd430/echo:latest   "go run /echo/main.go"   46 minutes ago      Up 46 minutes             0.0.0.0:9001->8080/tcp    nice_noether
cf4675024fa3        chlalstjd430/echo:latest   "go run /echo/main.go"   46 minutes ago      Created                                             compassionate_davinci
32617814466b        example/echo:latest        "go run /echo/main.go"   8 hours ago         Up 8 hours                0.0.0.0:32768->8080/tcp   naughty_kalam
4a671813692e        example/echo:latest        "go run /echo/main.go"   9 hours ago         Up 9 hours                0.0.0.0:9000->8080/tcp    inspiring_poitras
2490f2b623a3        example/echo               "go run /echo/main.go"   9 hours ago         Exited (2) 9 hours ago                              romantic_boyd
192f07f065fe        example/echo               "go run /echo/main.go"   11 hours ago        Exited (2) 9 hours ago                              lucid_mendeleev
~~~

## docker container stop - 컨테이너 정지하기

> docker container stop 컨테이너ID_또는_컨테이너명

~~~~
$ docker container ls -a
CONTAINER ID        IMAGE                      COMMAND                  CREATED             STATUS                    PORTS                     NAMES
0f6830630de9        chlalstjd430/echo:latest   "go run /echo/main.go"   46 minutes ago      Up 46 minutes             0.0.0.0:9001->8080/tcp    nice_noether
~~~~

~~~~
$ docker container stop 0f68
0f68
~~~~

~~~~
choeminseong-ui-MacBook-Pro:~ choeminseong$ docker container ls -a
CONTAINER ID        IMAGE                      COMMAND                  CREATED             STATUS                      PORTS                     NAMES
0f6830630de9        chlalstjd430/echo:latest   "go run /echo/main.go"   48 minutes ago      Exited (2) 17 seconds ago                             nice_noether
~~~~

컨테이너ID로 종료할 떄 컨테이너ID 전체값을 입력하지 않고 앞부분의 일부만 입력해도 해당 컨테이너ID와 일치하는 것을 찾아 종료해준다.

<br>

## docker container restart - 컨테이너 재시작하기

> docker container restart 컨테이너ID_또는_컨테이너명


<br>

## docker container rm - 컨테이너 파기하기

> docker container rm 컨테이너ID_또는_컨테인어명

~~~
$ docker container rm nice_noether
nice_noether
~~~

#### -f

현재 실행중인 컨테이너 삭제시 사용하는 옵션

> docker container rm -f 컨테이너ID_또는_컨테이너명

<br>

## docker container logs - 표준 출력 연결하기

docker container logs 명령을 사용하면 현재 실행 중인 특정 도커 컨테이너의 표준 출력 내용을 확인할 수 있다.

> docker container logs [options] 컨테이너ID_또는_컨테이너명

~~~
$ docker container logs 3261
2020/09/05 07:04:55 start server
~~~

#### -f

-f 옵션을 사용하면 새로 출력되는 표준 출력 내용을 계속 보여준다.

<br>

## docker container exec - 실행 중인 컨테이너에서 명령 실행하기

docker container exec 명령을 사용하면 실행 중인 컨테이너에서 원하는 명령을 실행할 수 있다.

> docker container exec [options] 컨테이너ID_또는_컨테이너명 컨테이너에서_실행할_명령

~~~
$ docker container run -t -d --name echo --rm example/echo:latest
22e874692682876b000b974029a695bc628dc7efbb3ed8f8e72b36588bc98c8d

$ docker container exec echo pwd
/go
~~~

<br>

## docker container cp - 파일 복사하기 

docker container cp 명령은 컨테이너끼리 혹은 컨테이너와 호스트 간에 파일을 복사하기 위한 명령이다. Dockerfile에 포함된 COPY 인스트럭션은 이미지를 빌드할 떄 호스트에서 복사해 올 파일을 정의하기 위한 것이고, docker container cp 명령은 실행 중인 컨테이너와 파일을 주고받기 위한 것이다.

> docker container cp [options] 컨테이너ID_또는_컨테이너명:원본파일 대상파일

> docker container cp [options 호스트_원본_파일 컨테이너ID_또는_컨테이너명:대상파일]

~~~
$ docker container cp echo:/echo/main.go
~~~




<br>
<br>
<br>

**참고**

[도커/쿠버네티스를 활용한 컨테이너 개발 실전 입문](http://www.yes24.com/Product/Goods/70893433)