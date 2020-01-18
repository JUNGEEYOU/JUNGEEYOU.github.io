---
layout: post
title: "Threading 세마포어(Semaphore) 적용"
date: 2019-12-02
excerpt: ""
tags: [python, Semaphore , thread]
comments: true
python: true
---


> 세마포어를 이용하여 제한된 스레드만 진입 가능하도록 설정해보자.

# 1. Semaphore

> 세마포어는 각 acquire() 호출로 감소하고, 각 release() 호출로 증가하는 내부 카운터를 관리합니다. 카운터는 절대로 0 밑으로 내려갈 수 없습니다; acquire()가 0을 만나면, release()를 호출할 때까지 기다리면서 블록합니다.

- **acquire**(): 세마포어를 얻는 것. 내부 카운터가 0보다 크면, 1 감소시키고 True를 즉시 반환합니다. 0이면, release()가 호출될 때까지 기다린 다음 True를 반환합니다.
- **release**(): 세마포어를 반답하고 내부 카운터를 1 증가하여 세마포어를 얻기 위해 대기하는 테스크에게 작업을 하게 할 수 있습니다.

# 2. 소스 코드
{% highlight python %}
    import threading
    import time
    
    # 세마포 객체 생성. 한번에 실행될 쓰레드를 3개로 제한
    sem = threading.Semaphore(3)
    
    def run_semaphore():
        sem.acquire()
        print("세마포어 테스트 진행중 ")     # 여기가 3개의 쓰레드 존재 
        time.sleep(2)  
        sem.release()
    
    
    threads = []
    # 생성할 쓰레드 개수. 여기서는 10개의 쓰레드를 생성한다.
    for num in range(10):
        thread = threading.Thread(target=run_semaphore)
        threads.append(thread)
    
    for th in threads:
        th.start()  # 쓰레드 시작
    
    for th in threads:
        th.join()  # 종료대기
    
    print('Finish All Threading ')
{% endhighlight %}
# 참고 사이트

- [https://docs.python.org/ko/3/library/asyncio-sync.html#asyncio.Semaphore](https://docs.python.org/ko/3/library/asyncio-sync.html#asyncio.Semaphore)
