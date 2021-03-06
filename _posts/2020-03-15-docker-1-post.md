---
layout: post
title: "docker에 대해 알아보자! (docker 기본 개념)"
date: 2020-03-15
excerpt: ""
tags: [docker , docker 기본 개념 ]
comments: true
docker : true 
feature: /assets/img/docker/basic_1/whale.jpeg
image: /assets/img/docker/basic_1/Moby-logo.png
---

# docker에 대해 알아보자!

> 도커를 사용은 했지만 기본 지식이 부족한 것 같아 기초부터 탄탄히 하려고 한다. 그럼 예전부터 계속 핫한🔥 도커에 대해 알아보자.

<br/>

# 💡 목차

> 이번 시간에 학습할 부분은 아래와 같다.  전체적으로 무엇을 배울지 미리 확인하고 가자.

1.  **도커(docker)란?** 
    - 도커의 기본 개념을 이해 합니다.
2. **가상 머신과 도커** 
    - 도커를 이해하기 위해 비슷한 개념인 가상머신과 비교하여 이해해 봅시다.
3. **도커를 사용하는 이유** 
    - 도커를 왜 사용하는지 알아 봅시다.
4. **도커 이미지와 컨테이너 이해** 
    - 도커의 핵심 개념으로 이미지와 컨테이너가 무엇인지 알아 봅시다.
5. **도커 아키택처** 
    - 도커 전체적인 아키텍처를 살펴 봅시다.
  
<br/>

---

# 1. 도커(docker)란?

> 아래의 도커 로고를 보며 도커 의미를 알아보자. 로고의 의미를 확인 하면 핵심 개념이 들어 있음을 확인할 수 있습니다.

![docker/Untitled.png](/assets/img/docker/basic_1/Untitled.png)

- 도커란 리눅스 컨테이너를 기반으로 합니다. 이는 특정한 프로그램의 <span style="background-color: #e6e6ff; font-clolr: #000000">배포 및 관리를 단순하게 해주는 유용한 오픈소스 </span> 프로젝트 입니다.
- 도커는 가상머신의 개념과 유사하지만, 차이점이 존재합니다.
    - <span style="background-color: #e6e6ff; font-clolr: #000000">도커와 유사한 가상머신과 비교하며 </span> 도커에 대해 알아봅시다.
    
<br/>

---

# 2.  도커 컨테이너 vs 가상머신

> 도커 컨테이너 개념을 쉽게 이해하기 위해 차이점을 알아보자.

### 🔹  가상 머신

- **OS 가상화**
    - 가상 머신에는 항상 아래 그림과 같이 Guest OS 가 필요
    - 이미지에는 OS가 포함되어 이미지 용량이 큼
    - 결과적으로 가상머신 이미지로 배포하기에 어려움이 존재

![docker/Untitled%201.png](/assets/img/docker/basic_1/Untitled%201.png){: width="60%" height="60%"}
[도커 문서](https://docs.docker.com/get-started/) Containers and virtual machines  참조 

### 🔹  도커 컨테이너

- **프로세스 격리 방식**
    - 한 서버에 여러 개의 컨테이너를 실행하며, 서로 영향을 미치지 않고, 독립적으로 실행 가능
- **vm과 달리 guest OS 설치 필요 없음**
    - 자원을 host os와 공유하여 용량이 낮음
    - Host OS와 컨테이너의 차이만 패키징하여 저장( Host OS와 컨테이너 간 공유)
    - 결과적으로 vm과 다르게 배포하기에 수월

![docker/Untitled%202.png](/assets/img/docker/basic_1/Untitled%202.png){: width="60%" height="60%"}
[도커 문서](https://docs.docker.com/get-started/) Containers and virtual machines 참조 

<br/>

---

# 3. 도커를 사용하는 이유

## 📌 개발과 배포가 편리해 진다.

- **기존 배포 방식**
    - <span style="background-color: #e6e6ff; font-clolr: #000000">운영 환경에 배포를 하려면, 해당 os 및 버전에 맞춰 개발 환경을 세팅</span> 해야합니다. os가 조금이라도 달라지면, 세팅 방식이 바뀌게 됩니다. 이런 점에서 개발 세팅과 배포는 너무 어려움이 많아집니다.
- **도커를 사용하게 되면?**
    - 도커를 사용하여 <span style="background-color: #e6e6ff; font-clolr: #000000">운영환경에 배포하려고 하면, 해당 컨테이너를 도커 이미지를 만들어 운영 서버에 전달</span>하기만 하면 됩니다. 따라서 운영 서버에 새롭게 패키지나 각종 라이브러리 설치 등으로 인한 의존성을 걱정할 필요가 없습니다.
    - 이것이 가능한 이유는 이전에 설명한 가상머신의 이미지와 달리 이미지 크기가 작기 때문입니다. 또한, 중복되는 레이어는 재사용하여 배포 속도가 빨라집니다.


<br/>

## 📌 독립성과 확장성이 높아진다.

> 해당 내용을 설명하기 전에 모놀리스 구조와 마이크로 서비스 구조를 이해하고, 왜 도커를 사용하는지 알아봅시다.

### 🔹  모놀리스 구조

- 아래의 그림과 같이 모든 기능이 하나의 프로그램에서 구동되는 방식입니다.
- <span style="background-color: #e6e6ff; font-clolr: #000000">서비스가 복잡&거대 해질 수록 확장하기 어렵다는 단점이 </span> 있습니다.

![docker/Untitled%203.png](/assets/img/docker/basic_1/Untitled%203.png){: width="50%" height="50%"}

<br/>

### 🔹 마이크로서비스 구조

- 아래의 그림과 같이 여러 모듈이 독립된 형태입니다.
- 독립되기 때문에 **각 기능은 언어에 종속되지 않고, 변화에 빠른 대처가 가능**합니다.
- <span style="background-color: #e6e6ff; font-clolr: #000000"> 컨테이너는 쉽게 생성이 가능하며, 각 컨테이너가 독립된 환경을 제공</span>합니다. 따라서 마이크로 구조에서 가장 많이 사용됩니다.
    - 아래 그림을 서비스한다고 해 봅시다. DB 컨테이너와 웹 서버 컨테이너로 분리할 수 있습니다. 웹 서버에 부하 발생 시, 컨테이너를 늘려 확장이 가능합니다. 또한 웹과 DB를 컨테이너로 독립된 형태로 구성할 수 있습니다.

![docker/Untitled%204.png](/assets/img/docker/basic_1/Untitled%204.png){: width="60%" height="60%"}


<br/>

---

# 4. 도커 이미지와 컨테이너 이해

> 도커에서 사용하는 기본 단위는 이미지와 컨테이너 입니다. 이는 도커의 핵심 개념입니다.

![docker/Untitled%205.png](/assets/img/docker/basic_1/Untitled%205.png){: width="80%" height="80%"}

### 🔹  도커 이미지

> 이미지는 위와 같이  <span style="background-color: #e6e6ff; font-clolr: #000000"> 1) 베이스 이미지와 2) 도커파일(Dockerfile)로 생성된 이미지</span>가 존재한다. 1) 베이스 이미지는 Ubuntu, centos, nginx, mongodb 등이 존재합니다. 이는 상위 이미지를 가지고 있지 않습니다.  2) 도커파일로 생성하는 이미지는 커스터마이징을 하기 위해 사용됩니다. 예를 들어 우분투 + nginx 이미지를 만들고 싶을 때 사용됩니다.

- **도커 컨테이너를 실행하기 위해 필요한 설정 템플릿**
    - 이미지는 도커 컨테이너를 생성하기 위해 필요합니다.
    - 가상머신을 생성할 때 사용하는 iso 파일과 비슷한 개념입니다.
- **레이어 방식(여러 개의 계층 구조)**
    - 수정과 추가되는 부분은 레이어를 추가하는 방식입니다.
- **커스터마이징 가능**
    - 이미지는 커스터마이징이 가능하며, 그러기 위해서는 Dockerfile를 작성하면 됩니다.
    - 작성한 이미지는 [docker hub](https://hub.docker.com/)에 등록하거나 docker register를 만들어 저장할 수 있습니다.
    
<br/>

### 🔹  도커 컨테이너

- **이미지를 실행(run)한 상태**
    - 위 그림과 같이 실행을 하면 컨테이너가 생성된다.
- **다른 컨테이너들과 독립적 존재**
    - 한 서버에 여러 컨테이너들은 서로 영향을 주지 않으며, 호스트에도 영향을 주지 않습니다.
- **데이터 영구적 보관 x**
    - 컨테이너가 데이터를 영구적으로 보관하지 않으므로 컨테이너를 삭제하면 안에 있는 데이터와 상태들까지도 삭제된다. 이때 volume을 사용하면 컨테이너를 삭제하더라도 데이터 또는 상태를 보존할 수 있다.

<br/>

---

# 5. Docker architecture

아래 그림과 같이 도커의 구조는 두 가지가 존재합니다. <span style="background-color: #e6e6ff; font-clolr: #000000">클라이언트와 서버</span> 입니다. 

![docker/Untitled%206.png](/assets/img/docker/basic_1/Untitled%206.png)
[도커 문서](https://docs.docker.com/engine/docker-overview/) Docker architecture 참조 

### 🔹 Daemon

 도커 서버는 실제 컨테이너를 생성 및 실행하며 이미지를 관리합니다. 이는 dockerd(docker daemon) 에서 동작합니다. 

### 🔹 Client

  도커 클라이언트로 사용자가 명령어를 입력(예를 들면 'docker run') 하면 도커 클라이언트는 명령어를 도커 데몬에게 api로 전달합니다. 

### 🔹 Registry

 도커 이미지를 저장하는 곳이다. 기본적으로 Docker Hub에 퍼블릭 레지스트리로 생성할 수 있으며 프라이빗 레지스트리도 생성할 수 있습니다. 

### 🔹 Docker 객체

- **Images** : 컨테이너를 생성하기 위한 **read-only** 템플릿이며 기존 이미지를 커스터마이징하여 새로운 이미지를 만들 수도 있습니다.
- **Containers**: Image를 실행(run)한 것이다. 또한 컨테이너는 다른 컨테이너와 호스트 컴퓨터로부터 독립적이다.


<br/>

---

# 마무리

> 이번 시간에는 도커의 기본 개념에 대해서 알아봤습니다. 아래 공부하기 좋은 사이트도 같이 보시면 좋을 것 같습니다. 다음 시간에는 docker를 실제로 사용해 보겠습니다. 🤗

### 📌 docker 공부하기 좋은 사이트 리스트

- [https://docs.docker.com/get-started/](https://docs.docker.com/get-started/)
- [https://github.com/remotty/documents.docker.co.kr](https://github.com/remotty/documents.docker.co.kr)
