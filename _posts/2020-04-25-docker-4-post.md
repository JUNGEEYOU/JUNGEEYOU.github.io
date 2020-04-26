---
layout: post
title: "도커 컴포즈 사용하기"
date: 2020-04-25
excerpt: ""
tags: [docker , docker-compose , docker compose  ]
comments: true
docker : true 
feature: /assets/img/docker/basic_1/whale.jpeg
image: /assets/img/docker/basic_1/Moby-logo.png
excerpt: "도커 컴포즈 사용하기" 
---




# 💡 목차

> 이번 시간에 학습할 부분은 아래와 같다.  전체적으로 무엇을 배울지 미리 확인하고 가자.

1. **도커 컴포즈 사용하는 이유** 
    - 도커 컴프즈를 사용하는 이유를 알고 가자
2. **도커 컴포즈 설치하기** 
    - 도커 컴포즈를 사용하기 위해 설치를 먼저 해보자.
3. **도커 컴포즈 사용하기** 
    - 도커 컴프즈를 사용하고 파일 작성 방법에 대해 알아보자.

---

# 1. 도커 컴포즈 사용하는 이유

 여러 개의 컨테이너를 실행해야 할 때나 도커 실행 시 설정이 많아지면 사용할 때 복잡해집니다. 예를 들어 한 워드프레스 애플리케이션을 구축하기 위해  mysql + wordpress 를 생성해야 한다면, 아래와 같이 하나씩 컨테이너를 생성해야 합니다. 

 해당 예제는 docker를 사용해보자 (docker 기본 명령어 사용해보기) 실습에서 다룬 부분입니다. 

{% highlight bash %}
# 1. mysql 컨테이너 실행 
$ docker run -d \
 --name wordpressdb \
 -e MYSQL_ROOT_PASSWORD=password \
 -e MYSQL_DATABASE=wordpress \
 -v /my_db:/var/lib/mysql \
 mysql:5.7

# 2. wordpress 컨테이너 실행 
$ docker run -d \
 -e WORDPRESS_DB_PASSWORD=password \
 --name wordpress \
 --link wordpressdb:mysql \
 -p 80 \
wordpress
{% endhighlight %}

 이와 같이 여러 개의 컨테이너를 하나의 서비스인 [도커 컴포즈](https://docs.docker.com/compose/)를 사용해서 <span style="background-color: #e6e6ff; font-clolr: #000000">한 파일로 정의한다면 번거롭지 않고 편리</span>할 것 입니다. 그뿐만 아니라 <span style="background-color: #e6e6ff; font-clolr: #000000">실행에 필요한 옵션을 파일에 작성</span> 가능하며, 컨테이너 간 <span style="background-color: #e6e6ff; font-clolr: #000000">실행 순서나 의존성도 관리</span>할 수 있습니다.

---

# 2. 도커 컴포즈 설치하기

 맥, 윈도우 환경은 도커 설치 시 자동 설치되었을 것이다. 따라서 [리눅스 설치 방법](https://docs.docker.com/compose/install/)만 알아보자. 

{% highlight bash %}
# 1. docker compose 다운 
$ sudo curl -L "https://github.com/docker/compose/releases/download/1.25.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

# 2. 실행 가능 권한 적용 
$ sudo chmod +x /usr/local/bin/docker-compose

# 3. 설치되었는지 테스트 
$ docker-compose --version
{% endhighlight %}

---

# 3. 도커 컴포즈 사용하기

 도커 컴포즈는 도커 실행 시 <span style="background-color: #e6e6ff; font-clolr: #000000">복잡한 설정을 쉽게 관리하기 위해 YAML 파일에 정의</span>하는 툴입니다.  위에서 복잡하게 설정해서 실행한 애플리케이션을 docker-compose.yml 파일에 정의해서 간편하게 실행해 봅시다. [도커 문서에 있는 예제](https://docs.docker.com/compose/wordpress/)를 살펴봅시다. 

## docker-compose.yml 파일 작성하기

[wordpress 허브](https://hub.docker.com/_/wordpress/?tab=description)에 있는 설명을 보며 작성해 봅시다. 우선 위에서 작성한 도커 실행을 docker-compose.yml 파일을 작성해서 간편하게 실행시켜 보자. 

{% highlight bash %}
# 1. 원하는 경로를 만들어 주며 해당 폴더로 이동하자. 
$ mkdir my_wordpress
$ cd my_wordpress

# 2. docker-compose.yml 파일을 작성한다. 아래 yaml파일을 복사해서 넣어보자. 
my_wordpress $ vi docker-compose.yml
{% endhighlight %}

{% highlight yaml %}
version: '3'
services:
  wordpress:
    depends_on:
       - db
    image: wordpress
    restart: always
    ports:
      - 80:80
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: exampleuser
      WORDPRESS_DB_PASSWORD: examplepass
      WORDPRESS_DB_NAME: exampledb
    volumes:
      - wordpress:/var/www/html
  db:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_DATABASE: exampledb
      MYSQL_USER: exampleuser
      MYSQL_PASSWORD: examplepass
      MYSQL_RANDOM_ROOT_PASSWORD: '1'
    volumes:
      - db:/var/lib/mysql
volumes:
  wordpress:
  db:
{% endhighlight %}

## docker compose  실행하기

> 해당 경로에 존재하는 docker-compose.yml 파일을 실행해 준다. -d 옵션은 백그라운드 모드에서 실행됩니다.

 $ docker-compose up -d  

{% highlight bash %}
my_wordpress $ docker-compose up -d  
{% endhighlight %}

[localhost](http://localhost):80 에 접속해보면 아래와 같이 워드프레스 화면이 나타납니다. 

![Untitled/Untitled.png](/assets/img/docker/basic_4//Untitled.png)

## docker-compose.yml 파일 의미 살펴보기

그럼 docker-compose.yml  파일의 의미를 살펴봅시다. 그리고 YAML 파일을 작성하는 방법을 알아봅니다. 도커 컴포즈를 사용하기 위해 <span style="background-color: #e6e6ff; font-clolr: #000000">기존에 사용하던 run 명령어를 YAML 파일로 변환</span>해야 합니다. 

YAML 파일은 <span style="background-color: #e6e6ff; font-clolr: #000000">1. 버전 정의 2. 서비스 정의 3. 볼륨 정의 4 네트워크 정의</span> 로 구성됩니다. 이 중 서비스 정의를 가장 많이 사용하며, 볼륨 정의와 네트워크 정의는 선택적으로 사용됩니다. 해당 파일은 2개의 공백으로 들여 쓰기를 하여 상위 항목과 구분합니다. 

### version

YAML 파일 포맷 버전을 나타냅니다. 도커 컴포즈 버전은 도커 엔진 버전에 의존성이 있으므로 가능하면 최신 버전을 사용하는 것이 좋습니다. 도커 엔진과 도커 컴포즈의 호환성에 대한 내용은 [도커 문서](https://docs.docker.com/compose/compose-file/)를 참고해 주세요. 

아래와 같이 3만 적는 경우 3버전 최신 버전을 사용한다는 의미입니다. 

{% highlight bash %}
version: '3' 
{% endhighlight %}

### service

생성될 컨터이너를 묶어놓은 단위입니다. 여기서는 wordpress와 db 컨테이너를 생성하였습니다. 

- wordpress, db는 생성될 서비스의 이름입니다. 이 항목 아래에는 컨테이너가 생성될 때 필요한 옵션을 지정할 수 있습니다.

{% highlight yaml %}
services:
  wordpress:
    depends_on:
       - db
    image: wordpress
    ------------ 생략 --------------------
  db:
{% endhighlight %}

### image

서비스의 컨테이너를 생성할 때 쓰일 이미지의 이름을 설정합니다. docker run과 같이 이미지가 존재하지 않는  경우 저장소에서 자동으로 내려받습니다. 

{% highlight yaml %}
services:
  wordpress:
    image: wordpress
{% endhighlight %}

### environment

 docker run 명령어에서 -e 옵션과 동일합니다. 서비스의 컨테이너 내부에서 사용할 환경 변수를 지정합니다. 

{% highlight yaml %}
 environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: exampleuser
      WORDPRESS_DB_PASSWORD: examplepass
      WORDPRESS_DB_NAME: exampledb
{% endhighlight %}

### port

docker run 명령어의 -p 옵션과 같으며 서비스의 컨테이너를 개방할 포트를 설정합니다. 

{% highlight yaml %}
ports:
      - 80:80
{% endhighlight %}

### volumes

 run 명령어에서 -v 옵션과 동일합니다. 

{% highlight yaml %}
volumes:
      - wordpress:/var/www/html
{% endhighlight %}

### command

컨테이너가 실행될 때 수행할 명령어를 설정합니다. docker run 명령어의 마지막 붙는 커맨드와 같습니다. 

### depends_on

특정 컨테이너에 대한 의존 관계를 나타내며, 이 항목에 명시된 컨테이너가 먼저 생성되고 실행됩니다. 

해당 예제에서는 wordpress는 mysql db에 의존적이기 때문에 아래와 같이 설정해줍니다. 

{% highlight yaml %}
wordpress:
     depends_on:
       - db
{% endhighlight %}

### build

도커파일에서 이미지를 빌드해 서비스의 컨테이너를 생성하도록 설정합니다. 

build 항목에 정의된 도커파일에서 이미지를 빌드하여 서비스의 컨테이너를 생성하도록 합니다. image는 빌드되면 생성되는 이미지 이름입니다. 

{% highlight yaml %}
services:
  web:
    build: ./composetest 
    image: my_web:latest
{% endhighlight %}

## docker-compose 명령어

### docker-compose up

> docker-compose 로 docker-compose.yml 파일에 정의된 컨테이너를 생성합니다. -d옵션으로  백그라운 모드로 실행 가능합니다.

{% highlight bash %}
$ docker-compose up -d 

Creating network "wordpress_default" with the default driver
Creating wordpress_db_1 ... done
Creating wordpress_wordpress_1 ... done
{% endhighlight %}

### docker-compose down

> docker-compose 로 생성된 컨테이너 정지 후 삭제합니다.

{% highlight bash %}
$ docker-compose down 

Stopping wordpress_wordpress_1 ... done
Stopping wordpress_db_1        ... done
Removing wordpress_wordpress_1 ... done
Removing wordpress_db_1        ... done
Removing network wordpress_default
{% endhighlight %}

### docker-compose ps

> docker-compose 로 생성된 컨테이너 목록을 확인할 수 있습니다.

{% highlight bash %}
$ docker-compose ps

        Name                       Command               State          Ports       
------------------------------------------------------------------------------------
wordpress_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp, 33060/tcp
wordpress_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
{% endhighlight %}

---

# 마무리

> 이번 시간에는 도커 컴포즈를 직접 사용해 봤습니다. 다음 시간에는 아래의 내용을 학습할 예정입니다. 😀

- 도커 이미지 자동 배포하기