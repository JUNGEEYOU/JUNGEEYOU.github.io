---
layout: post
title: "2. 배열과 리스트 / 선택 정렬"
date: 2020-01-04
excerpt: ""
tags: [algorithm, 선택 정렬 알고리즘, 배열, 리스트]
comments: true
algorithm: true
image: /assets/img/algorithm/2/back.jpg
feature: /assets/img/algorithm/2/back.jpg

---

# 👀 목차

- 배열과 연결 리스트에 대해 공부합니다.
- 퀵 정렬의 기초 작업인 **선택 정렬**에 대한 학습을 합니다.

---

# 1. 메모리가 동작하는 방법

- 컴퓨터에 아주 많은 서랍이 있다고 생각하고, 그 모든 서랍에는 모두 주소가 붙여 있다고 생각하자. 그럼 컴퓨터에게 메모리를 요청할 때, 서랍의 주소를 줄 것이고 거기 안에 데이터를 담을 것이다.
- 만약 여러 데이터를 담아야할 때는 배열 혹은 연결리스트를 사용한다.<span style="background-color: #e6e6ff; font-clolr: #000000"> 이때 중요한 것은 언제 어떤 것을 사용할 지가 중요하다.</span>

---

# 2. 배열과 연결 리스트

> 여러 개의 항목을 저장해야할 때, 어떤 것을 사용할지 두 가지 장단점을 생각하고 결정하자!

 - 배열 vs 연결 리스트 
 
 |  |  배열 | 연결 리스트 | 
 |----------|----------|----------|
 | 특징 | 1. 데이터들이 서로 이웃하게 메모리를 사용해야만 한다.  2.  미리 크기를 정해 좋고 사용하지 않으면 메모리 낭비. 또는 크기 이상을 사용하려면 메모리 전체가 이동해야한다. | 1. 데이터들은 서로 이웃하지 않아도 된다.  따라서 배열 처럼 전체가 이동할 필요가 없다. 2. 다음 데이터의 주소를 저장해 둔다.   |
 | 예시 | 영화관에서 4명이 같이 와서 모두 같이 앉는 경우  | 영화관에서 4명이 같이 왔지만, 흩어져서 영화를 보는 경우   |
 | 읽기 | O(1)  ⇒ 임시 접근   | O(n)  ⇒ 순차 접근 : 마지막 데이터를 보기 위해선 처음 부터 순서대로 데이터를 확인해야만. 왜냐하면 이전 데이터가 다음 데이터 주소를 저장하기 때문이다.   |
 | 삽입  | O(n) ⇒ 1. 중간에 데이터를 넣기 위해선 뒤에 있는 데이터는 모두 밀어야한다. 2. 만약 삽입 시, 메모리 크기가 넘어가면 전체 이동해야한다.  | O(1) ⇒ 삽입 시, 이전 데이터가 가지고 있는 다음 데이터 주소는 내가 갖고, 내 주소는 이전 데이터에게 주면 된다.  |
 | 삭제  | O(n)  ⇒ 삭제된 데이터의 자리를 뒷 데이터들이 다시 순차적으로 채워 줘야한다.  |  O(1) ⇒ 삭제 시, 삭제할 데이터의 다음 데이터 주소를 이전 데이터에게 주면 된다.   |
 {:.inner-borders}

---

# 3. 선택 정렬

> 가수 별로 내가 몇 곡을 들었는지 기록되어 있다고 해 봅시다. 이때 이 목록을 가장 많이 들을 것 부터 가장 적게 들은 순서로 정렬을 하여 내가 가장 좋아하는 가수의 순위를 알고 싶다면 어떻게 해야할까요? 이때 선택 정렬을 사용하여 봅시다.


## 🔹 선택 정렬이란?

가장 간단한 정렬 알고리즘 중 하나 입니다. <span style="background-color: #e6e6ff; font-clolr: #000000"> n개의 레코드 중에서 최소값(최대값)을 찾아 첫 번째 위치에 놓고, 나머지 (n-1)개 중에서 다시 최소값(최대값)을 찾아 두 번째 위치에 놓는 방식을 반복하여 정렬하는 방식이다. </span>

아래의 예제를 살펴 보자. 오름차순 정렬이며, 화살표는 비교한다는 의미이다. 

![2/Untitled%201.png](/assets/img/algorithm/2/Untitled%201.png){ width: 200%; }

### 순서도

![2/Untitled%202.png](/assets/img/algorithm/2/Untitled%202.png)

## 🔹 선택 정렬의 수행 시간

첫 번째 루프에서는 (n-1) 번 비교를 진행합니다. 두 번째에서는 (n-2) 그러므로 총 비교 횟수는 

$$(n-1) + (n-2) + ... + 2 + 1  = n(n-1)/2$$

하지만 이전에 빅오표기법을 배웠을 때, 상수는 제거하게 된다.  선택 정렬의 수행시간은 아래와 같다. 

$$O(N^2) $$

## 🔹  예시 코드 - python
{% highlight python %}
    #-*- coding:utf-8 -*-
    def find_smallest(item_list):
        """
        리스트 중 가장 작은 수의 인덱스를 리턴
        :param item_list:
        :return:
        """
        small_num = item_list[0]
        small_index = 0
    
        for i in range(1, len(item_list)):
            if small_num > item_list[i]:
                small_num = item_list[i]
                small_index = i
        return small_index
    
    
    def selection_sort(item_list):
        """
        선택 정렬 함수
        :param item_list:
        :return: 오름차순 정렬 리턴
        """
        sort_list =list()
        for i in range(len(item_list)):
            small_num = find_smallest(item_list)
            sort_list.append(item_list.pop(small_num))
        return sort_list
    
    my_list = [5, 2, 1, 4, 7, 10]  
    print (selection_sort(my_list))   # [1, 2, 4, 5, 7, 10]
{% endhighlight %}

# 소스 코드

- [https://github.com/JUNGEEYOU/selection_sort](https://github.com/JUNGEEYOU/selection_sort)

# 참고

- Hello Coding 그림으로 개념을 이해하는 알고리즘

