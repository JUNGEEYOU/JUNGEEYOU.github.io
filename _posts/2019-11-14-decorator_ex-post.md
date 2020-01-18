---
layout: post
title: "3. 데코레이터 패턴(Decorator pattern) 적용 - Python"
date: 2019-11-14
excerpt: ""
tags: [decorator, Decorator Pattern, 데코레이터 패턴, head first design patterns, design pattern, Python, 데코레이터 패턴 적용]
comments: true
design_patterns: true
---

# 1. 데코레이터 패턴 설명

> 이전에 배운 데코레이터 패턴을 간단하게 알아보자

![](/assets/img/design_pattern/decorator_ex/Untitled-7a71bc78-c672-4065-aeac-e90a165428e4.png)

> 다이어그램을 보면서 각 기능에 대해 이해해보자

- **Component**
    - 추상 클래스 혹은 인터페이스로 구현
    - 기본 요소인 ConcreateComponentA와 추가적 요소인 Decorator로 구성되어 있다.
- **ConcreateComponenetA**
    - 기본적인 요소로 이전 예제에서는 기본 음료인 에스프레소 등이 이에 해당된다.
- **Decorator**
    - 추상 클래스 혹은 인터페이스로 구현
    - 추가적 요소로 실제 구성은  자식 클래스에서 구현한다.
- **ConcreateDecoratorA**
    - 자신이 장식하고 있는 객체에게 행동을 위임하는 것 외에 원하는 추가적인 작업을 수행하도록 만들 수 있다.

---

# 2. 데코레이터 패턴 적용

> 시스템이 진행 중에 에러 메시지를 보낸다고 해보자. 이때 상황에 따라 다른 메신저 조합을 사용하고 싶을 때가 있을 것이다. 만약 여러 사람에게 보낸다면?  이를 데코레이터 패턴에 적용하여 아래와 같이 다이어그램을 작성하였다.

![](/assets/img/design_pattern/decorator_ex/-e00c4c38-29ed-43e3-bda4-a1ba3afbf712.png)

## 코드 들어가기 전에...

- [https://stackoverflow.com/questions/2124190/how-do-i-implement-interfaces-in-python](https://stackoverflow.com/questions/2124190/how-do-i-implement-interfaces-in-python)
- 위 링크를 들어가면 누가 python interface 구현을 어떤식으로 하냐 질문이 올라와있다. 아래 답변을 보면 python은 인터페이스가 필요하지 않고, 그 이유는 멀티 상속이 가능하기 때문이다. 그리고 인터페이스 모듈도 존재하긴 한다. (abc 모듈 사용)

![](/assets/img/design_pattern/decorator_ex/-01312777-e4d5-45b8-8e90-c5bcc07a454c.png)

- **Componenent 구현**

> NotifierComponenent 를 만들고, send 는 추상메소드로 만들어야 해서 pass로 작성해 실제 구현은 하지 않았다. 단지 상속하고 구현을 안하면 에러가 발생하도록 만들었다. abc 모듈은 사용하지 않겠다.
{% highlight python %}
    class NotifierComponenent:
        """
         알림 Componenent 인터페이스
        """
        def send(self):
            raise NotImplementedError("Please Implement this method")
{% endhighlight %}
- **ConcreateComponenetA - 기본 알림 클래스 구현**
{% highlight python %}
    class BasicNotifier(NotifierComponenent):
        """
        기본 알림 클래스 - log만 진행
        """
    
        def send(self, message):
            print("기본 로그 출력: " + message)
{% endhighlight %}
- **Decorator**
{% highlight python %}
    class Decorator(NotifierComponenent):
        pass
{% endhighlight %}
- **ConcreateDecoratorA**

> sms, kakao 등
{% highlight python %}
    class SmsSender(Decorator):
        """
        핸드폰 메시지 전송 데코레이터 클래스
        """
        notifier = NotifierComponenent
        receiver = ""
    
    
        def __init__(self, notifier=NotifierComponenent, receiver=str()):
            self.notifier = notifier
            self.receiver = receiver
    
        def send(self, message):
            self.notifier.send(message)
            print('sms 전송: '+ message + "to: "+ self.receiver)
{% endhighlight %}
- **main문**
{% highlight python %}
    send_lena = KakaoSender(SmsSender(BasicNotifier(), "lena"), "lena")
    send_lena.send("error message")
{% endhighlight %}
- 출력
{% highlight text %}
    기본 로그 출력: error message
    sms 전송: error message to: lena
    KAKAO 전송: error message to: lena
{% endhighlight %}
---

# 전체 코드

[https://github.com/JUNGEEYOU/test_python](https://github.com/JUNGEEYOU/test_python)
