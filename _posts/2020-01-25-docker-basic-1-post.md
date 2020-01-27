---
layout: post
title: "docker에 대해 알아보자."
date: 2020-01-25
excerpt: ""
tags: [docker , docker 기본 개념 ]
comments: true
docker: true
---
# 👀 목차

1. docker 사용 이유 
2. Docker  vs 가상머신 
3. docker 이미지와 컨테이너 개념

---
# 1. docker 사용 이유

> 서버 관리는 어렵다. 그런데 도커를 이용하면 서버가 다른 환경이여도 같은 서버를 띄울 수 있다.  또한, 여러 개의 도커 컨테이너를 독립적으로 띄울 수 있다.

---

# 2.  Docker  vs 가상머신

![1%20docker/Untitled.png](/assets/img/docker/basic-1/Untitled.png)
[https://www.docker.com/resources/what-container](https://www.docker.com/resources/what-container) 참고 

## 2-1. 가상머신

> 가상머신은 실제 하드웨어와 직접적인 통신이 없는 가상 컴퓨터를 말합니다.  우리가 주로 사용하는 virtualbox나 vmware과 같은 가상머신은 반 가상화를 이용합니다. 가상 머신은 편하긴 하지만 속도가 느립니다. 그림 오른쪽과 같이 **항상 guest OS를 설치해야 하며 가상머신 이미지 안에 OS가 포함되므로 이미지 용량이 커집니다.**

## 2-2. docker

> Docker는 반가상화보다 좀더 경량화된 방식입니다. 그림 왼쪽과 같이 게스트 OS를 설치하지 않습니다. Docker 이미지에 서버 운영을 위한 프로그램과 라이브러리만 격리해서 설치할 수 있고, **OS 자원은 호스트와 공유**합니다. 이렇게 되면서 이미지 용량이 크게 줄어들었습니다.
Docker는 Git에서 소스를 관리하는 것처럼 이미지 버전 관리 기능을 제공합니다.(Repository) 그리고 중앙 관리를 위해 저장소에 이미지를 올리고, 받을 수 있습니다.`(Push/Pull)`. 게다가 GitHub처럼 Docker 이미지를 공유할 수 있는 `Docker Hub`도 제공 합니다.

---

# 3. docker 이미지와 컨테이너 개념

![1%20docker/Untitled%201.png](/assets/img/docker/basic-1/Untitled%201.png)
[https://docs.docker.com/engine/docker-overview/](https://docs.docker.com/engine/docker-overview/) 이미지 참고 

## 3-1. IMAGES

> 도커 이미지는 `docker container을 실행하기 위해 필요한 설정 템플릿`이다. 이미지는 커스터마이징 할 수 있으며, 그러기 위해서는 Dockerfile를 작성하면 됩니다. 이를 작성했으면, docker hub에 등록하거나 docker registry를 만들어 저장할 수 있습니다.

- 추가로 이미지는 레이어 방식으로 수정되거나 추가된 부분은 레이러를 추가하는 방식이다.

## 3-2. CONTAINERS

> `image 실행(run)한 상태`를 뜻합니다. 이는 다른 컨테이너들과 독립적으로 존재합니다. 운영체제로 보면 이미지는 실행 파일이고 컨테이너는 프로세스이다.
