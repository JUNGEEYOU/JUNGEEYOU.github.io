---
layout: post
title: "5. 싱글턴 패턴(Singleton Pattern)"
date: 2019-11-14
excerpt: ""
tags: [Singleton, Singleton Pattern, 싱글톤 패턴, head first design patterns, design pattern, java, 싱글턴 패턴, ]
comments: true
design_patterns: true
---

# 👀 학습 목표

- 고전적인 싱글턴 패턴을 이해하자
- 고전 싱글턴 문제점을 파악하고, 이를 해결한 싱글턴을 만들어 보자.

# 1. 고전적인 싱글턴 패턴

## 1-1.  싱글턴 패턴이란?

- 해당 클래스의 인스턴스가 하나만 만들어지고, 어디서든 그 인스턴스를 접근할 수 있도록 하기 위한 패턴

## 1-2. 싱글턴 패턴 적용 예시

- 스레드 풀
- 캐시
- 대화상자
- 사용자 설정
- 레지스트리 설정을 처리하는 객체
- 로그 기록용 객체
- 프린터 혹은 그래필 카드 같은 디바이스를 위한 디바이스 드라이버

## 1-3. 고전적 싱글턴 패턴이란?

- 우선 고전적인 싱글턴 패턴은 아래와 같다.  생성자는 private로 정의하고, getInstance() 메소드는 static 메소드로 정의하여 유일한 인스턴스 생성이 성립된다. 아래와 같이 정의를 하면 Singleton 클래스는 무조건 객체 생성 시, Singleton.getInstance() 를 호출하여 객체를 불러와 한 번만 인스턴스가 생성되며, 그 이후는 이전에 생성된 인스턴스를 계속 사용한다.

### 고전적 싱글턴 패턴 코드
{% highlight java %}
    public class Singleton {
        private static Singleton uniqueInstance;  // 정적변수로 정의하여 유일한 인스턴스 정의
    
        private Singleton(){}
    
        public static Singleton getInstance(){   // static 메소드로 생성하여 private 생성자를 호출할 수 있도록한다. 
            if(uniqueInstance==null){
                uniqueInstance = new Singleton();  // 게으른 인스턴스 생성: 필요하기 전에는 아예 생성을 안함 
            }
            return uniqueInstance;
        }
    }
{% endhighlight %}
### 고전적인 싱글턴 UML

![](/assets/img/design_pattern/singleton/Untitled.png)

## 1-4. 🙊 고전적인 싱글턴 문제점

- 만약 위의 코드를 멀티스레딩 한다고 하면  우연히 "new Singleton()" 코드를 각 각 실행하게 되어 인스턴스가 2개가 생성될 가능성이 있다.

---

# 2.  고전적 싱글턴 문제 해결

## 2-1. synchronized 키워드 사용

> synchronized 키위드를 추가하면 한 스레드 메소드가 끝나기 전에는 다른 스레드는 기다려 두 메소드는 동시에 실행되지 않습니다.
{% highlight java %}
    public class Singleton {
        private static Singleton uniqueInstance; 
    
        private Singleton(){}
    
         //synchronized: 한 스레드 메소드가 끝나기 전까지 다른 스레드는 기다림
        public static synchronized Singleton getInstance(){    
            if(uniqueInstance==null){
                uniqueInstance = new Singleton(); 
            }
            return uniqueInstance;
        }
    }
{% endhighlight %}
- 그런데 이렇게 하면 해결되지만, 동기화하면 속도 문제가 생기지 않을까?
    - 사실 동기화 하기 아깝다고 생각할 수 있다. 왜냐면 사실 synchronized 키워드가 필요한 시점은 처음 인스턴스 생성 시에만 필요하기 때문입니다.

## 2-2. 인스턴스를 필요할 때 생성하는 것이 아닌, 처음부터 만들자.

- getInstance()의 속도가 그리 중요하지 않다면 위에 코드를 사용하는 것도 문제가 되지 않습니다. 그러나 속도에 문제가 된다면, 인스턴스를 처음부터 생성합니다.
{% highlight java %}
    public class Singleton {
        private static Singleton uniqueInstance = new Singleton();
    
        private Singleton(){}
    
        public static  Singleton getInstance(){   
            return uniqueInstance;
        }
    }
{% endhighlight %}
## 2-3. DCL(Double-Checking Locking) 을 써서 getInstance()에서 동기화되는 부분을 줄인다.

- volatile 키워드를 사용하여 "instance" 변수를 메인 메모리에 저장하여 스레드가  자신의 캐시 메모리에서 데이터를 얻는 것이 아닌 무조건 메인 메모리에서 데이터를 읽어 스레드 간 데이터가 동일하게 합니다.
- 메소드를 전체 동기화하는 것이 아닌 null인 경우만 동기화(synchronized) 진행합니다.
    - 메소드 전체를 동기화하는 것이 아니라서 오버헤드를 줄일 수 있습니다.
{% highlight java %}
    public class Singleton {
        private volatile static Singleton instance;
        private Singleton() {}
    
        public static Singleton getInstance() {
            if (instance == null)  {
                synchronized(Singleton.class) {
                    if (instance == null) {
                        instance = new Singleton();
                    }
                }
            }
    
            return instance;
        }
    }
{% endhighlight %}
---

# 참고

- Head first design patterns 책

# 소스 코드

- [https://github.com/JUNGEEYOU/Observer_Pattern](https://github.com/JUNGEEYOU/Singleton-Pattern)
