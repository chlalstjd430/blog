---
title: "[Docker/도커] 03.도커 컨테이너 배포 - 컨테이너로 애플리케이션 실행하기"
date: 2020-09-04T21:04:07+09:00
categories: ["Docker"]
tags: ["Devops"]
---

더 자세한 내용은 부디 [책](http://www.yes24.com/Product/Goods/70893433)을 참고하길 부탁드립니다.

# 목차

1. [[Docker/도커] 01. 도커의 기초 - 도커란 무엇인가?](http://blog.cmstown.com/2020/09/docker_01/)

2. [[Docker/도커] 02. 도커의 기초 - 도커를 사용하는 의의 / 도커 설치하기](http://blog.cmstown.com/2020/09/docker_02/)

3. 👉 [Docker/도커] 03.도커 컨테이너 배포 - 컨테이너로 애플리케이션 실행하기 👈

4. [[Docker/도커] 04.도커 컨테이너 배포 - 도커 이미지 다루기](http://blog.cmstown.com/2020/09/docker_04/)

5. [[Docker/도커] 05.도커 컨테이너 배포 - 도커 컨테이너 다루기](http://blog.cmstown.com/2020/09/docker_05/)

6. [[Docker/도커] 06.도커 컨테이너 배포 - 운영과 관리를 위한 명령](http://blog.cmstown.com/2020/09/docker_06/)

7. [[Docker/도커] 07.도커 컨테이너 배포 - 도커 컴포즈로 여러 컨테이너 실행하기](http://blog.cmstown.com/2020/09/docker_07/)

8. [[Docker/도커] 08.컨테이너 실전 구축 및 배포](http://blog.cmstown.com/2020/09/docker_08/)

<br><br><br>

# 🐳 03.도커 컨테이너 배포 - 컨테이너로 애플리케이션 실행하기 🐳

도커의 기본 개념과 의의를 알아보았으니 도커의 기본 조작 방법을 배우고 애플리케이션을 배포하는 과정까지 알아보자.

먼저 도커 이미지와 도커 컨테이너의 관계를 이해하자. 

- 도커 이미지

    > 도커 컨테이너를 구성하는 파일 시스템과 실행할 애플리케이션 설정을 하나로 합친 것으로 컨테이너를 생성하는 템플릿 역할을 한다.

- 도커 컨테이너

    > 도커 이미지를 기반으로 생성되며, 파일 시스템과 애플리케이션이 구체화 돼 실행되는 상태이다.

도커 이미지 하나로 여러 개의 컨테이너를 생성할 수 있다.

한번 실제로 도커 이미지로 도커 컨테이너를 만드는 과정을 살펴보자. 먼저 gihyodocker/echo:latest라는 도커 이미지를 받아온다. docker image pull 명령으로 받을 수 있고 터미널에 다음과 같이 작성한다.

~~~
$ docker image pull gihyodocker/echo:latest
latest: Pulling from gihyodocker/echo
723254a2c089: Pull complete 
abe15a44e12f: Pull complete 
409a28e3cc3d: Pull complete 
503166935590: Pull complete 
abe52c89597f: Pull complete 
ce145c5cf4da: Pull complete 
96e333289084: Pull complete 
39cd5f38ffb8: Pull complete 
22860d04f4f1: Pull complete 
7528760e0a03: Pull complete 
Digest: sha256:4520b6a66d2659dea2f8be5245eafd5434c954485c6c1ac882c56927fe4cec84
Status: Downloaded newer image for gihyodocker/echo:latest
docker.io/gihyodocker/echo:latest
~~~

내려받은 이미지를 docker container run 명령으로 다음과 같이 실행한다. -t/-p 옵션에는 나중에 다루도록 할태니 일단 현재 명령어의 동작을 확인하자.

~~~
$ docker container run -t -p 9000:8080 gihyodocker/echo:latest
2020/09/04 12:47:02 start server
~~~

지금 만든 컨테이너는 옵션을 통해 포트 포워딩이 적용돼 있다. 도커 실행 환경의 포트 9000을 거쳐 HTTP 요청을 전달받는다.

이후 터미널에 "curl http://localhost:9000/" 을 입력하거나 해당 주소를 브라우저를 통해 접속해보면 "Hello World!"라는 문장을 볼 수 있다.

<br><br>

## 간단한 애플리케이션과 도커 이미지 만들기

도커 컨테이너를 어떻게 만들고 실행하는지에 대한 감을 잡기 위해 Go 언어로 만든 간단한 웹 서버를 도커 컨테이너에서 실행해 보자.


main.go라는 파일에 다음 코드를 작성한다.

~~~go
package main

import (
	"fmt"
	"log"
	"net/http"
)

func main() {
	http.HandleFunc("/", func(w http.ResponseWriter, r * http.Request) {
		log.Println("received request")
		fmt.Fprintf(w, "Hello Docker!!")
	})

	log.Println("start server")
	server := &http.Server {Addr: ":8080"}
	if err := server.ListenAndServe(); err != nil {
		log.Println(err)
	}
}
~~~

위 코드는 간단한 서버 애플리케이션으로 다음과 같은 기능을 갖는다.

- 모든 HTTP 요청에 대해 "Hello Docker!"라는 응답을 보낸다.

- 포트 8080로 요청을 받는 서버 애플리케이션으로 동작한다.

- 클라이언트로부터 요청을 받으면 received request라는 메시지를 표준으로 출력한다.

이 Go 언어 코드를 도커 컨테이너에 배치한다. main.go 파일을 포함하는 새로운 도커 이미지를 만드는 것이다. 이를 위해 main.go파일과 같은 디렉터리에 Dockerfile을 다음과 같이 작성한다.

~~~
FROM golan:1.9

RUN mkdir /echo
COPY main.go /echo

CMD ["go", "run", "/echo/main.go"]
~~~

여기서 사용된 FROM, RUN 같은 키워드를 인스트럭션(명령)이라고 한다.

인스트럭션은 여러 가지 있으나, 기본적인 것부터 살펴보자.

#### FROM

FROM 인스트럭션은 도커 이미지의 바탕이 될 베이스 이미지를 지정한다. Dockerfile로 이미지를 빌드할 떄 먼저 FROM 인스트럭션에 지정된 이미지를 내려받는다.

FROM에서 받아오는 도커 이미지는 도커 허브(Docker Hub)라는 레지스트리에 공개된 것이다. 도커는 FROM에서 지정한 이미지를 기본적으로 도커 허브 레지스트리에서 참조한다.

1.9라고 된 부분은 **태그**라고 한다. 각 이미지의 버전등을 구별하는 식별자이다.

#### RUN

RUN 인스트럭션은 도커 이미지를 실행할 때 컨테이너 안에서 실행할 명령을 정의하는 인스트럭션이다. 인자로 도커 컨테이너 안에서 실행할 명령을 그대로 기술한다.

#### COPY

COPY 인스트럭션은 도커가 동작 중인 호스트 머신의 파일이나 디렉토리를 도커 컨테이너 안으로 복사하는 인스트럭션이다.

위 예제에서는 호스트에서 작성한 main.go 파일을 도커 컨테이너 안에서 실행할 수 있도록 컨테이너 안으로 복사하는 데 이 인스트럭션을 사용했다.

#### CMD

CMD 인스트럭션은 도커 컨테이너를 실행할 떄 컨테이너 안에서 실행할 프로세스를 지정한다. RUN은 애플리케이션 업데이트 및 배치에, CMD는 애플리케이션 자체를 실행하는 명령어라고 이해하면 된다.

즉, 셀 스크립트로 치면 다음과 같다.

~~~shell
$ go run /echo/main.go
~~~

위 명령을 CMD 인스트럭션에 기술하면 다음가 같이 명령을 공백으로 나눈 배열로 나타낸다.

~~~docker
CMD ["go", "run", "/echo/main.go"]
~~~

<br><br>

## 도커 이미지 빌드하기

이제 위 파일을 작성했으면 **docker image build** 명령으로 도커 이미지를 빌드한다.

docker image build 명령은 도커 이미지를 빌드하기 위한 명령이다. docker image build 명령의 기본 문법은 다음과 같다.

-t 옵션으로 이미지명을 지정한다. 태그명도 지정할수 있으며, 생략 시에는 latest 태그가 붙는다.

-t 옵션으로 이미지명은 반드시 지정해야 한다고 생각하는 편이 좋다. 물론 작성하지 않아도 빌드 자체는 가능하다. 다만, 이미지명 없이는 해시값만으로 이미지를 구별해야 하므로 사용하기가 상당히 번거롭다.

~~~terminal
docker image build -t 이미지명[:태그명] Dockerfile의_경로
~~~

그럼 다음과 같이 도커 이미지를 빌드 해보자.

~~~terminal
cd  Dockerfile의_경로
docker image build -t example/echo:latest .

Sending build context to Docker daemon  3.072kB
Step 1/4 : FROM golang:1.9
1.9: Pulling from library/golang
55cbf04beb70: Pull complete 
1607093a898c: Pull complete 
9a8ea045c926: Pull complete 
d4eee24d4dac: Pull complete 
9c35c9787a2f: Pull complete 
8b376bbb244f: Pull complete 
0d4eafcc732a: Pull complete 
186b06a99029: Pull complete 
Digest: sha256:8b5968585131604a92af02f5690713efadf029cc8dad53f79280b87a80eb1354
Status: Downloaded newer image for golang:1.9
 ---> ef89ef5c42a9
Step 2/4 : RUN mkdir /echo
 ---> Running in c4b677a54953
Removing intermediate container c4b677a54953
 ---> d59e0db45149
Step 3/4 : COPY main.go /echo
 ---> a6bbddf2dfa0
Step 4/4 : CMD ["go", "run", "/echo/main.go"]
 ---> Running in 160bd2760fe8
Removing intermediate container 160bd2760fe8
 ---> 2c23be37add6
Successfully built 2c23be37add6
Successfully tagged example/echo:latest
~~~

위와 같이 step에 따라 인스트럭션을 수행하고 성공한 것을 확인할 수 있다.
그리고 위에 이미지명에 '/'가 추가되었는데, 이는 이미지명의 충돌을 피하기 위해 추가된 네임스페이스이다. 가급적이면 네임스페이스를 활용하여 이미지명의 충돌을 예방하자.

**docker image ls** 명령어로 이미지의 **REPOSITORY , TAG, IMAGE ID, CREATED, SIZE 값 등을 확인할 수 있다.

~~~
$ docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
example/echo        latest              2c23be37add6        2 minutes ago       750MB
golang              1.9                 ef89ef5c42a9        2 years ago         750MB
gihyodocker/echo    latest              3dbbae6eb30d        2 years ago         733MB
~~~

<br><br>

## 도커 컨테이너 실행

이미지를 생성했으니 docker container run 명령으로 컨테이너를 실행해 보겠다. 정상적으로 실행이 되면 'start sever'라는 로그가 다음과 같이 출력될 것이다.

~~~
choeminseong-ui-MacBook-Pro:01.test_go choeminseong$ docker container run example/echo
2020/09/04 17:49:01 start server
~~~

이후 컨테이너를 종료하고 싶다면 'Ctrl+C'를 입력하면된다.

#### 포트 포워딩

서버가 정상적으로 실행되었으니 로컬 환경에서 curl 명령어를 이용하여 get 요청을 8080포트에 보내보겠다.

~~~
choeminseong-ui-MacBook-Pro:~ choeminseong$ curl http://localhost:8080/
curl: (7) Failed to connect to localhost port 8080: Connection refused
~~~

그러면 위와 같이 Connection이 거절되었다는 문구가 나온다. 이것만 보면 애플리케이션이 리스닝하는 포트는 로컬 환경의 포트 8080이 아닌 것 같다. 이 지점에도 도커만의 특징이 있다.

도커 컨테이너는 가상 환경이지만, **외부에서 봤을 때 독립된 하나의 머신처럼 다룰 수 있다는 특징**이 있다. echo 애플리케이션은 8080 포트를 리스닝하고 있지만, 이 포트는 **컨테이너 포트라고 해서 컨테이너에 한정된 포트**다. 즉, curl은 컨테이너 안에서 실행해야 올바른 응답을 받을 수 있다는 것이다.

이처럼 HTTP 요청을 받는 애플리케이션을 사용하려면 컨테이너 밖에서 온 요청을 컨테이너 안에 있는 애플리케이션에 전달해줘야 한다. 그 역할을 담당하는 것이 바로 도커의 **포트 포워딩** 이다.

포트 포워딩을 사용하기 전에 앞서 실행한 컨테이너를 다음과 같이 정지시킨다.

~~~
stop $(docker container ls --filter "ancestor=example/echo" -q)
~~~

docker container run 명령어에 **-p** 옵션을 붙이면 포트 포워딩을 지정할 수 있다. -p 옵션값은 **호스트_포트:컨테이너_포트** 형식으로 기술하면 된다. 그러면 호스트 포트 9000을 컨테이너 포트 8080에 연결하도록 포트 포워딩을 적용하고 curl 명령어를 실행해보자.

~~~
$ docker container run -d -p 9000:8080 example/echo:latest
4a671813692ecc6bd7b4b3d0fb8f62e8bd4e8150ce8486611d578e58ff67ed22

$ curl http://localhost:9000/
Hello Docker!!
~~~

이제는 "Hello Docker!!"이라는 문장을 잘 받은걸 확인할 수 있다.  
이렇게 해서 도커 이미지 생성, 컨테이너 실행, 포트 포워딩까지 도커의 기본 사용법을 알아봤다.

추가적으로 다음과 같이 호스트 포트를 생략할 수 있다.  
~~~
$ docker container run -d -p 8080 example/echo:latest
~~~  
이럴 경우 "docker container ls" 명령어를 이용해 어떤 포트가 할당됐는지 알 수 있다.  
~~~
$ docker container run -d -p 8080 example/echo:latest
32617814466b39ee37d7a29ce8a0e615409825efa69f73d2c929dd68fbbb23e6

$ docker container ls
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                     NAMES
32617814466b        example/echo:latest   "go run /echo/main.go"   6 seconds ago       Up 6 seconds        0.0.0.0:32768->8080/tcp   naughty_kalam
~~~

<br>
<br>
<br>

**참고**

[도커/쿠버네티스를 활용한 컨테이너 개발 실전 입문](http://www.yes24.com/Product/Goods/70893433)

[초보를 위한 도커 안내서](https://subicura.com/2017/01/19/docker-guide-for-beginners-1.html)