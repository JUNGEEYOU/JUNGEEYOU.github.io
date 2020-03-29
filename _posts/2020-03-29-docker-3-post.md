---
layout: post
title: "docker 이미지를 만들어보자"
date: 2020-03-29
excerpt: ""
tags: [docker , dockerfile, docker 이미지  ]
comments: true
docker : true 
feature: /assets/img/docker/basic_1/whale.jpeg
image: /assets/img/docker/basic_1/Moby-logo.png
excerpt: "docker 이미지 만들기" 
---



> 이번 시간에는 docker 이미지를 커스터마이징하기 위해 Dockerfile로 이미지를 만들어 봅시다. 더 나아가 만든 도커 이미지를 hub에 올려 봅시다.

# 💡 목차

> 이번 시간에 학습할 부분은 아래와 같습니다.  전체적으로 무엇을 배울지 미리 확인하고 갑시다.

1. **docker 이미지 생성 방법** 
    - 이미지 생성 방법에 대해 정리해 봅시다.
2. **docker commit** 
    - docker commit 명령어를 통해 이미지를 생성해 봅니다.
3. **Dockerfile 만들기** 
    - Dockerfile를 만들어 이미지를 커스터마이징 해봅시다.
4. **docker hub 올리기** 
    - docker hub에 위에서 만든 이미지를 올려 봅시다.

---

# 1. docker 이미지 생성 방법

> 이미지 생성 방법은 다음과 같은 3가지가 존재한다.

1. **기본 이미지(nginx, centos 등) 로 컨테이너 생성**
    - 베이스 이미지를 pull 하여 이미지를 얻을 수 있습니다.
2. **Dockerfile를 만들어 이미지 커스터마이징 하기** 
    - Dockerfile는 컨테이너에 필요한 패키지, 소스코드 등을 기록해둔 파일로 빌드를 하면 도커 이미지가 생성됩니다.
    - 장기적인 시점에서 본다면, 아래 commit 방식보단 Dockerfile 작성이 저 좋습니다. 이유는 <span style="background-color: #e6e6ff; font-clolr: #000000"> 이미지 생성 방법 기록 + 배포 측면</span>에서 더 유리합니다.
3. **commit를 이용해서 이미지 생성하기** 
    - docker commit 명령어를 이용하면 컨테이너에서 설치 및 작업한 내용이 저장되는 방식입니다.

---

# 2. docker commit

> 이미지 생성 방법 중 commit를 이용해서 이미지를 생성해 봅시다.

## 🔹 commit  명령어 설명

> 아래 실습은 아래와 같이 진행하려고 합니다. 이 방법은 우선 기본 이미지를 실행 시킨 후, 컨테이너 안에서 원하는 패키지나 소스코드 등 작업(아래에서는 telnet를 설치) 을 진행합니다. 그 후, <span style="background-color: #e6e6ff; font-clolr: #000000"> commit 명령어로 원하는 작업이 추가된 새로운 이미지를 얻습니다. </span> 

![Untitled/Untitled.png](/assets/img/docker/basic_3/Untitled.png)

### 🔸  commit  명령어

- **참고** : [https://docs.docker.com/engine/reference/commandline/commit/](https://docs.docker.com/engine/reference/commandline/commit/)
{% highlight shell %}
💡 docker commit [OPTIONS] CONTAINER_명 [저장소이름]/이미지이름[:TAG]
{% endhighlight %}
### 🔸 OPTIONS

 | 옵션 |  설명  | 
 |----------|----------|
 |  <span>&nbsp;</span> -a, --author |  <span>&nbsp;</span> 생성자 정보를 입력합니다.   |
 |  <span>&nbsp;</span> -m, --message |   <span>&nbsp;</span> 변경 사항에 대한 메시지를 입력 합니다.    |
 |  <span>&nbsp;</span> -p, --pause |   <span>&nbsp;</span> 이미지를 생성하는 동안 컨테이너를 중단 시킵니다. default 값으로 true  |
 {:.inner-borders}
 
<br/>
## 🔹 실습하기  - telnet 설치된 이미지 만들기

> commit 없이 진행한 것과 commit 명령어로 진행한 작업을 비교하고자 합니다.  아래 두 가지 작업 모두 실습해 봅시다. 참고로 telnet은  특정 포트(Port)가 접속 가능한지 확인 가능한 명령어 입니다.

<br/>
### 🔸 commit 명령어 없이 이미지 확인하기

> 컨테이너에서 telnet를 설치해 보고 commit 없이 컨테이너를 내릴 경우 해당 이미지가 어떻게 되는지 확인해 봅시다.

1. **docker run** 
    - 아래 명령어로 centos 도커 컨테이너를 실행 후, bash로 컨테이너에 들어가보자.
{% highlight shell %}
    $ docker run -it --name commit_test centos bash
{% endhighlight %}
**2. telnet 를 설치하기** 

- 아래 명령어로 cenots 컨테이너에서 telnet를 설치합니다.
{% highlight shell %}
    [root@ade9f1da26d3 /]# yum install -y telnet
    ------- 생략 -------                                                                                                                              
    Complete!
    
    
    [root@ade9f1da26d3 /]# telnet
    telnet>
{% endhighlight %}
**3. 새 터미널에서 diff 명령어로 변경된 파일 확인하기**

- 확인해 보면 telnet이 추가된 것을 확인할 수 있다.
{% highlight shell %}
    (new terminal)
    $ docker diff commit_test
    
    A /usr/bin/telnet
{% endhighlight %}
**4. 해당 컨테이너 내리기** 
{% highlight shell %}
    $ docker rm -f commit_test
{% endhighlight %}
**5. 같은 이미지를 다시 실행 시켜  위에서 설치한  telnet 명령어가 되는지 확인**

- 아래와 같이 저장이 안된 모습을 확인할 수 있다.
{% highlight shell %}
    $ docker run -it --name commit_test centos bash
    [root@82aa81723f45 /]# telnet
    bash: telnet: command not found
{% endhighlight %}
<br/>
### 🔸  commit 명령어 후, 이미지 확인하기

> 위와 같이 컨테이너에서 telnet를 설치해 보고, commit 를 진행하면 어떤 점이 다른지 확인해 봅시다.

 **1. docker run** 

- 아래 명령어로 centos 도커 컨테이너를 실행 후, bash로 컨테이너에 들어가보자.
{% highlight shell %}
    $ docker run -it --name commit_test centos bash
{% endhighlight %}
**2. telnet 를 설치하기** 
{% highlight shell %}
    [root@82aa81723f45 /]# yum install -y telnet
    ------- 생략 -------                                                                                                                               
    Complete!
    
    
    [root@82aa81723f45 /]# telnet
    telnet>
{% endhighlight %}
**3. 새 터미널에서 commit 진행** 
- **-m 옵션:**  변경된 로그를 입력해 줍니다. ("install telnet")
- **컨테이너 명**: commit_test
- **원하는 이미지 명 & tag** : centos_telnet:01
{% highlight bash %}
    (new terminal)
    $ docker commit -m "install telnet" commit_test centos_telnet:01
    sha256:257fc79abba712f2dbb4e35c1816321dd854989bfedbb07ed94e614b4a59fa89
    
    
    jungee-MacBook-Pro:~ jungee$ docker ps
    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
    82aa81723f45        centos              "bash"              4 minutes ago       Up 4 minutes                            commit_test
    jungee-MacBook-Pro:~ jungee$ docker images
    REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
    centos_telnet       01                  257fc79abba7        6 seconds ago       274MB
    centos              latest              470671670cac        2 months ago        237MB
{% endhighlight %}
**4. commit 으로 생성한 이미지 실행하여 telnet 동작 확인** 

- commit 명령어로 telnet이 설치된 이미지가 생성된 것을 확인할 수 있다.
{% highlight shell %}
    $ docker run -it --name commit_test2 centos_telnet:01 bash
    [root@863ac3be5d1c /]# telnet
    telnet>
{% endhighlight %}

<br/>

---

# 3. DockerFile

## 🔹 기본 설명

> 이전에 설명했던 부분으로 1) 베이스 이미지와 2) 도커파일(Dockerfile)로 생성하는 방식이 있다. 지금은 Dockerfile로 이미지를 커스터마이징하여 사용하고자 합니다.  아래 <span style="background-color: #e6e6ff; font-clolr: #000000"> 이미지와 같이 Dockerfile를 build를 하면, 커스터마이징된 이미지가 생성</span> 됩니다.

![Untitled/Untitled%201.png](/assets/img/docker/basic_3/Untitled%201.png)

- **Dockerfile**: 컨테이너에 설치해야 하는 패키지, 추가해야하는 소스코드, 실행해야하는 명령어 등을 기록해 두는 파일
- **Build**: Dockerfile를 읽어 이미지를 생성합니다.

<br/>

## 🔹 실습 1. python-Flask

> app.py, requirements.txt 는 python-flask를 실행하기 위한 파일입니다. 우선 해당 파일을 생성 후, Dockerfile를 만들어 봅시다.

1. **app.py** 
- '/' 경로로 접근 시, hello_world() 라는 함수 실행합니다.
{% highlight python %}
    from flask import Flask
    app = Flask(__name__)
    
    @app.route('/')
    def hello_world():
        return 'hello Flask'
    
    
    if __name__ == '__main__':
        app.run(debug=True, host='0.0.0.0')
{% endhighlight %}
**2. requirements.txt**
- Flask 라는 라이브러리 설치가 필요하여 버전과 함께 설치할 라이브러리를 명시해준 파일입니다.
{% highlight text %}
    Flask==0.10.1
{% endhighlight %}
**3.  Dockerfile**
- 패키지, 소스코드 등을 기록해둔 파일입니다. 위에서 말했듯이 이를 build하면 커스터마이징된 이미지가 생성됩니다.
- RUN과 같은 명령어 의미에 대해서 파악 해봅니다.
{% highlight shell %}
    # FROM: 베이스 이미지를 지정 (여기서는 ubuntu 16.04 버전 사용) 
    FROM ubuntu:16.04
    
    # MAINTAINER: 개발자 정보를 나타냅니다. 
    MAINTAINER "youremail@domain.com"
    
    # RUN : 해당 명령어 실행, 필요한 패키지를 설치 
    RUN apt-get update -y && \
        apt-get install -y python-pip python-dev
    
    # COPY: 현재 경로(.)에 존재하는 파일들을 이미지 /app 경로에 모두 추가 
    COPY . /app
    
    # WORKDIR: 작업 디렉토리 변경. 셸 cd /app 과 같은 기능 
    WORKDIR /app
    
    # RUN: 명령어 실행. 복사된 requirements.txt 파일로 pip로 필요 라이브러리 설치 
    RUN pip install -r requirements.txt
    
    # EXPOSE: 컨테이너 실행 시 노출될 포트
    EXPOSE 5000
    
    # ENTRYPOINT: 컨테이너 시작 시 기본으로 실행되는 명령어 
    ENTRYPOINT [ "python" ]
    
    # CMD: 컨테이너 시작 시 실행되는 명령어로 위 ENTRYPOINT 명령어 뒤 인자로 실행하게 된다. 
    # 결국 python app.py 명령어 실행 
    CMD [ "app.py" ]
{% endhighlight %}

<br/>

### 🔸 Dockerfile 명령어

- **참고**: [https://docs.docker.com/engine/reference/builder/](https://docs.docker.com/engine/reference/builder/)

###  OPTIONS

 | 명령어 |  설명  | 사용법  |
 |----------|----------|----------|
 | <span>&nbsp;</span> FROM|  <span>&nbsp;</span> 베이스 이미지 지정/ 자신이 만든 이미지도 가능  |  <span>&nbsp;</span> FROM < 이미지 이름> |
 | <span>&nbsp;</span> MAINTAINER |  <span>&nbsp;</span> 개발자 정보를 나타냄   | |
 | <span>&nbsp;</span> RUN|  <span>&nbsp;</span> 명령어 실행. -y : 꼭 넣어주기  |   <span>&nbsp;</span> RUN <명령어> 혹은  RUN ["실행가능한 파일", "명령 인자1", "명령 인자2" ...  ] |
 | <span>&nbsp;</span> ADD|  <span>&nbsp;</span> 파일을 이미지에 추가 . COPY 기능(로컬 파일을 이미지에 추가) + 외부 url 및 tar 파일 추가 가능    |  <span>&nbsp;</span> ADD 추가할_파일 이미지에_추가될_경로 |
 | <span>&nbsp;</span> COPY |  <span>&nbsp;</span> 파일을 이미지에 추가 . 로컬 파일 이미지에 추가     |  <span>&nbsp;</span> COPY 추가할_파일 이미지에_추가될_경로 |
 | <span>&nbsp;</span> WORKDIR|  <span>&nbsp;</span> 작업 디렉토리 변경. 배시 셸 cd 명령어와 같은 기능  |  <span>&nbsp;</span> WORKDIR 작업디렉토리 |
 | <span>&nbsp;</span> EXPOSE|  <span>&nbsp;</span> 컨테이너로 실행 시 노출시킬 포트. 단순히 해당 포트를 사용한다는 것을 명시하는 것. 이를 사용해도 run 명령어(도커 실행)에서 -P 옵션도 필요   |  <span>&nbsp;</span> EXPOSE 포트 |
 | <span>&nbsp;</span> CMD |  <span>&nbsp;</span> 컨테이너가 시작될 때마다 실행할 명령어를 설정. Dockerfile에서 한 번만 사용 가능.     |  <span>&nbsp;</span> CMD /run.sh |
 | <span>&nbsp;</span> ENTRYPOINT|  <span>&nbsp;</span> 컨테이너가 시작될 때마다 실행할 명령어를 설정.  Dockerfile에서 한 번만 사용 가능. CMD와 다르게 컨테이너 실행 시, 명령어 입력을 하면 반드시 기본으로 실행하며 그 뒤 인자로 명령어가 실행됩니다.  | <span>&nbsp;</span>ENTRYPOINT 명령어. <br/> ENTRYPOINT ["실행 파일", "매개 변수1", "매개 변수2"] |
 | <span>&nbsp;</span> ENV|  <span>&nbsp;</span> 환경변수 지정    |  <span>&nbsp;</span> ENV  환경변수 값 |
 | <span>&nbsp;</span> VOLUME |  <span>&nbsp;</span> 호스트와 컨테이너 내부의 디렉토리를 설정하여 데이터를 공유.  도커 실행 시 , -v 명령어로 지정해야 볼륨이 생성됨.     |  <span>&nbsp;</span> VOLUME  컨테이너_디렉토리  |
 {:.inner-borders}
 
<br/>

**4. docker build** 
- 만든 Dockerfile를 build하여 이미지를 만들어 봅니다.
{% highlight shell %}
💡  docker build [OPTIONS] 도커파일경로 
{% endhighlight %}

### 🔸 OPTIONS

 | <span>&nbsp;</span> 옵션 |  <span>&nbsp;</span> 설명  | 
 |----------|----------|
 | <span>&nbsp;</span> --tag , -t |  <span>&nbsp;</span> 이미지 이름과 태그를 설정   |
 | <span>&nbsp;</span> --no-cache |  <span>&nbsp;</span> 빌드 시, 캐시 기능을 사용하지 않음     |
 {:.inner-borders}
 
 <br/>
{% highlight shell %}
    # Dockerfile 위치에서 build 명령어 실행 
    $ docker build -t myflask:0.1 .
    
    
    # docker 이미지 생성 결과 확인 
    $ docker images
    REPOSITORY             TAG                 IMAGE ID            CREATED             SIZE
    myflask                0.1                 bfac980a3b49        6 seconds ago       421MB
{% endhighlight %}
**5. docker run** 

- 커스터마이징된 이미지를 실행시켜 컨테이너 결과를 확인해 봅니다.
{% highlight shell %}
    $ docker run --name flask -d -p 5000:5000 myflask:0.1
{% endhighlight %}
6. 결과 확인 

- [localhost:5000](http://localhost:5000) 에 접속해자.

![Untitled/Untitled%202.png](/assets/img/docker/basic_3/login.png)

<br/>

## 🔹 Dockerfile build 자세히 살펴보기

### 🔸 빌드 과정 알아보기

> docker build 명령어 사용 시 빌드되는 과정을 알아봅시다.

Dockerfile에서 줄 수는 레이어 수를 의미한다. 아래와 같이 <span style="background-color: #e6e6ff; font-clolr: #000000">도커파일 명령어가 실행될 때 새로운 컨테이너가 생성되며, 이를 이미지로 커밋하게 됩니다.</span> 따라서 Dockerfile 작성 시, 줄 수을 최소화하는 것이 중요합니다. 

![Untitled/Untitled%203.png](/assets/img/docker/basic_3/Untitled%203.png)

<br/>

### 🔸 캐시 이미지 빌드

> 한 번 이미지를 빌드하면, 다시 같은 빌드를 진행할 경우 이전 빌드에서 사용했던 캐시를 이용하게 됩니다. 아래 예제로 위에서 빌드한 내용 그대로 다시 빌드해 봅시다.

- <span style="background-color: #e6e6ff; font-clolr: #000000"> Using cache 부분을 보면 해당 명령어를 실행하지 않고 이전 캐시 내용을 사용 </span>하는 것을 알 수 있습니다.
{% highlight shell %}
    # 1. Dockerfile를 복사한다. 
    $ cp Dockerfile Dockerfile_2
    
    
    $ ls
    Dockerfile		Dockerfile_2		app.py			requirements.txt
    
    # 2. 다시 같은 내용을 빌드해 봅니다. 
    $ docker build -t  myflask:0.2 -f Dockerfile_2 .
    Sending build context to Docker daemon   5.12kB
    Step 1/9 : FROM ubuntu:16.04
     ---> 77be327e4b63
    Step 2/9 : MAINTAINER "youremail@domain.tld"
     ---> Using cache
     ---> d528f6db2cad
    Step 3/9 : RUN apt-get update -y &&     apt-get install -y python-pip python-dev
     ---> Using cache
     ---> 1073338a84e5
    Step 4/9 : COPY . /app
     ---> f0aec51b1467
    
    ------- 생략 ------- 
{% endhighlight %}
그러나 항상 캐시 기능이 필요한 것은 아닙니다. 예를 들어 git clone 과 같은 명령어를 사용할 때 캐시가 적용되면 소스가 변경될 때 변경되지 않습니다. 따라서 <span style="background-color: #e6e6ff; font-clolr: #000000">--no-cache </span> 옵션을 사용하면 됩니다. 
{% highlight shell %}
    $ docker build --no-cache -t myflask:0.2 . 
{% endhighlight %}

<br/>

### 🔸  멀티 스테이지 이용해서 Dockerfile 빌드하기
멀티 스테이지는 컨테이너 이미지 생성 시 최종 컨테이너 이미지에는 필요 없는 환경은 제거하도록 단계를 나누어 이미지를 생성하는 것이다. 이번 포스트에서는 다루지 않겠습니다.  해당 링크를 참조 ([공식 사이트](https://docs.docker.com/develop/develop-images/multistage-build/)) 해서 실습을 진행해 보면 좋을 것 같습니다.

<br/>

### 🔸  Dockerfile 작성 시, 주의점

- **.dockerignore 파일을 작성하여 불필요한 파일을 이미지에 포함 시키지 말자.** 
    - 참고 : [https://docs.docker.com/engine/reference/builder/#dockerignore-file](https://docs.docker.com/engine/reference/builder/#dockerignore-file)
    - 아래 예제는 Dockerfile은 포함 시키지 않고 빌드 하고자 합니다.
    {% highlight shell %}
            # 1. .dockerignore 파일에 불필요한 이미지를 작성합니다.
            $ vi .dockerignore
            Dockerfile
            
            # 2. 해당 이미지를 빌드 합니다. 
            $ docker build -t  myflask:0.3  .
            
            # 3. 이미지를 실행한 후, 컨테이너에 들어 갑니다. 
            $ docker run --name flask -d -p 5000:5000 myflask:0.3
            3a1fa88e605b34357cf8601e04b5c1f1b8a13010ee036e67b8efe27f4fda07f6
            
            $ docker exec -it flask bash
            
            # 4. Dockerfile 파일만 없는 것을 확인할 수 있다. 
            root@3a1fa88e605b:/app# ls
            Dockerfile_2  app.py  requirements.txt
    {% endhighlight %}


- **RUN 명령어를 하나로 묶을 수 있다면 && 로 묶자.** 
    - 위에서 말했듯이 Dockerfile 한 줄은 한 이미지 레이어라고 하였습니다. 따라서 RUN 명령어는 한 줄로 묶는 것이 가능하다면 묶는 것이 좋습니다.

<br/>

---

# 4. docker hub

> 이번엔 내가 만든 이미지를 docker Hub에 올려보자. <span style="background-color: #e6e6ff; font-clolr: #000000"> docker Hub는 다른 사용자들과 도커 이미지를 공유하는 저장소입니다.</span> 

## 🔹 Docker hub 실습해 보기

1. **docker hub 가입하기** 
    - [https://hub.docker.com/signup](https://hub.docker.com/signup)

**2.  docker 로그인하기** 

- 방법 1. 터미널에서 docker login 명령어로 로그인 하기
{% highlight shell %}
    $ sudo docker login
    WARNING: Error loading config file: /Users/jungee/.docker/config.json: EOF
    Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
    Username: [내 hub ID]
    Password: 
    Login Succeeded
{% endhighlight %}
- 방법 2. UI에서 로그인하기 (mac 환경)
    - 아래 이미지 순서로 빨간 테두리 부분을 클릭하여 로그인해 봅니다.

![Untitled/Untitled%204.png](/assets/img/docker/basic_3/Untitled%204.png)
<center>도커 로그인 진행 </center>

![Untitled/Untitled%205.png](/assets/img/docker/basic_3/Untitled%205.png){: width="50%" height="50%"}
<center>자신의 도커 ID와 비밀번호를 입력</center>

**3.  이전에 build한 이미지를 계정에 맞게  tag 명령어로 새로 생성해 준다.** 

- 이미지는 자신 계정 이름과 일치해야 Hub에 push가 가능합니다.
{% highlight shell %}
💡  docker tag 이미지명:[태그] 원하는_이미지_명:[태그]
{% endhighlight %}

{% highlight shell %}
    $ sudo docker tag centos_telnet:01 junge2/centos_telnet:latest
    Password:
    
    $ docker images
    REPOSITORY             TAG                 IMAGE ID            CREATED 
                SIZE
    junge2/centos_telnet  latest              257fc79abba7        24 hours ago        274MB
{% endhighlight %}
**4. docker hub에 push**
{% highlight shell %}
💡 docker push [허브_아이디]/이미지명:[태그]
{% endhighlight %}
{% highlight shell %}
    $ docker push junge2/centos_telnet:latest
    
    WARNING: Error loading config file: /Users/jungee/.docker/config.json: open /Users/jungee/.docker/config.json: permission denied
    The push refers to repository [docker.io/junge2/centos_telnet]
    648f89c4f2c4: Pushed 
    0683de282177: Mounted from library/centos 
    latest: digest: sha256:856ed620b2179be0d6bdbbb0c38ef41b558049642489e349e715f8526777768a size: 741
{% endhighlight %}
**5. 사이트 docker hub에 들어가 확인해보자.** 

- [https://hub.docker.com/repositories](https://hub.docker.com/repositories)
- 아래 이미지와 같이 위에서 push한 이미지가 있음을 확인할 수 있습니다.

![Untitled/Untitled%206.png](/assets/img/docker/basic_3/Untitled%206.png)

<br/>
**6. 이전 이미지를 지우고, hub에 올린 이미지를 다운받아 보자.**

- **docker rmi** :  이미지를 삭제
- **docker pull [자신의 docker hub ID]/이미지:버전** : 자신이 올린 이미지를 다운 받습니다.
- **docker images:** 이미지 리스트 확인
{% highlight shell %}
    $ sudo docker rmi junge2/centos_telnet
    Untagged: junge2/centos_telnet:latest
    Untagged: junge2/centos_telnet@sha256:856ed620b2179be0d6bdbbb0c38ef41b558049642489e349e715f8526777768a
    
    $ sudo docker pull junge2/centos_telnet:latest
    latest: Pulling from junge2/centos_telnet
    Digest: sha256:856ed620b2179be0d6bdbbb0c38ef41b558049642489e349e715f8526777768a
    Status: Image is up to date for junge2/centos_telnet:latest
    docker.io/junge2/centos_telnet:latest
    
    $ docker images
    REPOSITORY             TAG                 IMAGE ID            CREATED             SIZE
    junge2/centos_telnet   latest              257fc79abba7        24 hours ago        274MB
{% endhighlight %}
---

# 마무리

> 이번 시간에는 도커 이미지 생성 방법에 대해서 알아 보았습니다. 다음 시간에는 컴포즈 사용 방법에 대해서 알아 보고 직접 실습해 봅시다~ 😁