---
title: "[Docker/도커] 07.도커 컨테이너 배포 - 도커 컴포즈로 여러 컨테이너 실행하기"
date: 2020-09-06T16:52:31+09:00
categories: ["Docker"]
tags: ["Devops"]
---

더 자세한 내용은 부디 [책](http://www.yes24.com/Product/Goods/70893433)을 참고하길 부탁드립니다.

# 목차

1. [[Docker/도커] 01. 도커의 기초 - 도커란 무엇인가?](http://blog.cmstown.com/2020/09/docker_01/)

2. [[Docker/도커] 02. 도커의 기초 - 도커를 사용하는 의의 / 도커 설치하기](http://blog.cmstown.com/2020/09/docker_02/)

3. [[Docker/도커] 03.도커 컨테이너 배포 - 컨테이너로 애플리케이션 실행하기](http://blog.cmstown.com/2020/09/docker_03/)

4. [[Docker/도커] 04.도커 컨테이너 배포 - 도커 이미지 다루기](http://blog.cmstown.com/2020/09/docker_04/)

5. [[Docker/도커] 05.도커 컨테이너 배포 - 도커 컨테이너 다루기](http://blog.cmstown.com/2020/09/docker_05/)

6. [[Docker/도커] 06.도커 컨테이너 배포 - 운영과 관리를 위한 명령](http://blog.cmstown.com/2020/09/docker_06/)

7. 👉 [Docker/도커] 07.도커 컨테이너 배포 - 도커 컴포즈로 여러 컨테이너 실행하기 👈

8. [[Docker/도커] 08.컨테이너 실전 구축 및 배포](http://blog.cmstown.com/2020/09/docker_08/)

<br><br><br>

# 🐳 07.도커 컨테이너 배포 - 도커 컴포즈로 여러 컨테이너 실행하기 🐳

도커를 실제 운영 환경에 적용할 때 단일 컨테이너만 이용하는 경우는 드물다. 이에 여러 컨테이너를 동작을 제어하기 위한 설정 파일이나 환경 변수를 어떻게 전달하지, 컨테이너의 의존관계를 고려할 때 포트 포워딩을 어떻게 설정해야 하는지 드으이 요소를 적절히 관리해야 한다.  
이떄 필요한 것이 **도커 컴포즈(Docekr Compose)다. 

## docker-compose 명령으로 컨테이너 실행하기
Compose는 yaml 포맷으로 기술된 설정 파일로 여러 컨테이너의 실행을 한 번에 관리할 수 있게 해준다.

우선 어떤 기능들이 있는지 살펴보자. 윈도우/macOS용 도커를 로컬에 설치한 경우 바로 사용할 수 있고, [리눅스 환경은 별도 설치](https://hcnam.tistory.com/25)가 필요하다.

먼저 컨테이너 하나를 실행해 보겠다. 그리고 같은 작업을 docker-composer를 사용해 다시 수행할 것이다.  
~~~
$ docker container run -d -p 9000:8080 example/echo:latest
9eec9e85ef46b267e40518f108910e60528fcce9c7d643b8403681160d28217c
~~~

임의의 디렉터리에서 docker-compose.yml이라는 파일명으로 다음과 같은 내용을 작성한다.  
~~~yaml
version: "3"
services:
  echo:
    image: example/echo:latest
    ports:
      - 9000:8080
~~~

여기서 version: "3" 부분은 이 docker-compose.yml 파일의 내용을 해석하는 데 필요한 문법을 선언한 것이다. 여기서 만든 example/echo:latest 이미지를 도커 컴포즈에서 사용해 보겠다.  

services 요소 아래의 echo는 컨테이너 이름으로, 그 아래에 다시 어떤 이미지를 실행할지가 정의된다. image 요소는 도커 이미지, ports는 포트 포워딩 설정을 지정한다.

이 파일을 이용해 도커 컨테이너를 실행해 보겠다. 해당 파일이 위치한 디렉터리에서 이 정의에 따라 여러 컨테이너를 한꺼번에 시작하려면 **docker-compose up** 명령을 사용하면 된다.

~~~
$ docker-compose up -d
Starting 02docker_compose_echo_1 ... done
choeminseong-ui-MacBook-Pro:02.docker_compo

$ docker container ls
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                    NAMES
7807cf538fbf        example/echo:latest   "go run /echo/main.go"   2 minutes ago       Up About a minute   0.0.0.0:9000->8080/tcp   02docker_compose_echo_1
~~~

컨테이너가 정상적으로 동작되고 있는 것을 확인했다. 그렇다면 컨테이너를 정지해보자. **docker-compose down** 명령을 사용하면 docker-compose.yml 파일에 정의된 모든 컨테이너가 정지 혹은 삭제된다.

~~~
$ docker-compose down
Stopping 02docker_compose_echo_1 ... done
Removing 02docker_compose_echo_1 ... done
Removing network 02docker_compose_default
~~~

일일이 컨테이너 ID를 확인후 지울 필요가 없어 훨씬 간단하다. 이번에는 지난번 작성한 main.go와 Dockerfile이 있는 디렉토리에 docker-compose.yml을 작성후 실행해보자.

docker-compose.yml

~~~yml
version: "3"
services:
  echo:
    build: .
    ports:
      - 9000:8080
~~~

~~~
$ docker-compose up -d --build
Creating network "01test_go_default" with the default driver
Building echo
Step 1/4 : FROM golang:1.9
 ---> ef89ef5c42a9
Step 2/4 : RUN mkdir /echo
 ---> Using cache
 ---> 24e37983e029
Step 3/4 : COPY main.go /echo
 ---> 290af47dafb2
Step 4/4 : CMD ["go", "run", "/echo/main.go"]
 ---> Running in 14c5efebddb9
Removing intermediate container 14c5efebddb9
 ---> 127a4a067c54
Successfully built 127a4a067c54
Successfully tagged 01test_go_echo:latest
Creating 01test_go_echo_1 ... done
~~~

이로써 docker-compose.yml 파일을 작성하면 기존 docker 명령을 사용해 컨테이너를 실행할 떄 매번 부여하던 옵션을 설정 파일로 관리할 수 있다. 그러나 컴포즈를 사용한 구성 관리 기능의 진가는 여러 컨테이너를 실행할 떄 발휘한다.

<br>

## 젠킨스 컨테이너 실행하기

컴포즈를 사용해 여러 컨테이너를 실행하기 위해 필요한 기본 요소를 파악하기 위해 젠킨스를 예제 삼아 실행해 보자. 다음과 같이 docekr-compose.yml 파일을 작성한다.

~~~
version: "3"
services:
  master:
    container_name: master
    image: jenkins/jenkins:2.235.5-lts
    ports:
      - 8080:8080
    volumes:
      - ./jenkins_home:/var/jenkins_home
~~~

젠킨스 이미지는 도커 허브에 올라와 있는 것을 이용한다. volumes라는 설정 항목은 호스트와 컨테이너 사이에 파일을 복사하는 것이 아니라 파일을 공유할 수 있는 매커니즘이다. Dockerfile의 COPY 인스트럭션이나 container cp 명령은 로스트와 컨테이너 사이에 파일을 복사하는 기능이었지만, volumes는 공유라는 점에서 차이가 있다. 즉, 현재 volumes를 이용하여 현재 작업 디렉토리 바로 아래에 jenkins_home 디렉터리를 젠킨스 컨테이너의 /var/jenkins_home에 마운트한다.

이제 컴포즈를 실행하는데, 이 때 -d 옵션을 사용하지 않고 포어그라운드로 실행하면 패스워드가 생성되는데 이 패스워드를 잘 복사해 놓는다.

~~~
$ docker-compose up
Creating network "02docker_compose_default" with the default driver
Pulling master (jenkins/jenkins:2.235.5-lts)...
2.142-slim: Pulling from jenkinsci/jenkins

...

master    | Sep 06, 2020 6:27:21 PM jenkins.install.SetupWizard init
master    | INFO: 
master    | 
master    | *************************************************************
master    | *************************************************************
master    | *************************************************************
master    | 
master    | Jenkins initial setup is required. An admin user has been created and a password generated.
master    | Please use the following password to proceed to installation:
master    | 
master    |                         **비밀번호**
master    | 
master    | This may also be found at: /var/jenkins_home/secrets/initialAdminPassword
master    | 
master    | *************************************************************
master    | *************************************************************
master    | *************************************************************
master    | 
master    | Sep 06, 2020 6:27:29 PM hudson.model.UpdateSite updateData
master    | INFO: Obtained the latest update center data file for UpdateSource default

...

master    | INFO: Finished Download metadata. 13,358 ms
~~~

여기서 젠킨스를 호스트 쪽 포트 8080과 포트 포워딩으로 연결하였으니 http://localhost:8080/ 에 접속 해보자.

<p align = "center">
    <img src = "https://user-images.githubusercontent.com/50758600/92332744-00ba9580-f0bb-11ea-84ca-891558539164.png">
</p>

이후 아까 복사해둔 비밀번호를 입력하면 다음과 같은 화면이 나온다.

<p align = "center">
    <img src = "https://user-images.githubusercontent.com/50758600/92332745-01532c00-f0bb-11ea-8a6d-ba5139c0253c.png">
</p>

여기서 세세한 설정은 건드릴 필요 없으니, 'Install suggested plugins'를 클릭한다. 그러면 다음과 같이 설치 과정이 나올 것이다.

<p align = "center">
    <img src = "https://user-images.githubusercontent.com/50758600/92332743-fdbfa500-f0ba-11ea-8ada-e08e63ef6529.png">
</p>

이후 플러그인 설치 후 로그인 하면 젠킨스 홈 화면이 나온다.

<br>

## 마스터 젠킨스 용 SSH 키 생성

이제 젠킨스 컨테이너를 하나 실행했다. 다응믄 좀 더 실용적인 예제로, 여기에 슬레이브 젠킨스 컨테이너를 추가해보겠다. 실제 젠킨스 운영할 때 단일 서버로 운영하기보다는 관리 기능이나 작업 실행 등은 맘스터 인스터스가 맡고, 작업을 실제로 진행하는 것은 슬레이브 인스턴스가 담당한다. 이러한 구성을 컴포즈로 만들어 보겠다.

먼저 마스터가 슬레이브에 접속할 수 있도록 마스터 컨테이너에서 SSH 키를 생성한다.  
~~~
$ docker container exec -it master ssh-keygen -t rsa -C ""
Generating public/private rsa key pair.
Enter file in which to save the key (/var/jenkins_home/.ssh/id_rsa): 
Created directory '/var/jenkins_home/.ssh'.
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /var/jenkins_home/.ssh/id_rsa.
Your public key has been saved in /var/jenkins_home/.ssh/id_rsa.pub.
~~~

지금ㅁ 만든 /var/jenkins_home/.ssh/id_rsa.pub 파일은 마스터 젠킨스가 슬레이브 젠킨스에 접속할 때 사용할 키다. 이 키를 슬레이브를 추가할 때 설정할 것이다.

<br>

## 슬레이브 젠킨스 컨테이너 생성

슬레이브 인스턴스 역할을 할 젠킨스 컨테이너를 추가한다. 마스터 컨테이너는 master, 슬레이브 컨테이너느 slave01로 각각 이름을 붙인다.

~~~
version: "3"
services:
  master:
    container_name: master
    image: jenkins/jenkins:2.235.5-lts
    ports:
      - 8080:8080
    volumes:
      - ./jenkins_home:/var/jenkins_home
    links:
      - slave01

  slave01:
    container_name: slave01
    image: jenkins/ssh-slave
    environment:
      - JENKINS_SLAVE_SSH_PUBKEY={호스트 파일 시스템의 ./jenkins_home/.ssh/id_rsa.pub 의 내용을 붙여넣는다}
~~~

#### SSH 접속 허용 설정

새로 추가하는 슬레이브 컨테이너를 만들 떄는 SSH로 접속하는 슬레이브 용도로 구성된 도커 이미지를 사용한다. 슬레이브 컨테이너 안에서 키를 받아오거나 설정해서는 안되며, 외부 환경 변수로 받아 오게 해야 한다.

#### SSH 접속 대상 설정

이제 마스터 컨테이너가 어떻게 슬레이브 컨테이너를 찾아 추가할 것인가 하는 문제가 남아 있다. IP 주소를 찾아 설정하는 방법도 있지만, 컴포즈를 사용하면 좀 더 깔끔하게 문제를 해결할 수 있다. linkks: - slave01 이 바로 그 방법이다. 이것으로 master에서 slave01이라는 이름으로 슬레이브 컨테이너를 찾아갈 수 있다.

#### 컨테이너 간의 관계 정리 및 준비 완료

지금까지 작업한 디렉토리 상태를 tree 명령으로 확인해 보자. tree가 설치되지 않았다면 [해당 블로그](https://eunguru.tistory.com/150)를 참고하자.

~~~
$ tree -a -L 2
.
├── docker-compose.yml
└── jenkins_home
    ├── .bash_history
    ├── .cache
    ├── .groovy
    ├── .java
    ├── .lastStarted
    ├── .owner
    ├── .ssh
    ├── com.cloudbees.hudson.plugins.folder.config.AbstractFolderConfiguration.xml
    ├── config.xml
    ├── copy_reference_file.log
    ├── hudson.model.UpdateCenter.xml
    ├── hudson.plugins.git.GitTool.xml
    ├── identity.key.enc
    ├── init.groovy.d
    ├── jenkins.CLI.xml
    ├── jenkins.install.InstallUtil.installingPlugins
    ├── jenkins.install.InstallUtil.lastExecVersion
    ├── jenkins.install.UpgradeWizard.state
    ├── jenkins.telemetry.Correlator.xml
    ├── jobs
    ├── logs
    ├── nodeMonitors.xml
    ├── nodes
    ├── plugins
    ├── queue.xml.bak
    ├── secret.key
    ├── secret.key.not-so-secret
    ├── secrets
    ├── updates
    ├── userContent
    ├── users
    ├── war
    └── workflow-libs
~~~

이제 docker-compose.yml 이 작성되었으니 해당 디렉토리에서 다시 컴포즈를 실행해보자. 이후 docker-compose ps 명령으로 확인해보면 master와 slave01이라는 이름이 붙은 컨테이너가 실행됐음을 알 수 있다.

~~~
$ docker-compose up -d
Pulling slave01 (jenkins/ssh-slave:)...
latest: Pulling from jenkins/ssh-slave
f15005b0235f: Pull complete

...

Status: Downloaded newer image for jenkins/ssh-slave:latest
Creating slave01 ... done
Recreating master ... done

$ docker-compose ps
 Name                Command               State                 Ports              
------------------------------------------------------------------------------------
master    /sbin/tini -- /usr/local/b ...   Up      50000/tcp, 0.0.0.0:8080->8080/tcp
slave01   setup-sshd                       Up      22/tcp      
~~~

#### 마지막 설정

두 컨테이너를 실행했다고 해서 마스터 젠킨스가 슬레이브 젠킨스를 인식하지는 못한다. 이에 실제로 젠킨스 페이지에서 설정을 해주어야한다.  
먼저 다시한번 'http://localhost:8080/' 에 접속하고, 왼쪽 메뉴에 'Jenkins 관리' 클릭 후 '노드 관리'에 들어간다. 그리고 왼쪽 메뉴탭의 '신규 노드'를 클릭해주고 다음과 같은 절차를 따른다.

노드명은 'slave01'로 정하고, Premanet Agent에 체크해주자

![스크린샷 2020-09-07 오후 6 05 04](https://user-images.githubusercontent.com/50758600/92371669-db6b6d00-f136-11ea-9efb-f4fb23b335ff.png)

![스크린샷 2020-09-07 오후 6 23 06](https://user-images.githubusercontent.com/50758600/92371955-4321b800-f137-11ea-8497-101a16761485.png)


그러면 다음과 같은 화면이 나오는데, 'Remote root directory'는 본인이 원하는 디렉토리를 아무거나 설정해도 된다. 그리고 '	Launch method'를 'Launch agents via SSH' 로 설정해준다. 여기서 'Host' 값은 slave01로 입력하면, 굳이 slave01의 IP 주소를 찾아 입력할 필요 없이 master 컨테이너에서 slave01이라는 이름으로 컨테이너를 찾아올 수 있다. 'Credentials'은 Add를 눌러 풀다운 메뉴에서 Jenkins를 선택해 설정을 추가한다.

![스크린샷 2020-09-07 오후 6 36 01](https://user-images.githubusercontent.com/50758600/92373241-ffc84900-f138-11ea-9360-5878958a65e7.png)

그러면 'kind'를 SSH Username with private key로 바꾸고, 'Username'은 Jenkins, Private Key는 미스터 젠키스의 /.ssh/id_rsa를 선택한다. 그리고 'Add'를 누른 뒤 'Credentials'에 Jenkins를 추가해주고, 'Host Key Verification Startegy'도 다음과 같이 바꿔주자.

- id_rsa 의 키가 pass phrase가 제거되어있지 않으면 [이 블로그](https://m.blog.naver.com/PostView.nhn?blogId=websearch&logNo=220326799080&proxyReferer=https:%2F%2Fwww.google.com%2F)를 참고하자.

![스크린샷 2020-09-07 오후 6 37 26](https://user-images.githubusercontent.com/50758600/92373386-32724180-f139-11ea-88fd-bfd83f8216f6.png)

그리고 최종적으로 'Save'하면 다음과 같은 화면이 나온다.

![스크린샷 2020-09-07 오후 8 15 46](https://user-images.githubusercontent.com/50758600/92382051-ee863900-f146-11ea-9065-3634c9d8e0a7.png)

- 참고로 java 경로를 못찾는 오류가 생길 경우 [이 글](https://github.com/chlalstjd430/debugging-note/blob/master/01.%20CICD/jenkins%EC%97%90%EC%84%9C%20java%20%EA%B2%BD%EB%A1%9C%EB%A5%BC%20%EB%AA%BB%20%EC%B0%BE%EB%8A%94%20%EC%98%A4%EB%A5%98.md)을 참고하자


이렇게 컴포즈를 이용해 마스터/슬레이브로 구성된 젠킨스를 구축했다. 물론 해당 작업에서는 약간의 수작업도 진행하였다. 그렇담면 다음 글에서 설정만으로 도커 구성 관리를 완성할 수 있도록 애플리케이션과 컨테이너를 만들려면 어떻게 해야 하는지 그 기본적인 내용을 알아보겠다.


<br>
<br>
<br>

**참고**

[도커/쿠버네티스를 활용한 컨테이너 개발 실전 입문](http://www.yes24.com/Product/Goods/70893433)