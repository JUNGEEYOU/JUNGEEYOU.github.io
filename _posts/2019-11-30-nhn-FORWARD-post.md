---
layout: post
title: "[NHN FORWARD 2019] 세미나 후기"
date: 2019-11-26
excerpt: ""
tags: [NHN FORWARD 2019, NHN FORWARD, NHN FORWARD 후기, 세미나]
comments: true
seminar: true
---
# [2019.11.26/ NHN FORWARD 2019] 세미나 후기

# 0. 들어가기 전에 - 후기

![](/assets/img/seminar/nhn_forward/Untitled.png)

![](/assets/img/seminar/nhn_forward//Untitled%201.png)

- 여러 트랙이 존재하여 듣고 싶은 것을 선택하기에 좋았습니다. 적게 올 줄 알았는데, 사람이 정말 많이 와서(800명 넘게?) 놀랬습니다. 그리고 저는 오후에는 머신러닝 기초를 들어 여러 세션은 듣지 못 하였습니다.
- 참고 사이트: [NHN FORWARD](https://forward.nhn.com/presentation-session)

# 1. 점진적으로 프런트엔드 프레임워크 교체하기(AngularJS to Vue)

> 현재 angluarjs를 쓰고있어서 vue나 혹은 react로 변경하고 싶었다. 따라서 이 세션을 들어보았다.

## 1. 점진적으로 교체해야하는 이유?

- 전면 교체는 리스크가 크다.
    - 컴포넌트 단위로 조금씩 변경
    - 일부 페이지만 변경하기!
    - 마이크로 프레임 워크 고려하기!

## 2. 교체 전 필수 준비 사항

1. 서비스 기획서 확보 
2. QA 테스트 케이스 문서 확보 
3. 코드를 통한 기능 파악 

## 3. 점진적으로 변경하는 법

1. spa 포기, 별도 페이지로 분리 
- 단점: 페이지가 계속 깜빡인다....

2. spa 유지하고, iframe 사용하기 

- 단점: 페이지 깜빡이고... qhdks cnldir

3. spa 유지, vue in angular js 

- 라우터는 angluar js에서만 하도록 해야함

## 4. 두 프레임워크 간 통신하는 방법

1. vue의 리액티브 시스템 활용 
2. vuex dispatch, commit 사용 
3. vue에서 angularjs 전달 

## 5. story book을 사용

- 여러 명이서 개발 가능하며 이를 이용해 개발 속도를 올릴 수 있다고 함.

## 6. 마이크로 프런트 엔드

- 참고: [https://medium.com/@tomsoderlund/micro-frontends-a-microservice-approach-to-front-end-web-development-f325ebdadc16](https://medium.com/@tomsoderlund/micro-frontends-a-microservice-approach-to-front-end-web-development-f325ebdadc16)

---

# 2.  머신러닝 기초

> 머신러닝 기초에 대해서 들었고, 간단한 머신러닝 예제를 같이 학습하였다. 정말 기초부터 듣고 싶은 사람에 추천하고, 기초가 아닌 응용된 것을 듣고 싶은 사람에겐 지루할 것 같습니다.  중간에 듣다가 기록을 하지 못하여  머신러닝까지만 기록한 내용입니다.

## 1. 머신러닝 기초

### 1-1. 머신러닝이란?

> `데이터`를 잘 표현할 수 있는 `모델`을 찾고, 모델을 이용해 새로운 데이터를 `예측`하는 방법

- 데이터를 잘 표현할 수 있는 모델
    - 기준: 예측값과 실제의 값의 차이(Loss)가 작을수록 좋은 모델
- 모델
    - 여러가지 모델을 지님

### 1-2. 딥러닝

- 머신러닝과 다르게 데이터를 구분해줌

### 1-3. 요약

> 어떤 data와 어떤 purpose로 하는지 정의가 중요!

## 2. PyTorch로 시작하기

### 2-1. python/pyTorch 기초

1. 개발환경

- jupyter

2. 개발 언어: python 

- 인터프리터 언어, 동적 타이핑 언어

2. pytorch 사용 

- **pytorch** vs tensorflow

3. 라이브러리
-  numpy 
-  pillow

## 3. linear regression model

### 3-1. 문제 정의

> 머신러닝을 사용하여 어떤 태스크를 풀든지 간에 제일 중요한 것은 문제를 명확히 정의 하는 것입니다.

- 여기서는 중간고사 점수로 기말고사 예측

![](/assets/img/seminar/nhn_forward//Untitled%202.png)

1. **데이터셋 정의** 
- 데이터 가져오기
{% highlight python %}
    # PyTorch의 기능들을 사용하기에 앞서 torch 패키지를 불러옵니다.
    import torch
    from torch.utils.data import Dataset, DataLoader
    from torch.optim import SGD
    from utils import *
    
    class MyDataset(Dataset):
        def __init__(self, dbpath):
            super(MyDataset, self).__init__()
            self.data = []
            
            with open(dbpath, 'r') as file:
                for line in file.readlines():
                    midterm_score, final_score = line.strip().split(',')
                    features  = float(midterm_score)
                    labels = float(final_score)
                    self.data.append([features,labels])
        
        def __getitem__(self, index):
            #pass #TODO: data 리스트의 index번째 요소를 리턴해봅시다. #
            return torch.tensor(self.data[index][0]), torch.tensor(self.data[index][1])
        
        def __len__(self):
            #pass #TODO#
            return len(self.data)
    
    # Do not modify below this line #
    ds = MyDataset("datasets/score.csv")
    print("Total nubmer of data in the dataset: {}".format(len(ds)))
    print("7th data in the dataset: {}".format(ds[7]))
{% endhighlight %}
2. **데이터셋 로더** 

- 데이터셋 객체로부터 손쉽게 데이터를 추출하기 위한 유틸리티 객체
{% highlight python %}
    ds = MyDataset("datasets/score.csv")
    
    #ds_loader = #TODO#
    ds_loader = DataLoader(ds, batch_size=4, shuffle=True)
    
    # Do not edit below this line #
    for features, labels in ds_loader:
        print(features, labels)
        break
{% endhighlight %}
3. **모델 정의** 
{% highlight python %}
    from torch.nn import Parameter
    
    class MyScoreModel(torch.nn.Module):
        def __init__(self):
            super(MyScoreModel, self).__init__()
            #pass #TODO#
            self.weight = Parameter(torch.randn(1))
            self.bias = Parameter(torch.randn(1))
            #또는
            #self.register_parameter('weight', self.weight)
            #self.register_parameter('bias', self.bias)
        
        def forward(self, x):
            #output =  #TODO#
            output = self.weight * x + self.bias
            return output
    
    # Do not edit below this line #
    model = MyScoreModel()
    for p in model.parameters():
        print(p)
{% endhighlight %}
**4. loss 정의** 

- MSELoss

5. **Optimizer(최적화 방법) 정의**

- SGD optimizer 사용

![](/assets/img/seminar/nhn_forward/Untitled%203.png)

## 4. Logistic Regression with PyTorch

- 합격 불합격 분류 문제라면?

- 선형함수? 계단함수?  > 부드러운 계단함수!?
- 중간고사와 기말고사 성적 데이터가 입력으로 주어졌을 때, 패스/유급 여부를 예측하는 Logistic Regression 모델을 구현하기

![](/assets/img/seminar/nhn_forward/Untitled%204.png)
