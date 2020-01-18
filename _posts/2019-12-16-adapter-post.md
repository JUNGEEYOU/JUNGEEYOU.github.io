---
layout: post
title: "7-1. 어댑터 패턴(Adapter Pattern)"
date: 2019-12-16
excerpt: ""
tags: [Adapter, Adapter Pattern, 어댑터 패턴, head first design patterns, design pattern]
comments: true
design_patterns: true
---

# 👀 학습 목표

- 어댑터 패턴을 이해하자.
- 어댑터 패턴을 적용하자.
- 데코레이터 vs 어댑터 vs 퍼사드 패턴 차이를 알아보자.

---

# 1.  어댑터 패턴이란?

## 1-1. 어댑터 개념

![7%201/Untitled.png](/assets/img/design_pattern/adapter/Untitled.png)

- 새로운 업체에서 제공하는 클래스 라이브러리를 사용해야 한다고 합시다. 그런데 이 새로 채택한 업체에서 사용하는 인터페이스가 기존 업체에서 사용하던 인터페이스와 다르다고 가정하면 이때 필요한 것이 어댑터입니다.
- 어댑터는 클라이언트로부터 요청을 받아 새로운 업체에서 제공하는 클래스에서 받아들일 수 있는 형태의 요청으로 변환시켜주는 중개인 역할을 합니다.

## 1-2. 어댑터란?

- 어댑터 패턴을 적용하면 인터페이스가 변경되어도 클라이언트는 변경할 필요 없습니다.
- **Client**: 타겟 인터페이스만 바라보며 타겟 인터페이스를 호출
- **Target**: 아래 예시에서는 Duck 인터페이스 의미
- **Adapter**: 어댑티로 구성하며, target 인터페이스를 구현
- **Adaptee**: 모든 요청은 결국 어댑티에게 위임됨. 기존 시스템를 의미하고 아래 예시에서는 Turkey 를 의미

![7%201/Untitled%201.png](/assets/img/design_pattern/adapter/Untitled%201.png)
{% highlight text %}
    한 클래스의 인터페이스를 클라이언트에서 사용하고자 하는 다른 인터페이스로 변환합니다. 
    어댑터를 이용하면 인터페이스 호환성 문제 때문에 같이 쓸 수 없는 클래스들을 연결해서 쓸 수 있습니다. 
{% endhighlight %}
## 1-3. 두가지 종류의 어댑터

- 1. 객체 어댑터 2. 클래스 어댑터 나뉩니다. 객체 어댑터는 자바에서 다중 상속이 안되서 사용되며, 클래스 어댑터는 다중 상속이 되는 언어에서 사용됩니다.

### 1. 객체 어댑터

- 자바와 같은 다중 상속이 안 되는 언어에서 사용

![7%201/Untitled%202.png](/assets/img/design_pattern/adapter/Untitled%202.png)

### 2. 클래스 어댑터

- 다중 상속이 가능한 언어에서 사용

![7%201/Untitled%203.png](/assets/img/design_pattern/adapter/Untitled%203.png)

---

# 2. 어댑터 패턴 적용하기

## 2-1. 오리 어댑터로 감싼 칠면조 만들기

> Turkey를 Duck으로 변화해주는 어댑터를 만들자. (Turkey(기존 시스템) > Duck)

- Duck / Turkey 인터페이스
{% highlight java %}
    public interface Duck {
        public void quack();
        public void fly();
    }
    
    
    
    public interface Turkey {
        public void gobble();
        public void fly();
    }
{% endhighlight %}
- MallardDuck/WildTurkey  클래스
{% highlight java %}
    public class MallardDuck implements Duck {
        public void quack() {
            System.out.println("Quack");
        }
    
        public void fly() {
            System.out.println("I'm flying");
        }
    }
    
    public class WildTurkey implements Turkey {
        public void gobble() {
            System.out.println("Gobble gobble");
        }
    
        public void fly() {
            System.out.println("I'm flying a short distance");
        }
    }
{% endhighlight %}
- 어댑터 TurkeyAdapter
{% highlight java %}
    // 실제는 가금류지만 DUCK 으로 변환
    public class TurkeyAdapter implements Duck {
        Turkey turkey;
    
        public TurkeyAdapter(Turkey turkey) {
            this.turkey = turkey;
        }
    
        public void quack() {
            turkey.gobble();
        }
    
        public void fly() {
            for(int i=0; i < 5; i++) {
                turkey.fly();
            }
        }
    }
{% endhighlight %}
- 테스트
{% highlight java %}
    public class DuckTestDrive {
        public static void main(String[] args) {
            /*
            출력 결과
                1. Turkey 가 말한다
                Gobble gobble
                I'm flying a short distance
                2. DUCK이 말한다
                Quack
                I'm flying
                3. turkeyAdapter가 말한다
                Gobble gobble
                I'm flying a short distance
                I'm flying a short distance
                I'm flying a short distance
                I'm flying a short distance
                I'm flying a short distance
                */
            MallardDuck duck = new MallardDuck();
    
            WildTurkey turkey = new WildTurkey();
            Duck turkeyAdapter = new TurkeyAdapter(turkey);  //실제는 가금류지만 오리처럼 보이게 함
    
            System.out.println("1. Turkey 가 말한다");
            turkey.gobble();
            turkey.fly();
    
            System.out.println("2. DUCK이 말한다");
            testDuck(duck);
    
            System.out.println("3. turkeyAdapter가 말한다");
            testDuck(turkeyAdapter);
    
    
    
    
        }
        static void testDuck(Duck duck){
            duck.quack();
            duck.fly();
        }
    
    }
{% endhighlight %}
## 2-2. AC 파워 어댑터

- **클라이언트**:  타겟 인터페이스(Duck)에 맞게 구현되어 있습니다.
- **어댑터**: 위에 예제에서는 TurkeyAdapter 클래스이다. 이는 Duck으로 구현하였고, 구현 내용에는 어댑티가 존재하였다.
- **어댑티**: Turkey 인터페이스가 이에 해당하며, 기존 시스템을 의미한다.

### - 클라이언트에서 어댑터 사용 방법

1. 클라이언트에서 타겟 인터페이스를 이용하여 메소드를 호출함으로써 어댑터에 요청 
2. 어댑터는 어댑티 인터페이스를 이용하여 그 요청을 어댑티에 대한 메소드 호출로 변환 
3. 클라이언트에서는 호출 결과를 받긴 하지만 중간에 어댑터가 껴 있는지는 모른다. 

![7%201/Untitled%204.png](/assets/img/design_pattern/adapter/Untitled%204.png)

## 2-3. 어댑터 실전 예제- enumeration을 iterator에 적응시키기

- Iterator 가 타겟이다.  Enumeration > Iterator  적응 시켜보자
- EnumerationIterator  어댑터 코드
{% highlight java %}
    mport java.util.Enumeration;
    import java.util.Iterator;
    
    public class EnumerationIterator implements Iterator {
        Enumeration enumeration;
    
        public EnumerationIterator(Enumeration enumeration){
            this.enumeration = enumeration;
        }
        @Override
        public boolean hasNext() {
            return enumeration.hasMoreElements();
        }
    
        @Override
        public Object next() {
            return enumeration.nextElement();
        }
    
        @Override
        public void remove() {
            throw new UnsupportedOperationException();   // enumeration은 remove()가 없기 때문에 예외를 던진다. 
        }
    }
{% endhighlight %}
---

# 3. 데코레이터 vs 어댑터 vs 퍼사드 패턴 용도 비교

- **데코레이터**: 인터페이스는 바꾸지 않고 책임(기능)만 추가
- **어댑터**:  한 인터페이스를 다른 인터페이스로 변환
- **퍼사드**: 인터페이스를 간단하게 바꿈

---

# 참고

- Head first design patterns 책

---

# 소스 코드

- [https://github.com/JUNGEEYOU/Adapter-Pattern](https://github.com/JUNGEEYOU/Adapter-Pattern)
