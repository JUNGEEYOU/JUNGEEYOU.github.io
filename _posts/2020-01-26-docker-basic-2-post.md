---
layout: post
title: "도커 실습하기 ( docker 다운로드, 명령어 정리)"
date: 2020-01-26
excerpt: ""
tags: [docker , docker 다운로드 , docker 명령어 정리 ]
comments: true
---

# 👀 목차
1. 도커 다운로드 
2. 도커 설치 확인 (docker version) 
3. 컨테이너 실행하기 (docker run ~) 
4.  docker  명령어 

---

# 1. 도커 다운로드

- 맥 : [맥 다운로드 매뉴얼](https://docs.docker.com/docker-for-mac/)
- 윈도우: [윈도우 다운로드 매뉴얼](https://docs.docker.com/docker-for-windows/)
- 리눅스: [리눅 다운로드 매뉴얼](https://docs.docker.com/install/linux/docker-ce/centos/)

---

# 2. 도커 설치 확인 (docker version)
{% highlight shell %}
$ docker version
{% endhighlight %}

### 명령어 결과

> server, client 가 존재하는 이유는 아래와 같은 역할을 각각 한다.

- **client**:  현재 나의 컴퓨터
- **server**: host 에서 실제로 실행하고 결과를 client에게 전달

![2%20docker/Untitled.png](/assets/img/docker/basic-2/Untitled.png)
T 아카데미 강의 자료 사진 첨부 
{% highlight shell %}
    $ docker version
    Client: Docker Engine - Community
     Version:           19.03.5
     API version:       1.40
     Go version:        go1.12.12
     Git commit:        633a0ea
     Built:             Wed Nov 13 07:22:34 2019
     OS/Arch:           darwin/amd64
     Experimental:      false
    
    Server: Docker Engine - Community
     Engine:
      Version:          19.03.5
      API version:      1.40 (minimum version 1.12)
      Go version:       go1.12.12
      Git commit:       633a0ea
      Built:            Wed Nov 13 07:29:19 2019
      OS/Arch:          linux/amd64
      Experimental:     true
{% endhighlight %}
---

# 3. 컨테이너 실행하기 (docker run ~)

## 3-1. docker run 의미

> 사용할 이미지가 저장되어 있는지 확인하고 없다면 `다운로드(pull)`를 한 후 `컨테이너를 생성(create)`하고 `시작(start)`  한다.

- 추가 run 명령어 설명
    - [docker run 명령어](https://docs.docker.com/engine/reference/run/)
- **명령어**
{% highlight shell %}
    $ docker run [OPTIONS] IMAGE[:TAG|@DIGEST] [COMMAND] [ARG...]
{% endhighlight %}
- run OPTIONS 

|  옵션  |  설명 | 
|----------|----------|
| -d | detached mode 흔히 말하는 백그라운드 모드  |
| -p | 호스트와 컨테이너의 포트를 연결 (포워딩)  |
| -v | 호스트와 컨테이너의 디렉토리를 연결 (마운트) |
| -e | 컨테이너 내에서 사용할 환경변수 설정  |
| —name | 컨테이너 이름 설정  |
| -it | -i와 -t를 동시에 사용한 것으로 터미널 입력을 위한 옵션  |
| —rm | 프로세스 종료시 컨테이너 자동 제거  |
| —link | 컨테이너 연결 [컨테이너 명:별칭]  |
{:.inner-borders}


## 3-2. docker 사용 예시

### - ubuntu:18.04 컨테이너 실행

> ubuntu:18.04 컨테이너를 실행해 봅시다.

- 명령어
{% highlight shell %}
    $ docker run ubuntu:18.04
{% endhighlight %}
- 실행 결과
{% highlight shell %}
    Unable to find image 'ubuntu:18.04' locally
    18.04: Pulling from library/ubuntu
    5c939e3a4d10: Pull complete 
    c63719cdbe7a: Pull complete 
    19a861ea6baf: Pull complete 
    651c9d2d6c4f: Pull complete 
    Digest: sha256:8d31dad0c58f552e890d68bbfb735588b6b820a46e459672d96e585871acc110
    Status: Downloaded newer image for ubuntu:18.04
{% endhighlight %}
### - bin/bash으로  실행하기

> 컨테이너 내부로 들어가기 위해  `/bin/bash` 를 입력하여 bash쉘을 실행하고 `-it` 를 넣어 키보드를 입력할 수 있게 한다.
{% highlight shell %}
    $ docker run --rm -it ubuntu:18.04 /bin/bash
    root@068eeb84a16b:/# ls
    bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
    boot  etc  lib   media  opt  root  sbin  sys  usr
{% endhighlight %}
### - redis 컨테이너 실행 하기

> 컨테이너 이름은 redis로 하고 `-d` 옵션으로 백그라운드 실행, `-p` 옵션으로 호스트 1234 포트와 컨테이너 6379 포트 연결하여 내 pc 1234포트로 접속한다.
{% highlight shell %}
    $ docker run --name=redis -d -p 1234:6379 redis
    Unable to find image 'redis:latest' locally
    latest: Pulling from library/redis
    8ec398bc0356: Pull complete 
    da01136793fa: Pull complete 
    cf1486a2c0b8: Pull complete 
    a44f7da98d9e: Pull complete 
    c677fde73875: Pull complete 
    727f8da63ac2: Pull complete
{% endhighlight %}
### - mysql 컨테이너 실행하기

> 아래의 `-e` 옵션 관련 환경 변수 옵션은 `"docker hub (이미지)"로 구글 검색`하면 도커 허브에 관련 설정이 정의되어 있으니 참고 필요하다.

- docker hub 설명: [mysql 환경설정](https://hub.docker.com/_/mysql)
{% highlight shell %}
    $ docker run -d -p 3306:3306 \
     -e MYSQL_ALLOW_EMPTY_PASSWORD=true \
     --name mysql \
     mysql:5.7
{% endhighlight %}
### -wordprocess 컨테이너 실행하기
{% highlight shell %}
    docker run -d -p 8080:80 \
    -e WORDPRESS_DB_HOST=docker.for.mac.localhost \ -e WORDPRESS_DB_NAME=wp \
    -e WORDPRESS_DB_USER=wp \
    -e WORDPRESS_DB_PASSWORD=wp \
    wordpress
{% endhighlight %}
---

# 4. docker  명령어

> 자주 사용하는 명령어를 확인해 보자. 다른 명령어들은 아래 참고 바랍니다.

- 기본 명령어 참고 : [docker 기본 명령어 정리](https://docs.docker.com/engine/reference/commandline/docker/)

## 4-1. docker pull

> 이미지를 다운로드를 한다.
{% highlight shell %}
    $ docker pull [OPTIONS] NAME[:TAG|@DIGEST]
{% endhighlight %}
## 4-2. docker ps

> 컨테이너 목록 확인 / 옵션 없이 사용하는 경우, 실행중인 컨테이너만 출력
{% highlight shell %}
    $  docker ps [OPTIONS]
{% endhighlight %}
- OPTIONS

|  Name, shorthand  |  Description | 
|----------|----------|
| --all , -a | 모든 컨테이너를 보여준다. (컨테이너가 실행중이지 않는 것이라도, stop된 컨테이너도 보여줌 )   |
{:.inner-borders}

## 4-3. docker rm

> 컨테이너를 제거한다.
{% highlight shell %}
    $ docker rm [OPTIONS] CONTAINER [CONTAINER...]
{% endhighlight %}
- OPTIONS

|  Name, shorthand  |  Description | 
|----------|----------|
| --force , -f | 실행중인 컨테이너를 강제로 제거한다.  (uses SIGKILL)  |
{:.inner-borders}


- 자주 사용하는 명령어
{% highlight shell %}
    $ docker rm -f <컨테이너 명> 
{% endhighlight %}
## 4-4. docker images

> 이미지 목록 확인하기
{% highlight shell %}
    $  docker images [OPTIONS] [REPOSITORY[:TAG]]
{% endhighlight %}
## 4-5. docker rmi

> 이미지 제거하기
{% highlight shell %}
    $ docker rmi [OPTIONS] IMAGE [IMAGE...]
{% endhighlight %}
- 자주 사용하는 명령어
{% highlight shell %}
    $ docker rmi <제거할 이미지 1> <제거할 이미지2> ... 
{% endhighlight %}
## 4-6. docker start

> stop된 컨테이너를 실행시켜 준다.
{% highlight shell %}
    $ docker start [OPTIONS] CONTAINER [CONTAINER...]
{% endhighlight %}
## 4-7.docker stop

> 실행 중인 컨테이너를 stop한다.
{% highlight shell %}
    $ docker stop [OPTIONS] CONTAINER [CONTAINER...]
{% endhighlight %}
## 4-8.docker logs

> 도커 컨테이너의 로그 확인하기
{% highlight shell %}
    $ docker logs [OPTIONS] CONTAINER
{% endhighlight %}
- OPTIONS

|  Name, shorthand  |  Description | 
|----------|----------|
| --follow , -f | 실시간 로그 생성   |
| --tail <number>  | 마지막 number 숫자 줄 만큼만 출력   |
{:.inner-borders}


- 자주 사용하는 명령어
{% highlight shell %}
    $ docker logs -f <컨테이너 명> 
{% endhighlight %}
## 4-9. docker exec

> 실행 중인 컨테이너에 들어갈 때 사용
{% highlight shell %}
    $ docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
{% endhighlight %}
- 자주 사용하는 명령어
{% highlight shell %}
    $ docker exec -it <컨테이너 명> /bin/bash
{% endhighlight %}