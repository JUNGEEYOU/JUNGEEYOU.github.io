---
layout: post
title: "docker를 사용해보자 (docker 기본 명령어 사용해보기)"
date: 2020-03-15
excerpt: ""
tags: [docker , docker 기본 명령어 ]
comments: true
docker : true 
feature: /assets/img/docker/basic_1/whale.jpeg
image: /assets/img/docker/basic_1/Moby-logo.png
excerpt: "도커 기본 명령어 정리  "
---


# docker를 사용해보자 (docker 기본 명령어 사용해보기)

> 이번 시간에는 이전에 배운 개념을 토대로 docker 를 직접 사용해 봅시다.

# 💡 목차

> 이번 시간에 학습할 부분은 아래와 같다.  전체적으로 무엇을 배울지 미리 확인하고 가자.

1. **도커 다운로드** 
2. **도커 설치 확인 (docker version)**  
    - docker version 명령어에 대한 의미를 살펴 보자.
3.  **docker 이미지 다루기 - 자주 사용 하는 명령어 정리**
    - docker 이미지를 다루는 명령어를 살펴 봅니다.
4. **docker  컨테이너 다루기  - 자주 사용 하는 명령어 정리** 
    - docker 컨테이너를 다루는 명령어를 살펴 봅니다.
5. **docker 실습** 
    - docker 명령어를 직접 사용해 봅시다.
6. **docker 기타 명령어 정리** 
    - 그 외 기다 명령어를 살펴 봅니다.

---

# 1. 도커 다운로드

> 아래 주소를 통해 도커를 다운로드 해보자.

- 맥 : [https://docs.docker.com/docker-for-mac/](https://docs.docker.com/docker-for-mac/)
- 윈도우: [https://docs.docker.com/docker-for-windows/](https://docs.docker.com/docker-for-windows/)
- 리눅스: [https://docs.docker.com/install/linux/docker-ce/centos/](https://docs.docker.com/install/linux/docker-ce/centos/)

## 🔹 docker 설치 for centos7

> centos7 에 docker 를 설치하는 방법입니다.

{% highlight bash %}

    # yum-config-manager 쓸 수 있도록 설치
    $ sudo yum -y install yum-utils
    
    # 도커 repository 추가 
    $  sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
    
    # 최신 도커 설치
    $ sudo yum -y install docker-ce
    
    # docker를 root 계정이 아닌 현재 계정으로도 사용하기 위해 docker 그룹에 현재 계정도 추가한다. 
    $ sudo usermod -aG docker $(whoami)
    
    # reboot 시 도커 자동으로 재 실행 되도록 설정
    $ sudo systemctl enable docker.service
    
    # 도커 서비스 시작 
    $ sudo systemctl start docker.service
    
{% endhighlight %}
---

# 2. 도커 설치 확인 (docker version)

> 아래 명령어는 mac 에서 진행한 결과 입니다.

## 🔹 docker version

 설치 완료 후 도커가 정상적으로 동작하는지 아래 명령어를 입력합니다. 

    **$ docker version**

## 🔹 docker version 결과

> 이전에 설명한 Docker architecture 에서 도커의 구조는 **클라이언트-서버** 가 존재한다고 하였습니다. 여기 결과에  Client/Server가 존재하는 이유도 이와 같습니다. 이 명령어 결과의 의미는 다시 정리해보면 아래와 같습니다.

{% highlight bash %}
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

![docker%20docker/Untitled.png](docker%20docker/Untitled.png)

docker Client-Server 구조 

- **client**:  현재 나의 컴퓨터. **OS/Arch** 를 보면 제 컴퓨터가 mac이라서 **darwin/amd64** 라고 확인할 수 있습니다.
- **server**: 실제 컨테이너를 생성 및 실행하며 이미지를 관리합니다. Client가 명령어를 전달하면 Server가 명령어를 받아 실행합니다.

 

---

# 3. docker 이미지 다루기 - 자주 사용 하는 명령어 정리

- **기본 명령어 참고** : [https://docs.docker.com/engine/reference/commandline/docker/](https://docs.docker.com/engine/reference/commandline/docker/)

## 🔹 docker search

> docker hub에 존재하는 이미지 리스트를 확인할 수 있다.

 docker search [검색할 이미지명]

### 예시

{% highlight bash %}
    **$ docker search ubuntu**
    NAME                                                      DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
    ubuntu                                                    Ubuntu is a Debian-based Linux operating sys…   10605               [OK]                
    dorowu/ubuntu-desktop-lxde-vnc                            Docker image to provide HTML5 VNC interface …   404                                     [OK]
    rastasheep/ubuntu-sshd                                    Dockerized SSH service, built on top of offi…   243                                     [OK]
    consol/ubuntu-xfce-vnc                                    Ubuntu container with "headless" VNC session…   211                                     [OK]
    ubuntu-upstart                                            Upstart is an event-based replacement for th…   106                 [OK]                
    ansible/ubuntu14.04-ansible                               Ubuntu 14.04 LTS with ansible                   98                                      [OK]
    neurodebian                                               NeuroDebian provides neuroscience research s…   67                  [OK]
{% endhighlight %}

## 🔹 docker pull

> 이미지를 다운로드를 한다.

 docker pull  [저장소 이름]/[이미지 이름]:[태그]

[ ](https://www.notion.so/e4f8be1cd6a64b2584d4f7b5d9442516)

### 🔸 예시

- ubuntu 라는 이미지 이름. 태그는 18.04 의 이미지를 다운로드 한다.
- **저장소**: 도커 허브에 존재. [https://registry.hub.docker.com/_/ubuntu?tab=description](https://registry.hub.docker.com/_/ubuntu?tab=description) 에서 확인 가능
{% highlight bash %}
    **$ docker pull ubuntu:18.04**
    18.04: Pulling from library/ubuntu
    423ae2b273f4: Pull complete 
    de83a2304fa1: Pull complete 
    f9a83bce3af0: Pull complete 
    b6b53be908de: Pull complete 
    Digest: sha256:04d48df82c938587820d7b6006f5071dbbffceb7ca01d2814f81857c631d44df
    Status: Downloaded newer image for ubuntu:18.04
    docker.io/library/ubuntu:18.04
{% endhighlight %}

## 🔹 docker images

> 이미지 목록 확인하기

docker images 

### 🔸 예시

- 이전 예제에서 다운받은 이미지 리스트를 확인할 수 있다.

    $ docker images
    REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
    ubuntu              18.04               72300a873c2c        2 weeks ago         64.2MB

## 🔹 docker rmi

> 이미지 제거하기

docker rmi 이미지1 [이미지2, 이미지3 ...]

### 🔸 예시

- 다운 받았던 이미지를 삭제하고, 다시 'docker images' 명령어로 목록을 확인하자
- 제거 방식은 docker rmi  [도커이름:태그] 또는 [image id]
{% highlight bash %}
    **$ docker rmi ubuntu:18.04**
    Untagged: ubuntu:18.04
    Untagged: ubuntu@sha256:04d48df82c938587820d7b6006f5071dbbffceb7ca01d2814f81857c631d44df
    Deleted: sha256:72300a873c2ca11c70d0c8642177ce76ff69ae04d61a5813ef58d40ff66e3e7c
    Deleted: sha256:d3991ad41f89923dac46b632e2b9869067e94fcdffa3ef56cd2d35b26dd9bce7
    Deleted: sha256:2e533c5c9cc8936671e2012d79fc6ec6a3c8ed432aa81164289056c71ed5f539
    Deleted: sha256:282c79e973cf51d330b99d2a90e6d25863388f66b1433ae5163ded929ea7e64b
    Deleted: sha256:cc4590d6a7187ce8879dd8ea931ffaa18bc52a1c1df702c9d538b2f0c927709d
    
    **$ docker images**
    REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
{% endhighlight %}

---

# 4. docker  컨테이너 다루기  - 자주 사용 하는 명령어 정리

## 🔹 docker run

> 컨테이너를 생성하고 실행하기

- 사용할 이미지가 저장되어 있는지 확인하고 없다면 다운로드(pull)를 한 후 컨테이너를 생성(create)하고 시작(start)  한다.

docker run [OPTIONS] IMAGE[:TAG|@DIGEST] [COMMAND] [ARG...]

### 🔸 OPTIONS

[   옵션](https://www.notion.so/3101aeccf8534396ae09bd9551157baa)

### 🔸  예시 1

- ubuntu:18.04  컨테이너를 실행하는 예시로 해당 이미지가 없어서 다운로드(pull)한 뒤, 컨테이너를 실행한다.

    **$ docker run ubuntu:18.04**
    Unable to find image 'ubuntu:18.04' locally
    18.04: Pulling from library/ubuntu
    423ae2b273f4: Pull complete 
    de83a2304fa1: Pull complete 
    f9a83bce3af0: Pull complete 
    b6b53be908de: Pull complete 
    Digest: sha256:04d48df82c938587820d7b6006f5071dbbffceb7ca01d2814f81857c631d44df
    Status: Downloaded newer image for ubuntu:18.04

## 🔸  예시 2

- **- - name**: test라는 컨테이너 이름 설정
- **-it:** 컨테이너 내부로 들어가 bash 입력이 가능
- **- - rm:** 프로세스 종료되면 자동으로 도커 컨테이너 종료
{% highlight bash %}
    **$ docker run --name test -it --rm ubuntu:18.04
    
    # 아래는 docker bash 들어간 것** 
    **root@de78d86be9c0:/# ls**
    bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

## 🔹 docker ps

> 컨테이너 목록 확인. 옵션 없이 사용하는 경우 실행중인 컨테이너만 출력.

docker ps [OPTIONS]

### 🔸 OPTIONS

[ OPTIONS](https://www.notion.so/c7762f0699c0475dbf130c3c6e4130f7)

### 🔸 **예시**

- STATUS 를 통해 컨테이너의 상태를 확인 가능.
- **Exited**: 정지된 상태 , **UP**: 실행 중인 상태
{% highlight bash %}
    $ docker ps -a
    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
    cd532bf89bbb        ubuntu:18.04        "/bin/bash"         4 minutes ago       Exited (0) 4 minutes ago                       test
{% endhighlight %}
## 🔹 docker rm

> 컨테이너를 제거

 docker rm [OPTIONS] 컨테이너1 [컨테이너2...]

### 🔸 OPTIONS

[ OPTION](https://www.notion.so/7f12ca5b8d5b4639821f3ca47849d777)

### 🔸 예시

    $ docker rm -f test 

## 🔹 docker start

> stop된 컨테이너를 실행하기

docker start [OPTIONS] 컨테이너1 [컨테이너2...]

## 🔹 docker stop

> 실행 중인 컨테이너를 stop 하기

docker stop [OPTIONS] 컨테이너1 [컨테이너2...]

## 🔹 docker logs

> 도커 컨테이너의 로그 확인하기

 docker logs [OPTIONS] CONTAINER

### 🔸 OPTIONS

[OPTIONS](https://www.notion.so/260f97e7794842b6b0818a09f35794c1)

### 🔸 예시

    $ docker logs -f <컨테이너 명> 

## 🔹 docker exec

> 실행 중인 컨테이너에 들어갈 때 사용

 docker exec [OPTIONS] CONTAINER COMMAND [ARG...]

### 🔸 예시

    $ docker exec -it <컨테이너 명> /bin/bash 

---

# 5. docker 실습

## 🔹 컨테이너 외부 노출하기 (run -p 사용)  - nginx

> nginx 를 띄워서 확인해보자

- **-p 옵션 :** -p [호스트 포트]:[컨테이너 포트]
{% highlight bash %}
    **$ docker run --name nginx-server -d -p 8080:80 nginx**
    
    Unable to find image 'nginx:latest' locally
    latest: Pulling from library/nginx
    68ced04f60ab: Pull complete 
    28252775b295: Pull complete 
    a616aa3b0bf2: Pull complete 
    Digest: sha256:2539d4344dd18e1df02be842ffc435f8e1f699cfc55516e2cf2cb16b7a9aea0b
    Status: Downloaded newer image for nginx:latest
    87781528d23655be1c12feb6a1f54a63f8e41696a370512e41462cd210288114
{% endhighlight %}
- **localhost:8080 접속하기**

![docker%20docker/Untitled%201.png](docker%20docker/Untitled%201.png)

## 🔹 워드프레스 + mysql

### 🔸  mysql 컨테이너 생성

- **환경 설정 확인  :** [https://registry.hub.docker.com/_/mysql](https://registry.hub.docker.com/_/mysql)
{% highlight bash %}
    $ docker run -d \
     --name wordpressdb \
     -e MYSQL_ROOT_PASSWORD=password \
     -e MYSQL_DATABASE=wordpress \
     mysql:5.7
{% endhighlight %}
### 🔸  워드프레스 컨테이너 생성

- **환경 설정 확인:** [https://registry.hub.docker.com/_/wordpress](https://registry.hub.docker.com/_/wordpress)
- **-p 80:** 호스트의 포트 하나와 컨테이너 80 포트 연결
- **—link:** 다른 컨테이너 ip 대신 별명으로 접근하도록 설정. [컨테이너 명]:[호스트 별칭]
{% highlight bash %}
    $ docker run -d \
     -e WORDPRESS_DB_PASSWORD=password \
     --name wordpress \
     --link wordpressdb:mysql \
     -p 80 \
    wordpress
{% endhighlight %}

### 🔸  사이트 접속

- wordpress 호스트포트는 지정을 안해줘 자동으로 설정되었다. docker ps 명령어로 포트를 확인하자!
{% highlight bash %}
    $ docker ps
    CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                   NAMES
    699c2553b0c6        wordpress           "docker-entrypoint.s…"   11 minutes ago      Up 10 minutes       0.0.0.0:32773->80/tcp   wordpress
    e74707db71cc        mysql:5.7           "docker-entrypoint.s…"   11 minutes ago      Up 11 minutes       3306/tcp, 33060/tcp     wordpressdb
{% endhighlight %}
- **localhost:[해당포트] 에 접속**

![docker%20docker/Untitled%202.png](docker%20docker/Untitled%202.png)

워드프레스에 접속 결과 

### 🔸  컨테이너 로그 보기

> 접속이나 페이지 이동 시, 새로운 로그를 확인 할 수 있습니다.

    $ docker logs -f wordpress

## 🔹docker 볼륨 (run -v 사용)

> 컨테이너는 삭제와 동시에 데이터도 같이 삭제 됩니다.  mysql 같은 db는 볼륨 생성을 해서 데이터 보존이 필요합니다. 이번에는 위 예제를 약간 변경하여 볼륨을 이용해 봅시다.

### 🔸   mysql 컨테이너 생성

- **환경 설정 확인  :** [https://registry.hub.docker.com/_/mysql](https://registry.hub.docker.com/_/mysql)
- -v : 호스트와 컨테이너의 디렉토리를 연결. [호스트의 공유 디렉터리]:[컨테이너의 공유 디렉터리]
    - **호스트의 공유 디렉터리**: 내 컴퓨터에 원하는 디렉터리로 변경해도 좋습니다.  - 아래 예제를 하기 위해 해당 디렉터리를 생성 후, 진행해 주세요.
    - **컨테이너의 공유 디렉터리**: mysql 데이터 저장하는 기본 디렉터리가 /var/lib/mysql 입니다.
{% highlight bash %}
    $ docker run -d \
     --name wordpressdb \
     -e MYSQL_ROOT_PASSWORD=password \
     -e MYSQL_DATABASE=wordpress \
     -v /home/wordpress_db:/var/lib/mysql \
     mysql:5.7
{% endhighlight %}
### 🔸   워드프레스 컨테이너 생성 (변경 없음)

- **환경 설정 확인:** [https://registry.hub.docker.com/_/wordpress](https://registry.hub.docker.com/_/wordpress)
{% highlight bash %}
    $ docker run -d \
     -e WORDPRESS_DB_PASSWORD=password \
     --name wordpress \
     --link wordpressdb:mysql \
     -p 80 \
    wordpress
{% endhighlight %}
### 🔸   볼륨 공유 확인하기

- 내 컴퓨터 **호스트의 공유 디렉터리** 로 이동 후, 확인

    $ ls
    auto.cnf		client-cert.pem		ib_logfile0		ibtmp1			private_key.pem		server-key.pem
    ca-key.pem		client-key.pem		ib_logfile1		mysql			public_key.pem		sys
    ca.pem			ib_buffer_pool		ibdata1			performance_schema	server-cert.pem		wordpress

- **컨테이너 공유 디렉터리에 파일 존재 확인**
    - 호스트의 공유 디렉터리와 동일함을 알 수 있다.

    **1.  컨테이너에 접속** 
    $ docker exec -it wordpressdb bash
    
    **2. mysql 데이터 저장 위치로 이동** 
    # cd /var/lib/mysql
    
    **3. 파일 확인** 
    root@d27bb99cfa91:/var/lib/mysql# ls
    auto.cnf    ca.pem	     client-key.pem  ib_logfile0  ibdata1  mysql	       private_key.pem	server-cert.pem  sys
    ca-key.pem  client-cert.pem  ib_buffer_pool  ib_logfile1  ibtmp1   performance_schema  public_key.pem	server-key.pem	 wordpress

---

# 6. docker 기타 명령어 정리

> 위에서 배운 명령어 외 기타 명령어 입니다.

## 🔹 docker history

> 이미지의 히스토리를 확인할 수 있습니다.

docker history [OPTIONS] [이미지 명:tag]

### 🔸 OPTIONS

[OPTIONS](https://www.notion.so/8638a3bb07d642ed8fe1d475523b13aa)

### 🔸  예시

> 베이스 이미지의 경우 아래와 같은 history 를 확인할 수 있다.
{% highlight bash %}
    $ docker history ubuntu:16.04
    IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
    77be327e4b63        4 weeks ago         /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B                  
    <missing>           4 weeks ago         /bin/sh -c mkdir -p /run/systemd && echo 'do…   7B                  
    <missing>           4 weeks ago         /bin/sh -c set -xe   && echo '#!/bin/sh' > /…   745B                
    <missing>           4 weeks ago         /bin/sh -c rm -rf /var/lib/apt/lists/*          0B                  
    <missing>           4 weeks ago         /bin/sh -c #(nop) ADD file:1f70668251e2e58ce…   124MB
{% endhighlight %}
## 🔹 docker cp

> 컨테이너 안에 있는 파일을 꺼낼 수 있습니다.

docker cp [컨테이너 이름]:[가져올 파일의 경로] [호스트 경로]

### 🔸  예시

> 위에서 진행한 컨테이너의 hosts 파일을 가져와 봅시다.
{% highlight bash %}
    $ docker cp wordpressdb:/etc/hosts ./
    
    $ ls
    hosts
{% endhighlight %}
## 🔹 docker inspect

> 이미지 또는  컨테이너의 세부 정보를 출력합니다.

- 참고 : [https://docs.docker.com/engine/reference/commandline/inspect/#examples](https://docs.docker.com/engine/reference/commandline/inspect/#examples)

docker inspect [이미지 또는 컨테이너 이름]

### 🔸  예시
{% highlight bash %}
    **$ docker inspect wordpress**
    [
        {
            "Id": "8f75f55337e0f9919f02446e25cfe1afd9c97eacce13483cbc62d020465c30ef",
            "Created": "2020-03-22T01:51:36.2863858Z",
            "Path": "docker-entrypoint.sh",
            "Args": [
                "apache2-foreground"
            ],
            "State": {
                "Status": "running",
                "Running": true,
                "Paused": false,
                "Restarting": false,
                "OOMKilled": false,
                "Dead": false,
                "Pid": 16681,
                "ExitCode": 0,
                "Error": "",
                "StartedAt": "2020-03-22T01:51:36.8741324Z",
                "FinishedAt": "0001-01-01T00:00:00Z"
            },
    ...
{% endhighlight %}
---

# 마무리

> 이번 시간에는 도커를 직접 사용해 봤습니다. 다음 시간에는 아래의 내용을 학습할 예정입니다. 😀

- docker hub에 올려보기 Dockerfile 만들기

