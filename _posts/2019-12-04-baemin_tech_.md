---
layout: post
title: "우아한 레디스(Redis)  세미나 후기 -강대명님"
date: 2019-11-18
excerpt: ""
tags: [우아한 Tech,Redis, 레디스, 세미나, 우아한 redis, 후기, 배달의 민족   ]
comments: true
seminar: true
redis: true
---


# 0. 들어가기 전에 - 후기

> 회사에서 redis를 사용하고 있어서 개선할 점이나 다른 회사는 어떤 식으로 사용하는지 보기 위해서 세미나에 참석하게 되었다. 강대명님의 세미나를 듣고, redis 관리 방법이나 사용하면 주의 점 등 기존에 몰랐던 점을 많이 알게 되어서 정말 유익하였습니다.👏👏

- 강연 자료: [https://www.slideshare.net/charsyam2/redis-196314086](https://www.slideshare.net/charsyam2/redis-196314086)
- 강연 업로드될 곳: [https://www.youtube.com/channel/UC-mOekGSesms0agFntnQang](https://www.youtube.com/channel/UC-mOekGSesms0agFntnQang)

---

# 0. 목차

1. Redis 소개 
2. 왜  Collection이 중요한가?
3. Redis Collections
4. Redis 운영
5. Redis 데이터 분산
6. Redis failover

---

# 1. Redis 소개

## 1-1. 소개 전에 Cache

### # 캐시 구조 1. Look aside Cache

- 현재 제 프로젝트 중 하나가  redis 캐시 사용 시, 이 구조를 사용하고 있다.

1. web server에서 데이터가 존재하는지 캐시에서 먼저 확인 

2. 캐시에 데이터가 있으면 캐시에서 가져감

3. 캐시에 없으면 db에서 데이터를 읽고, 캐시에 이를 제출 

### # 캐시 구조 2. Write Back

- 로그를 캐시에 저장하고 나중에 db에 저장하는 구조로 사용
1. web server는 모든 데이터를 캐시에만 저장 
2. 캐시에 특정 시간동안 데이터 저장. 
3. 캐시에 있는 데이터를 db에 저장 
4. db에 저장된 데이터는 삭제 

---

# 2. 왜  Collection이 중요한가?

## 2-1. 중요한 이유?

- 외부 collection을 잘 이용하는 것으로 여러 개발 시간  단축 및 문제를 줄여줌 (편의성, 개발 난이도 줄임)

## 2-2. Redis 사용 처

- Remote Data Store
    - a 서버, b 서버, c서버에서 데이터를 공유하고 싶을 때 사용
- redis 는 원자성을 보장함(싱글 스레드)
- 주로 사용하는 곳들
    - 인증 토큰 등을 저장
    - 랭킹 보드 사용(sorted set)
    - 유저 api limit
    - 잡 큐(list)

---

# 3. Redis Collections

> 세미나에서는 자세하게는 다루지 않았습니다.  컬렉션을 잘 못 선택하면 속도에 문제가 생길 수 있어 주의가 필요합니다.

## 3-1. collection 설명

- string
- list
- set
    - 데이터가 있는지 없는지만 체크하는 용도
- Sorted set
    - 랭킹에 따서 순서가 바뀌길 원하는 경우 사용
    - 유저 랭킹 보드에 사용
    - 주의: score는 double 타입이기 때문에 값이 정확하지 않을 수 있다. > 컴퓨터에서는 실수가 표현할 수 없는 정수값들이 존재!!
- Hash

## 3-2. Collection 주의 사항

- **한 컬렉션에 너무 많은 아이템 넣지 말기**
    - 10000개 이하 몇 천개 수준으로 유지하는 것이 좋음
- **expire는 item 개별로 걸리지 않고, 전체 collection에 대해서만 걸림**
    - 모두 삭제될 수 있으니 주의!

---

# 4. Redis 운영

## 4-1. 메모리 관리를 잘하자!

### 메모리 관리

- Physical Memory이상을 쓰면 문제가 발생
    - Swap이 있으면 Swap를 사용하지만 해당 메모리 page 접근 시 마다 늦어진다. 그리고 Swap를 한번 사용하면 계속 사용하게 되어 디스크를 사용하여 느려진다.
    - Swap 없으면 OOM으로 죽음!
- Maxmemory 설정을 하더라도 이보다 더 사용할 가능성 큼.
- RSS 값 모니터링 필요
- 큰 메모리를 사용하는 한 인스턴스 보단 작은 메모리를 사용하는 인스턴스가 더 안전하다.

### 메모리를 줄이기 위한 설정

- 기본적으로 collection 들은 아래 같은 자료구조를 사용
    - hash → hashtable 하나 더 사용
    - Sorted Set →  Skiplist 와 HashTable 사
    - Set -> HashTable 사용
- **Ziplist 사용하기**
    - in-memory 특성 상, 적은 개수라면 선형 탐색을 하더라도 빠름

## 4-2. O(N)  관련 명령어는 주의!

> redis 는 싱글 스레드이다. 따라서 여러개의 명령어를 동시에 처리하지 못한다. 그럼 O(N) 명령들을 주의해서 사용해야 한다.

### 대표적인 o(n) 명령어

- keys
- FLUSHALL, FLUSHDB
- Delete Collections
- Get All Collections

### 대표적인 실수 사례

- key가 백만개 이상인데 keys사용하는 경우
    - scan 명령어로 긴 명령을 짧은 여러번의 명령으로 변경 가능
- 아이템이 몇 만개든 hash, sorted set, set에서 데이터를 가져오는 경우
    - 일부만 가져오기
    - 큰 collection을 작은 여러 collection으로 나눠서 저장
- Spring security oath RedisTokenStore 이슈
    - 최신 버전에서는 set(o(1))로 해결

---

# 5. Redis 데이터 분산

- 데이터 특성에 따라 선택 방법이 달라진다
    - 캐시인 경우, 우아한 redis
    - persistent 해야하면 안 우아한 redis

### 1. **Consistent Hashing**

### 2. **Sharding**

- 데이터를 어떻게 나눌까? 어떻게 찾을까?
- 하나의 데이터를 모든 서버에서 찾아야 한다면?
- 상황마다 샤딩 전략이 달라짐

    2-1. **Range**: range 지정 후, 거기에해당되는 것 저장. 한 range 서버에 몰릴 가능성 높음 

    2-2. **Modular**: n%k 서버로 데이터 결정. range보다는 균등 분배 가능성 높음. 서버 추가 시 재분배 양도 많아짐 

    2-3. **Indexed**:  key가 어디에 저장될지 관리하는 서버가 따로 존재 

### 3. **Redis Cluster**

- 장점: 자체적인 primay, secondary failover, slot 단위의 데이터 관리
- 단점: 메모리 사용량이 더 많고, migration 자체는 관리자가 시점을 결정해야함

---

# 6. Redis failover

- Coordinator 기반 Failover
- VIP/DNS 기반  Failover
- Redis Cluster 사용

---

# 7. Monitoring

## 7-1. Redis Info 정보

- RSS
- Used Memory
- Connection 수
- 초당 처리 요청 수

## 7-2. System

- CPU
- Disk
- Network rx/tx

## 7-3. CPU가 100% 칠 경우

1. 처리량이 많은 경우
    - 좀 더 CPU 성능 좋은 것으로 이전
2. O(N) 명령어 많은 경우
- monitor 명령어를 통해 특정 패턴 파악

---

# 8. 결론

- redis 는 좋은 툴
- 그러나 메모리 빡빡한 경우, 관리에 어려움
- Client-output-buffer-limit 설정 필요
- **Cache로 쓰는 경우,**  문제가 적게 발생
    - Redis가 문제가 있을 때 , DB등의 부하가 어느정도 증가하는지 확인 필요.
- **Persistent Store로 쓰는 경우**
    - 무조건 Primary/Secondary 구조로 구성이 필요
    - 메모리를 절대로 빡빡하게 사용하면 안됨
        - 정기적인 마이그레이션 필요
        - 가능하면 자동화 툴 만들어 사용
