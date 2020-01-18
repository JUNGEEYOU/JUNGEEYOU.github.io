---
layout: post
title: "3. 데코레이터 패턴(Decorator pattern)"
date: 2019-11-14
excerpt: ""
tags: [decorator, Decorator Pattern, 데코레이터 패턴, head first design patterns, design pattern]
comments: true
design_patterns: true
---

# 👀 학습 목표

- 데코레이터 패턴을  이해한다.
- 데코레이터 패턴과 관련된 디자인 원칙을 학습한다.

---

# 1. 데코레이터 패턴이란?

## 1-1. 데코레이터 패턴이란?

- 기본 객체(Component) 에 추가적인 요소(Decorator)를 동적으로 첨가하는 것을 말한다. 여기서 데코레이터는 기본 객체(Component)의 서브 클래스로 만들어 유연하게 확장 가능하다.

![](/assets/img/design_pattern/decorator/Untitled-ac09e63c-051a-46aa-8d06-f6ffe7a0e3a7.png)

> 예를 들어 펭수에게 추운 겨울을 지내기 위해 옷을 입힌다고 해보자. 그럼 펭수는 여기서 기본 객체인 Componet라고 할 수 있고, 추가적인 요소 옷들은 Decorator라고 할 수 있다.

## 데코레이터 패턴 다이어그램 이해

![](/assets/img/design_pattern/decorator/Untitled-7a71bc78-c672-4065-aeac-e90a165428e4.png)

> 다이어그램을 보면서 각 기능에 대해 이해해보자

- **Component**
    - 추상 클래스 혹은 인터페이스로 구현
    - 기본 요소인 ConcreateComponentA와 추가적 요소인 Decorator로 구성되어 있다.
- **ConcreateComponenetA**
    - 기본적인 요소로 다음 예제에서는 기본 음료인 에스프레소 등이 이에 해당된다.
- **Decorator**
    - 추상 클래스 혹은 인터페이스로 구현
    - 추가적 요소로 실제 구성은  자식 클래스에서 구현한다.
    - 다음 예제에서는 커피의 추가 요소인 우유, 휘핑크림 등이 이에 해당된다.

---

# 2. 데코레이터 패턴 이해

> 커피 전문점을 운영하는 CEO라고 생각해보자. 다양한 음료를 팔기 위해 다양한 음료를 모두 포괄하는 주문 시스템을 만들어야 한다.

## 2-1. 스타버즈 커피 만들기 ☕️

- 처음에는 아래와 같이 간단히 추상 클래스 음료를 만들고, 실제 커피는 "음료"를 상속 받는 것으로 구성하였습니다.

![](/assets/img/design_pattern/decorator/Untitled-1947d9b8-2c4d-47d0-9bc7-60dbdcd1eac3.png)

### - 문제점 😹
{% highlight text %}
    1. 커피 주문 시 스팀 우유, 모카, 휘핑크림을 추가하며 이를 추가할 때도 커피 가격이 올라갑니다. 
    > 현재 다이어그램에 이를 추가하면 "휘핑크림이 추가된 에스프레소", "휘핑크림이 추가된 모카" 등등 클래스를 
    수 없이 추가해야합니다. 
    2. 우유 가격이 인상되거나 카라멜 토핑이 추가되면 관련된 모든 클래스를 수정& 추가 해줘야합니다. 
{% endhighlight %}
## 2-2. 문제 해결된 커피 주문 시스템 만들기 1

- 그러면 "음료"라는 추상 클래스에 각 음료에 우유, 두유, 모카, 휘핑 크림이 들어가는지 여부를 나타내는 메소드를 추가하여 비용을 알 수 있도록 합시다.

![](/assets/img/design_pattern/decorator/Untitled-b07bd878-3a74-4212-8f1b-c21a1b542757.png)

### - 문제점 😹
{% highlight text %}
    1. 가격이 바뀌면 계속 기존 코드를 수정해줘야한다. 
    2. 첨가물의 종류가 많아지면 또 ~들어가니?()와 ~넣기() 메소드를 추가해줘야하고, 
    수퍼클래스 비용() 메소드에 첨가물을 확인하는 코드를 넣어줘야한다. 
    3. 새로운 음료 출시로 아이스티가 생겼는데, 이도 역시 관련없는 우유들어가니?()같은 메소드를 상속 받아야한다. 
    4. 손님이 더블 모카를 요청하면 어떻게 해야할까요?  
{% endhighlight %}
## 2-3. 문제 해결된 커피 주문 시스템 만들기 2

- 데코레이터 패턴을 적용하기 전에 OCP 디자인 원칙에 대해 배워 봅시다.

### - OCP(Open-Closed Principle)

> 아래 원칙을 보면 위에서 작성한 코드는 반대로 설계 했다는 것을 알 수 있죠? 확장(새로운 음료 추가)는 어려웠고, 코드 변경은 계속 했어야 합니다!

    클래스는 확장에 대해서는 열러 있어야한다. 반면, 코드 변경에는 닫혀 있어야한다. 

### - 데코레이터 패턴 적용하기 전에...

> 위에서 설명했던 데코레이터 패턴 입니다. 그럼 커피 시스템에 어떤 식으로 적용해야 할지 고민해 봅시다.

![](/assets/img/design_pattern/decorator/Untitled-7a71bc78-c672-4065-aeac-e90a165428e4.png)

- **Component**
    - 추상 클래스 혹은 인터페이스로 구현
    - 기본 요소인 ConcreateComponentA와 추가적 요소인 Decorator로 구성되어 있다.
- **ConcreateComponenetA**
    - 추상 클래스 혹은 인터페이스로 구현
    - 기본적인 요소로 이  예제에서는 기본 음료인 에스프레소 등이 이에 해당된다.
- **Decorator**
    - 추가적 요소로 실제 구성은  자식 클래스에서 구현한다.
    - 이 예제에서는 커피의 추가 요소인 우유, 휘핑크림 등이 이에 해당된다.
- **ConcreateDecoratorA**
    - 자신이 장식하고 있는 객체에게 행동을 위임하는 것 외에 원하는 추가적인 작업을 수행하도록 만들 수 있다.

### - 데코레이터 패턴 적용하기

![](/assets/img/design_pattern/decorator/Untitled-2445edbb-e543-4131-8b0c-de20dd219be3.png)

- **Beverage**
    - 데코레이터와 기본 요소가 이를 상속한다.
    - 여기서 추상 클래스로 구현한 이유는 단지, 처음에 구현 시, 추상 클래스로 구현되서 인터페이스로 구현 안한 것 뿐이다.  이는 인터페이스로 구현해도 된다.
- **CondimemtDecorator**
    - 데코레이터로 Mocha, Whip 같은 커피의 첨가물들이 이를 상속한다.
- **Decaf, HoseBlend, Espresso 등**
    - 이는 Component 들로 기본 요소를 나타낸다.

### - 적용 코드

- Class 정의
{% highlight java %}
    /*1. Beverage: 추상 클래스로 Component가 이에 해당된다. */
    public abstract class Beverage {
        String description = "제목 없음";
    
        public String getDescription(){
            return description;
        }
    
        public abstract double cost();
    }
    
    /*2. CondimentDecorator: 추상 클래스로 Decorator가 이에 해당된다. */
    public abstract class CondimentDecorator extends Beverage {
        // getDesciption(): 추상 메소드인 이유는? 첨가물 데코레이터 클래스들이 새로 구현하기 위해
        public abstract String getDescription();
    }
    
    /* Decaf: 기본 요소 중 하나 */
    public class Decaf extends Beverage {
    
        public Decaf() {
            description = "Decaf Coffee";
        }
    
        public double cost() {
            return 1.05;
        }
    }
    
    /* Espresso: 기본 요소 중 하나 */
    public class Espresso extends Beverage {
        public Espresso(){
            description = "에스프레소";
        }
        public double cost(){
            return 1.99;    // 에스프레소 가격
        }
    }
    
    /* HoseBlend: 기본 요소 중 하나 */
    public class HoseBlend extends Beverage {
        public HoseBlend(){
            description = "하우스 브랜드 커피";
        }
        public double cost(){
            return 0.89;    // 하우스 블랜드 커피 가격
        }
    }
    
    /* Mocha: 데코레이터 중 하나  */
    public class Mocha extends CondimentDecorator {
        Beverage beverage;
    
        public Mocha(Beverage beverage){
            this.beverage = beverage;    // 자신이 감쌀 음료를 저장한다. 
        }
        public String getDescription(){
    				// 자신이 감싼 음료의 상세 설명 + 자신의 설명 
            return beverage.getDescription() + ",  모카";   
        }
        public double cost(){
            return 0.2 + beverage.cost();     // 자신이 감싼 음료의 가격 + 자신의 가격
        }
    }
    
    /* Soy: 데코레이터 중 하나  */
    public class Soy extends CondimentDecorator {
        Beverage beverage;
    
        public Soy(Beverage beverage) {
            this.beverage = beverage;
        }
    
        public String getDescription() {
            return beverage.getDescription() + ", Soy";
        }
    
        public double cost() {
            return .15 + beverage.cost();
        }
    }
    
    /* Whip: 데코레이터 중 하나  */
    public class Whip extends CondimentDecorator {
        Beverage beverage;
    
        public Whip(Beverage beverage) {
            this.beverage = beverage;
        }
    
        public String getDescription() {
            return beverage.getDescription() + ", Whip";
        }
    
        public double cost() {
            return .10 + beverage.cost();
        }
    }
    
{% endhighlight %}
- main

> beverage2 객체를 잘 확인해보자.

- "디카페인" 인 기본 요소를 생성 한다. > Mocha객체가 "디카페인" 객체를 감싼다.  > Mocha객체가 "모카가 들어간 디카페인" 객체를 감싼다. > Whip 객체가 "더블모카가 들어간 디카페인" 객체를 감싼다.
{% highlight java %}
    public class Main {
    
        public static void main(String[] args) {
      
            Beverage beverage2 = new Decaf();
            beverage2 = new Mocha(beverage2);
            beverage2 = new Mocha(beverage2);
            beverage2 = new Whip(beverage2);
            System.out.println(beverage2.getDescription()
                    + " $" + beverage2.cost());    // Decaf Coffee,  모카,  모카, Whip $1.55
        }
    }
{% endhighlight %}
- 아래 사진은 beverage2 객체를 디버깅한 결과이다. 해당 객체를 보면 Whip이 모카를 감싸고, 모카는 모카를 감싸고, 모카는 디카페인 커피 객체를 감싼 것을 확인할 수 있다.

    ![](/assets/img/design_pattern/decorator/Untitled-a4433054-535b-4954-b5d7-fee9c071737f.png)

- beverage2.getDescription(), beverage2.cost()
    - 이 두 메소드를 호출 순서를 살펴보자.  두 메소드가 비슷하기에 cost() 메소드만 확인하자. beverage2는 현재 Whip 객체로 되어있고, Whip 객체의 cost()메소드는 아래 코드와 같이 구성되어 있다. 여기서 beverage는 whip이 감싼 Mocha 객체이다. 이 객체의 cost()는 return 0.2 + beverage.cost(); 를 리턴하는 메소드이다. 그럼 또 모카가 감싼 모카 cost() 메소드를 호출한다. 그리고 마지막으로 모카는 Decaf 커피 객체를 감싸서  return 1.05; 를 리턴한다.
{% highlight java %}
        // Whip cost() 
        public double cost() {
                return .10 + beverage.cost();
            }
{% endhighlight %}
    - 정리하자면,  아래와 같다.

    > 저는 이는 마치 재귀함수 호출과 비슷하다고 생각하였다.

    ![](/assets/img/design_pattern/decorator/Untitled-fed515c4-0e85-482f-9c1a-4ba55b5a5b5c.png)

---

# 3. 데코레이터 패턴 적용 예

> 자바 i/o에 파일에서 데이터를 읽어오는 기능이 데코레이터 패턴이다.  여기서는 데코레이터가 행동 위임뿐만 아니라 기능 추가 작업도 한다. 예를 들어 LineNumberInputStream 은 데이터를 읽을 때, 행 번호를 붙어주는 기능을 추가해주는 역할을 합니다.

- 확인 가능
    - [https://docs.oracle.com/javase/7/docs/api/](https://docs.oracle.com/javase/7/docs/api/)
    - InputStream 클래스 참고

        ![](/assets/img/design_pattern/decorator/1-703bdd08-5b8e-4f0e-be3b-ecbd3db31ab5.png)

- LowerCaseInputStream 생성

> 대문자를 소문자로 변경하는 데코레이터 추가
{% highlight java %}
    import java.io.*;
    
    public class LowerCaseInputStream extends FilterInputStream {
    
        public LowerCaseInputStream(InputStream in) {
            super(in);
        }
    
    // 바이트 read() 
        public int read() throws IOException {
            int c = in.read();   // 꾸미는 객체 read() 함수 호출 
            return (c == -1 ? c : Character.toLowerCase((char)c));  // 해당 클레스의 기능 추가 
        }
    
    // 바이트 배열 read() 
        public int read(byte[] b, int offset, int len) throws IOException {
            int result = in.read(b, offset, len);
            for (int i = offset; i < offset+result; i++) {
                b[i] = (byte)Character.toLowerCase((char)b[i]);
            }
            return result;
        }
    }
{% endhighlight %}
- main
- in2.read() 호출 시, 1. LowerCaseInputStream.read() 호출 2. LowerCaseInputStream 안에서  BufferedInputStream.read() 호출 3. BufferedInputStream 안에서 FileInputStream.read() 호출하고 결과 리턴 ....
{% highlight java %}
    public class Main {
    
        public static void main(String[] args) throws IOException {
        int c;
        try {
            InputStream in2 =
                    new LowerCaseInputStream(          // 구현한 클래스를 생성 : 대문자> 소문자 
                            new BufferedInputStream(   // 속도 향상을 위한 버퍼 저장 
                                    new FileInputStream("src\\input\\test.txt")));  // 기본 요소인 파일 읽는 역할 
            while((c = in2.read()) >= 0) {
                System.out.print((char)c);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    }
{% endhighlight %}
# 참고

- Head first design patterns 책

---

# 소스 코드

- [https://github.com/JUNGEEYOU/Decorator_ex](https://github.com/JUNGEEYOU/Decorator_ex)
