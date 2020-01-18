---
layout: post
title: "1. 스트래티지 패턴(Strategy pattern)"
date: 2019-11-04
excerpt: ""
tags: [Strategy, Strategy Pattern, 스트래티지 패턴, head first design patterns, design pattern]
comments: true
design_patterns: true
---
# 👀 학습 목표

- 스트래티지 패턴 이해한다.
- 스트래티지와 관련된 디자인 원칙을 학습한다.
- 디자인 패턴을 배워야하는 이유를 파악한다.

---

# 1. 스트래티지 패턴?
변경되는 행동들을 각각을 캡슐화하여 교환해서 사용할 수 있도록 만든다. 여기서 스트래티지을 활용하면 클라이언트(변경되지 않는 것)와는 독립적으로 변경되는 행동들을 바꿀수 있다. + 캡슐화된 행동은 상위 형식에 맞춰 개발한다. (다형성)

- 여기서 캡슐화는 애플리케이션에서 달라지는 부분을 뽑아서 캡슐화하는 것이다. 따라서 바뀌지 않는 부분은 영향을 받지 않고, 변경된 부분만 고치거나 확장 가능하다.


# 2. 스트래티지 패턴 이해
오리 관련 프로젝트를 진행하면서, 스트래티지 패턴을 이해합시다.

## 2-1. 간단한 simduck 애플리케이션 만들기
오리관련 클래스를 만들어 보자. Duck 수퍼 클래스를 만들고, Duck를 상속하여 다양한 종류의 오리를 만들 수 있다.

![](/assets/img/design_pattern/strategy/Untitled-07fd3d40-6774-4aa3-ae5d-0174d68d183c.png)

- 위의 다이어그램 구현 결과
    - 실제로는 각 클래스가 다른 파일로 구성되었습니다. 간단히 보기 위해 소스를 통합하였습니다.
{% highlight java %}
// 1. Duck: 수퍼 클래스 
public abstract class Duck {  
        // 실체가 아닌 공통적인 특성을 모아 놓은 클래스라서 추상 클래스로 구현
        public void quack(){
            System.out.println("꽥꽥~!");
        }
        public void swim(){
            System.out.println("음파음파~!");
        }
        // 오리마다 모양이 달라서 추상메소드로 구현
        public abstract void display();
    }
    
    //2. MallardDuck: Duck 상속 받는 오리의 한 종류 
    public class MallardDuck extends Duck {
        public void display(){
            System.out.println("청둥오리입니다!");
        }
    
    }
    
    //3. RedheadDuck: Duck 상속 받는 오리의 한 종류 
    public class RedheadDuck extends Duck {
        public void display(){
            System.out.println("빨간머리오리입니다!");
        }
    } 
{% endhighlight %}
- main
{% highlight java %}
    public class Main {
        public static void main(String[] args) {
    	Duck mallarduck = new MallardDuck();
    	Duck redheadduck = new RedheadDuck();
    
    	System.out.println("청둥오리 quack()");
    	mallarduck.quack();        			// 꽥꽥~!
    	System.out.println("청둥오리 swim()");
    	mallarduck.swim();					// 음파음파~!
    	System.out.println("청둥오리 display()");
    	mallarduck.display();				// 청둥오리입니다!
    
    	System.out.println("빨간오리 quack()");
    	redheadduck.quack();   				// 꽥꽥~!
    	System.out.println("빨간오리 swim()");
    	redheadduck.swim();					// 음파음파~!
    	System.out.println("빨간오리 display()");
    	redheadduck.display();				// 빨간머리오리입니다!
    
    
        }
    }
{% endhighlight %}

## 2-2. 날아다니는 오리를 만들자.

### 1) 수퍼 클래스에 fly() 메소드 추가?
경쟁 회사를 이기기 위해 오리가 날도록 해야 합니다. 그런데 별로 어려운 것이 아니죠. 왜냐하면 아래와 같이 수퍼 클래스에 fly() 메소드만 추가하면 되기 때문입니다.

{% highlight java %}
    public abstract class Duck {
        // 실체가 아닌 공통적인 특성을 모아 놓은 클래스라서 추상 클래스로 구현
        public void quack(){
            System.out.println("꽥꽥~!");
        }
        public void swim(){
            System.out.println("음파음파~!");
        }
        public void fly(){
            System.out.println("파다파닥!");
        }
        // 오리마다 모양이 달라서 추상메소드로 구현
        public abstract void display();
    }
{% endhighlight %}

### -  문제점 😹

 모든 서브 클래스는 나는 것이 아닙니다.  > 러버덕이 날게 된다! 

### 2) fly() 메소드를 러버덕이 오버라이드하기

날지 못하는 오리 클래스가 fly()를 오버라이드하면, 그냥 쉽게 해결됩니다. 추가적으로 러버덕은 "꽥꽥~!" 소리를 내는 것이 아니라 "삑삑~!" 소리를 내야 합니다. 따라서 fly()와 같이 오버라이드 합니다.

![](/assets/img/design_pattern/strategy/Untitled-1352c160-9718-4af2-b2bb-12460c12deaf.png)

{% highlight java %}
    // 1. Duck: fly() 메소드 구현 
    public abstract class Duck {
        // 실체가 아닌 공통적인 특성을 모아 놓은 클래스라서 추상 클래스로 구현
        public void quack(){
            System.out.println("꽥꽥~!");
        }
        public void swim(){
            System.out.println("음파음파~!");
        }
        public void fly(){
            System.out.println("파다파닥!");
        }
        // 오리마다 모양이 달라서 추상메소드로 구현
        public abstract void display();
    }
    
    // 2. RubberDuck: fly() 메소드 오버라이드하여 날 수 없게 함 & quack 소리 변경 
    public class RubberDuck extends Duck {
        public void display() {
            System.out.println("러버덕입니다!");
        }
        @Override
        public void fly(){
        }
        @Override
        public void quack(){
            System.out.println("삑삑~!");
        }
    }    
{% endhighlight %}
- main
{% highlight java %}
    public class Main {
    
        public static void main(String[] args) {
    	Duck mallarduck = new MallardDuck();
    	Duck redheadduck = new RedheadDuck();
    	Duck rubberduck = new RubberDuck();
    
    	System.out.println("청둥오리 fly()");
    	mallarduck.fly(); 			   // 파다파닥!
    	System.out.println("빨간오리 fly()");
    	redheadduck.fly();             // 파다파닥!
    	System.out.println("러버덕 fly()");
    	rubberduck.fly();              //
    	System.out.println("러버덕 quack()");
    	rubberduck.quack();            //삑삑~!
    
        }
    }
{% endhighlight %}

### -  문제점 😹

    1. 규격이 계속 변경될 경우, fly()와 quack() 메소드를 일일이 살펴봐야함 
    2. 새로운 오리가 추가될 때, 무엇을 그냥 가져가고 override할지 신경써야함

## 2-3. fly(), quck() 메소드를 제거하고, 인터페이스로 구현?
Duck 클래스에서 fly() 메소드를 제거하고, Flyable 인터페이스를 만들어 날 수 있는 오리에 대해서만 그 인터페이스를 구현하자!

![](/assets/img/design_pattern/strategy/Untitled-901c0b97-11d4-4528-ad54-3096853cac6e.png)

- 클래스, 인터페이스 정의
    - 실제로는 각 클래스가 다른 파일로 구성되었습니다. 간단히 보기 위해 소스를 통합하였습니다.
{% highlight java %}
    // 1. Duck: 공통적인 swim, display 메소드만 남겼다. 
    public abstract class Duck {
        // 실체가 아닌 공통적인 특성을 모아 놓은 클래스라서 추상 클래스로 구현
    
        public void swim(){
            System.out.println("음파음파~!");
        }
        // 오리마다 모양이 달라서 추상메소드로 구현
        public abstract void display();
    }
    
    //2. Flyable: 인터페이스 구현. fly()는 abstract가 앞에 붙지 않아도 자동 추상 메소드됨
    public interface Flyable {
        public void fly();
    }
    //3. Quackable: 인터페이스 구현 
    public interface Quackable {
        public void quack();
    }
    // 4.MallardDuck Duck  클래스는 상속하고, Flyable, Quackable은 인터페이스 상속 
    public class MallardDuck extends Duck implements Flyable, Quackable  {
        public void display(){
            System.out.println("청둥오리입니다!");
        }
        public void fly() {
            System.out.println("파다파닥!");
        }
        public void quack() {
            System.out.println("꽥꽥~!");
        }
    }
    // 5.RedheadDuck Duck  클래스는 상속하고, Flyable, Quackable은 인터페이스 상속
    public class RedheadDuck extends Duck implements Flyable, Quackable {
        public void display(){
            System.out.println("빨간머리오리입니다!");
        }
        public void fly(){
            System.out.println("파다파닥!");
        }
        public void quack(){
            System.out.println("꽥꽥~!");
        }
    }
    
    // 6.RubberDuck Duck  클래스는 상속하고, Quackable은 인터페이스 상속
    public class RubberDuck extends Duck implements Quackable{
        public void display() {
            System.out.println("러버덕입니다!");
        }
        public void quack(){
            System.out.println("삑삑~!");
        }
    }
    
    // 7.DecoyDuck Duck 클래스만 상속
    public class DecoyDuck extends Duck {
        public void display() {
            System.out.println("나무오리입니다!");
        }
    }
{% endhighlight %}

- main
{% highlight java %}
    public class Main {
    
        public static void main(String[] args) {
    
    		MallardDuck mallarduck = new MallardDuck();
    		RedheadDuck redheadduck = new RedheadDuck();
    		RubberDuck rubberduck = new RubberDuck();
    		DecoyDuck decoyduck = new DecoyDuck();
    		//원하는 객체 메소드 실행하기  
        }
    }
{% endhighlight %}

### -  문제점 😹

    1. 코드 중복: 청둥오리와 빨간머리오리는 꽥소리와 나는 방법이 동일함
    2. 날아가는 동작을 조금씩 바꾸기위해 모든 서브 클래스의 코드를 전부 다 고쳐야함 

## 2-4. 문제없는 오리 프로젝트 만들기 🐧

> 위에서 발생한 문제가 없는 프로젝트를 만들기 위해 아래의 디자인 원칙들을 지켜야 합니다.
{% highlight text %}
 애플리케이션에서 바뀌는 부분은 따로 뽑아 캡슐화시킨다. 그러면 나중에 바뀌지 않는 부분에는 영향을 주지 않고 그 부분만 고치거나 확장 가능하다.( 캡슐화) 
{% endhighlight %}
- 그러면 이 프로젝트에서는 바뀌는 부분은 fly(), quack() 입니다. 따라서 이 메소드는 Duck 클래스로 부터 분리하여 각 행동을 나타낼 클래스 집합을 새로 만듭시다.(캡슐화)

> 그럼 나는 행동과 꽥꽥거리는 행동을 구현하는 클래스 집합은 어떤 식으로 디자인 해야할까요?
{% highlight text %}
구현이 아닌 인터페이스에 맞춰 프로그래밍 한다.  (다형성) 
{% endhighlight %}
- 이전에는 Duck 클래스나 Duck 서브 클래스에 직접 구현하여 바뀌는 행동은 Duck 클래스에 의존하였다.  반면, 인터페이스로 구현하면, Duck 클래스는 해당 행동을 구현하는 방법을 알 필요도 없고, 의존적이지 않게된다.
- "인터페이스에 맞춰 프로그래밍한다."의 의미는 상위 형식에 맞춰 프로그래밍하여 다형성을 활용하자!

### - 해당 디자인 원칙들로 구현된 다이어그램 결과

![](/assets/img/design_pattern/strategy/Untitled-2c7880bb-1506-4dbf-ac6a-f0df14d24889.png)

### - 스트래티지 패턴이란? (위 그림을 보면서 다시 이해하세요)

변경되는 행동들을 각각을 `캡슐화`하여 교환해서 사용할 수 있도록 만든다. 여기서 스트래티지을 활용하면 클라이언트(변경되지 않는 것)와는 독립적으로  변경되는 행동들을 변경할 수 있다.  + 캡슐화된 행동은 상위 형식에 맞춰 개발합니다(`다형성`)

### 해당 소스 코드

- 클래스, 인터페이스 정의
    - 실제로는 각 클래스가 다른 파일로 구성되었습니다. 간단히 보기 위해 소스를 통합하였습니다.
    
{% highlight java %}
    // 1. Duck: FlyBehavior, QuackBehavior 인터페이스를 변수로 지정 
    public abstract class Duck {
        FlyBehavior flyBehavior;
        QuackBehavior quackBehavior;
    
        // performFly, performQuack:  각자 오리 형태에 맞게 행동함.
        public void performFly(){
            flyBehavior.fly();
        }
        public void performQuack(){
            quackBehavior.quack();
        }
    
       // setFlyBehavior,setQuackBehavior:  동적으로 나는 행동과 소리내는 행동을 지정하기 위한 메소드들
       // > 실행 후, 변경 가능하도록함 
        public void setFlyBehavior(FlyBehavior flyBehavior){
            this.flyBehavior = flyBehavior;
        }
        public void setQuackBehavior(QuackBehavior quackBehavior){
            this.quackBehavior = quackBehavior;
        }
    
        // display(): 오리마다 모양이 달라서 추상메소드로 구현
        public abstract void display();
    
        // swim(): 모든 오리의 공통 메소드
        public void swim(){
            System.out.println("음파음파~!");
        }
    }
    
    // 1-1. MallardDuck: Duck 서브 클래스 
    public class MallardDuck extends Duck {
        // 생성자 메소드: 해당되는 행위 인터페이스 정의 
        public MallardDuck(){
            quackBehavior = new Quack();
            flyBehavior = new FlyWithWings();
        }
    		@Override
        public void display(){
            System.out.println("청둥오리입니다!");
        }
    }
    
    // 1-2. ModelDuck: Duck 서브 클래스 2 - 모형 오리 
    public class ModelDuck extends Duck {
        public ModelDuck(){
            flyBehavior = new FlyNoWay();
            quackBehavior = new Quack();
        }
        @Override
        public void display() {
            System.out.println("로켓 추진으로 날아갑니다~!");
        }
    }
    
    
    // 2. FlyBehavior: interface 정의 
    public interface FlyBehavior {
        public void fly();
    }
    // 2-1. FlyWithWings: FlyBehavior 인터페이스 구현  
    public class FlyWithWings implements FlyBehavior {
        public void fly(){
            System.out.println("날고있어요!");
        }
    }
    ....( 나머지 나는 행동 생략) 
    
    
    // 3. QuackBehavior: interface로 정의
    public interface QuackBehavior {
        public void quack();
    }
    
    // 3-1. Quack: QuackBehavior 인터페이스 구현
    public class Quack implements QuackBehavior {
        public void quack(){
            System.out.println("꽥꽥~!");
        }
    }
    ....(나머지 소리내는 행동 생략) 
{% endhighlight %}

- main
    - model 객체를 보면 생성한 이후에도 setFlyBehavior() 메소드로 나는 방식을 변경 가능합니다.
{% highlight java %}
    public class Main {
    
        public static void main(String[] args) {
    
    		MallardDuck mallarduck = new MallardDuck();
    		mallarduck.display();       // 청둥오리입니다!
    		mallarduck.performFly();    // 날고있어요!
    		mallarduck.performQuack();  // 꽥꽥~!
    
    		Duck model = new ModelDuck();
    		model.performFly();        // 저는 못날아요!
    		model.setFlyBehavior(new FlyRockerPowerd());
    		model.performFly();       //  로켓 추진으로 날아갑니다!
    
        }
    }
 {% endhighlight %}
---

# 3. 디자인 패턴을 배우는 이유?

    1. 개발자와 의사소통 쉬워짐
    > 개발 로직 회의 시, 디자인에 초점을 맞출 수 있음
---

# 참고

- Head first design patterns 책

---

# 소스 코드 
[**소스코드**](https://github.com/JUNGEEYOU/Strategy_pattern)
