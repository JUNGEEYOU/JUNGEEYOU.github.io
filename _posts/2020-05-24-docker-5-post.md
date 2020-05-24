---
layout: post
title: "도커 자동 배포하기"
date: 2020-05-24
excerpt: ""
tags: [docker ,jenkins , docker 자동화   ]
comments: true
docker : true 
feature: /assets/img/docker/basic_1/whale.jpeg
image: /assets/img/docker/basic_1/Moby-logo.png
excerpt: "도커 자동 배포하기 - jenkins " 
---


# 💡 목차

> 이번 시간에는 도커 이미지 자동 배포를 진행하려고 합니다. 아래 목차를 확인하여  전체적으로 무엇을 배울지 확인하고 갑시다.

1. **jenkins**
    - jenkins 에 대한 간단히 알아보고 갑니다.
2. **jenkins 도커 실행하기 전 준비**
    - jenkins 도커를 실행하기 전에 먼저 원하는 도커 이미지를 hub에 올려 봅니다.
3. **jenkins 도커 실행**
    - jenkins를 도커로 실행합니다.
4. **jenkins 작업 생성하기**
    - jenkins 파이프 라인을 생성해서 자동 배포가 되도록 설정합니다.
5. **기타 작업 설정** 
    - jenkins 사용 시 편리한 기타 작업들을 설정해 줍니다.

---

# 1. jenkins

 코드 배포를 자동화하기 위해서 자동화 도구 중 하나인 [jenkins](https://www.jenkins.io/doc/)를 도커로 띄우려고 합니다. 그전에 jenkins에 대해 간단히 알아보고 갑시다.  jenkins를 설명하려는 포스트가 아니라서 간단하게만 설명하고 넘어가겠습니다. 

![Untitled%209938b766b4e1422e83c19fa97da8d02e/Untitled.png](/assets/img/docker/basic_5/Untitled.png)

## 🔹  CI/CD 란?

- **CI(Continuous Integration, 지속적인 통합):** 지속적인 빌드와 테스트 자동화를 의미합니다.
- **CD(Continuous Delivery, 지속적인 서비스 제공 또는 지속적인 배포) :** 배포 자동화를 의미합니다.

## 🔹  jenkins 란?

 자바 기반 웹 애플리케이션으로 CI/CD을 위한 도구입니다. 즉  빌드, 테스트, 배포를 자동화해 줍니다. 

## 🔹 이번 시간에 자동화할 것은?

 이번 시간에는 git으로 소스를 저장하면 자동으로 아래의 작업이 진행되도록 할 것입니다. 

 1. 소스 다운로드(git pull) 

2. docker build 

3. docker image tag 

4. docker hub에 해당 이미지 push  

5. docker 이미지 실행 

---

# 2. jenkins 도커 실행하기 전 준비

 뒤에서 jenkins로 작업을 생성할 때 git으로 관리하기 위해서 해당 작업 소스는 모두 git에서 관리할 것입니다. 프로젝트와 dockerfile, docker-compose.yml 모두 한 git repository에 올려 주세요. 

## 🔹  프로젝트 생성

 각자 도커 자동화 배포를 하고 싶은 프로젝트를 생성합니다. 저는 간단하게  Python Flask로 웹을 만들어서 진행하려고 합니다. 

 해당 프로젝트의 전체 경로를 미리 확인해보면 아래와 같습니다. 
{% highlight text %}
jenkins_flask
│
└───requirements.txt
│
└───Dockerfile
│
└───app.py
│
└───requirements.txt
{% endhighlight %}

- app.py

"/" 으로 접속 시 "Hello World!"로 리턴합니다. 그리고 포트도 80으로 설정해 줍니다. 

{% highlight python %}
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello World!'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=80)
{% endhighlight %}

- requirements.txt
 
 pip 설치 시 Flask 가 필요하기 때문에 Flask를 넣어줍니다. 

{% highlight text %}
Flask
{% endhighlight %}

## 🔹 Dockerfile, docker-compose 파일 생성
### 🔸 Dockerfile

 위에서 만든 파일 같은 경로에 해당 파일을 추가해 줍니다. 이전 시간에 배운 [Dockerfile 만들기](https://jungeeyou.github.io/docker-3-post/)를 참고하여 프로젝트에 맞는 Dockerfile를 만들어 줍니다. 
{% highlight text %}
FROM python:3

WORKDIR /usr/src/app

COPY requirements.txt ./
RUN pip install -r requirements.txt
COPY . .

EXPOSE 80
CMD [ "python", "app.py" ]
{% endhighlight %}


### 🔸 docker-compose.yml
 위에서 만든 파일 같은 경로에 해당 파일을 추가해 줍니다. 이전 시간에 배운 [docker-compose를 참고](https://jungeeyou.github.io/docker-4-post/)하여 프로젝트에 맞는 docker-compose.yml 를 만들어 줍니다. 
{% highlight yaml %}
version: '3'
services:
  app:
    build: .
    command: python app.py
    volumes:
      - .:/code
    ports:
      - "80:80"
{% endhighlight %}

## 🔹 dockerhub에 올리기

1. **해당 프로젝트 docker build 하기** 
  위에서 만든 dockerfile과 docker-compose.yml으로 간단히 "docker-compose build app"으로 빌드가 가능합니다. 
  {% highlight bash %}
  $ docker-compose build app
  {% endhighlight %}

2. **docker hub 로그인**  
   {% highlight bash %}
   $ sudo docker login
   {% endhighlight %}

3. **이미지 tag**
    - **<빌드로 생성된 이미지명>**: docker-compose build 명령어로 생성된 이미지 이름을 입력합니다.
    - **<docker_hub_id> :** 자신의 docker hub id를 입력해줍니다.
    {% highlight bash %}
    $ sudo docker tag <빌드로 생성된 이미지명>:latest  <docker_hub_id>/flask:latest
    {% endhighlight %}

4. **docker hub 에 push** 
    {% highlight bash %}
    $ sudo docker push <docker_hub_id>/flask:latest
    {% endhighlight %}

5. **doker hub에서 이미지 확인**  
 [https://hub.docker.com](https://hub.docker.com/) 에서 확인 가능합니다. 

---

# 3. jenkins 도커 실행

## 🔹 도커 실행하기
아래의 명령어를 입력하여 jenkin를 실행합니다. 이미지 "jenkins"를 사용하려고 했지만 [플러그인 설치 이슈](https://www.jenkins.io/blog/2018/12/10/the-official-Docker-image/)가 있어 jenkins/jenkins 로 설치합니다. 

 추가로 젠킨스에서 docker 관련 명령어를 사용합니다. 따라서 현재 서버에서 있는 docker를 볼륨으로 연결해서 젠킨스에 docker를 설치하지 않고 사용하도록 합니다. docker 관련 볼륨을 설정하지 않으면 젠킨스에서 docker 명령어 사용 시 [문제가 발생](https://forums.docker.com/t/docker-not-found-in-jenkins-pipeline/31683/24)합니다. 
{% highlight bash %}
$ docker run -d \
--name my_jenkins \
-p 8080:8080 \
-v [원하는 볼륨 경로]:/var/jenkins_home \
-v /var/run/docker.sock:/var/run/docker.sock \
-v $(which docker):/usr/bin/docker \
-v /usr/local/bin/docker-compose:/usr/local/bin/docker-compose \
-u root jenkins/jenkins
{% endhighlight %}

## 🔹 jenkins 도커 초기 설정
### 🔸 비밀번호 입력
[http://localhost:8080](http://localhost:8080/) 에 접속한 뒤, 아래와 같이 초기 어드민 패스워스 입력 화면이 나오면 방법 1. docker log에 나온 비번이나 혹은 방법 2. 컨테이너 해당 경로(/var/jenkins_home/secrets/initialAdminPassword)에 들어가서 확인합니다. 

![Untitled%209938b766b4e1422e83c19fa97da8d02e/Untitled%201.png](/assets/img/docker/basic_5/Untitled%201.png)

- **방법 1. docker log**

{% highlight bash %}
$ docker logs -f my_jenkins

Jenkins initial setup is required. An admin user has been created and a password generated.
Please use the following password to proceed to installation:

b39b7b29425b4883952ae4bd9f3bde11
{% endhighlight %}

- **방법 2. 도커 컨테이너에서 해당 경로(/var/jenkins_home/secrets/initialAdminPassword)에서 확인**

{% highlight bash %}
$ docker exec -it my_jenkins bash
 

jenkins@afbf7afefc6f:/$ cat /var/jenkins_home/secrets/initialAdminPassword

b39b7b29425b4883952ae4bd9f3bde11
{% endhighlight %}

### 🔸 플러그인 설치

아래 그림과 같이 추천하는 플러그인을 설치합니다.

![Untitled%209938b766b4e1422e83c19fa97da8d02e/Untitled%202.png](/assets/img/docker/basic_5/Untitled%202.png)

### 🔸  관리자 정보 입력

자신의 정보 입력 후 저장을 클릭 합니다. 

![Untitled%209938b766b4e1422e83c19fa97da8d02e/Untitled%203.png](/assets/img/docker/basic_5/Untitled%203.png)

---

# 4. jenkins 작업 생성하기

 우선 docker hub 접속을 위해 Credentials 생성을 진행하고,  pipeline를 이용해서 스테이지 별 작업을 생성해 봅시다. 

## 🔹  Credentials 생성

 docker hub 접속을 위해 Credentials 먼저 생성합니다. 

1. **Credentials 클릭** 

    ![Untitled%209938b766b4e1422e83c19fa97da8d02e/Untitled%204.png](/assets/img/docker/basic_5/Untitled%204.png)

 

2. **나의 docker hub 개정을 추가해 줍니다.** 

- **Username:** docker hub 아이디
- **Password:** docker hub 패스워드
- **ID:** docker-hub 라고 입력

![Untitled%209938b766b4e1422e83c19fa97da8d02e/Untitled%205.png](/assets/img/docker/basic_5/Untitled%205.png)

## 🔹 파이프 라인 만들기

pipeline를 이용해서 스테이지 별 작업을 생성해 봅시다. 위에서 말했듯이  소스 다운로드부터 docker 실행까지 pipeline으로 각 스테이지를 생성하겠습니다. 

1. **새작업 클릭** 
 ![Untitled%209938b766b4e1422e83c19fa97da8d02e/Untitled%206.png](/assets/img/docker/basic_5/Untitled%206.png)

2. **작업 이름 입력 후, pipeline 선택** 
 ![Untitled%209938b766b4e1422e83c19fa97da8d02e/Untitled%207.png](/assets/img/docker/basic_5/Untitled%207.png)

3. **새 작업 테스트** 
    - **Do not allow concurrent builds 체크:** 한 빌드가 진행 중이면 연속적인 빌드를 진행하지 않도록 합니다.
    - **GitHub project:** 자동화하고자 하는 프로젝트 git url를 입력합니다.
![Untitled%209938b766b4e1422e83c19fa97da8d02e/Untitled%208.png](/assets/img/docker/basic_5/Untitled%208.png)

4. **Pipeline Script 작성** 
 스테이지는 총 6단계로 되어있다.  1. Pull 2. Unit Test(pass) 3. Build 4. Tag 5. Push 6. Deploy로 구성되어 있다. git poll에 있는 url에 자신의 git repository url을 넣어줍니다.  withCredentials**는** 위에서 docker hub 접속을 위해 Credentials를 연결하기 위해 생성한 것과 연결하기 위해 필요합니다. 이 데이터는 Push 작업 시 필요합니다.  이제 각 스테이지에 대한 의미를 알아봅시다. 
    1. **Pull:** git 소스를 다운로드 받습니다.  위에서 만든 자신의 프로젝트 git url를 넣어줍니다. 
    2.  **Unit Test:** 빈 값으로 넣어 진행하지 않겠습니다. 
    3. **Build:** docker-compose를 이용해 build를 진행합니다. 
    4. **Tag:** docker image tag 를 진행합니다. 
    5. **Push:** docker hub에 push를 합니다. 
    6. **Deploy:** docker-compose 명령어로 이미지를 실행합니다.
    {% highlight groovy %}
    node {
      git poll: true, url:'https://github.com/JUNGEEYOU/jenkins_flask.git'
      withCredentials([[$class: 'UsernamePasswordMultiBinding',
         credentialsId: 'docker-hub',
         usernameVariable: 'DOCKER_USER_ID', 
         passwordVariable: 'DOCKER_USER_PASSWORD']]) { 
         stage('Pull') {
                git 'https://github.com/JUNGEEYOU/jenkins_flask.git' 
         }
          stage('Unit Test') {
          }
          stage('Build') {
                sh(script: 'docker-compose build app')
          }
          stage('Tag') {
                sh(script: '''docker tag ${DOCKER_USER_ID}/flask \
                ${DOCKER_USER_ID}/flask:${BUILD_NUMBER}''') }
          stage('Push') {
                sh(script: 'docker login -u ${DOCKER_USER_ID} -p ${DOCKER_USER_PASSWORD}') 
                sh(script: 'docker push ${DOCKER_USER_ID}/flask:${BUILD_NUMBER}') 
                sh(script: 'docker push ${DOCKER_USER_ID}/flask:latest')
          }
          stage('Deploy') {
              sh(script: 'docker-compose up -d production') 
          }
        } 
    }
    {% endhighlight %}

5. **파이프 라인 실행 전  docker-compose.yml  를 수정**
 빌드 시 생성될 이미지 명을 수정하여 ${DOCKER_USER_ID}로 이미지가 생성되도록 변경합니다. 그리고 production 부분을 추가하여 docker 실행을 docker-compose로 실행할 수 있도록 추가해 줍니다. 
{% highlight yaml %}
version: '3'
services:
  app:
    build: .
    image: ${DOCKER_USER_ID}/flask
  production:
    image: ${DOCKER_USER_ID}/flask:${BUILD_NUMBER}
    volumes:
      - .:/code
    command: python app.py
    ports:
      - "80:80"
{% endhighlight %}

6. **build now 실행하기** 
"build now"를 클릭하여 배포가 자동으로 되는지 확인합니다. 오른쪽 stage view를 보며 문제가 없는지 log도 확인합니다. 
![Untitled%209938b766b4e1422e83c19fa97da8d02e/Untitled%209.png](/assets/img/docker/basic_5/Untitled%209.png)

7. **Build Triggers 설정하고 자동으로 배포되는지 확인하기** 
  직접 "build now" 클릭해서 배포하는 것이 아닌 build trigger을 이용해 소스가 변경되면 자동으로 배포되도록 변경해 줍니다. 
 "구성"에 들어가 build trigger 부분으로 이동한 뒤 "poll scm" 을 클릭한 후, H/2 * * * *를 입력하여 2분마다 소스가 변경되었는지 확인하도록 합니다. 그 후 저장합니다. 

![Untitled%209938b766b4e1422e83c19fa97da8d02e/Untitled%2010.png](/assets/img/docker/basic_5/Untitled%2010.png)

 확인하기 위해 코드를 수정하고 git  push 해 줍니다. 저는 위에서 만든 flask 웹 return text를 수정하였습니다. 

{% highlight python %}
@app.route('/')
def hello_world():
    return 'Hello World-Build Triggers test'
{% endhighlight %}

그 뒤 "build now" 클릭 없이 자동으로 빌드 되는지 확인해 줍니다. 

---

# 5. 기타 작업 설정

## 🔹 젠킨스 스크립트 git으로 관리

 Pipeline Script의 변경 사항을 확인하거나 해당 스크립트를 보관하기 위해 git으로 저장해서 관리해 봅니다. 

1. **Jenkinsfile 파일을 추가** 
Jenkinsfile 이름으로 파일을 추가하고 위에서 만든 파이프라인 스크립트를 복사해서 넣어준다. 해당 파일은 git에 push 해 줍니다. 
{% highlight groovy %}
node {
  git poll: true, url:'https://github.com/JUNGEEYOU/jenkins_flask.git'
  withCredentials([[$class: 'UsernamePasswordMultiBinding',
     credentialsId: 'docker-hub',
     usernameVariable: 'DOCKER_USER_ID',
     passwordVariable: 'DOCKER_USER_PASSWORD']]) {
     stage('Pull') {
            git 'https://github.com/JUNGEEYOU/jenkins_flask.git'
     }
      stage('Unit Test') {
      }
      stage('Build') {
            sh(script: 'docker-compose build app')
      }
      stage('Tag') {
            sh(script: '''docker tag ${DOCKER_USER_ID}/flask \
            ${DOCKER_USER_ID}/flask:${BUILD_NUMBER}''') }
      stage('Push') {
            sh(script: 'docker login -u ${DOCKER_USER_ID} -p ${DOCKER_USER_PASSWORD}')
            sh(script: 'docker push ${DOCKER_USER_ID}/flask:${BUILD_NUMBER}')
            sh(script: 'docker push ${DOCKER_USER_ID}/flask:latest')
      }
      stage('Deploy') {
          sh(script: 'docker-compose up -d production')
      }
    }
}
{% endhighlight %}

2. **"구성"에 들어가 Pipline 부분으로 이동**
이동한 뒤 아래와 같이 설정해줍니다. 

- **Definition:** Pipline script from SCM 으로 변경
- **SCM:** git 선택
- **Repository URL:** 자신의 Repository url 입력해 줍니다.
- **Script Path:** 위에서 추가한 파일명을 입력해 줍니다. > Jenkinsfile

![Untitled%209938b766b4e1422e83c19fa97da8d02e/Untitled%2011.png](/assets/img/docker/basic_5/Untitled%2011.png)

---

# 마무리

 이번 시간에는 지금까지 배웠던 도커 이미지 빌드부터 이미지 실행까지 jenkins로 자동으로 배포하는 방법을 배웠습니다.