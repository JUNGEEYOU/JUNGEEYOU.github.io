---
layout: post
title: "10. 스테이트 패턴(State Pattern)"
date: 2020-01-19
excerpt: ""
tags: [State, State Pattern, 스테이트 패턴, design pattern]
comments: true
design_patterns: true
---
# 👀 학습 목표

- 스테이트 패턴을 이해한다.
    - **스트래티지 패턴**: 바꿔 쓸 수 있는 행동을 캡슐화한 다음, 실제 행동은 다른 객체에 위임합니다.
    - **스테이트 패턴:** 상태를 기반으로 하는 행동을 캡슐화하고 행동을 현재 상태한테 위임합니다.
- 스테이트 패턴 예제를 이해한다.

# 1. 스테이트 패턴 정의

## 1-1. 스테이트  패턴이란

> 객체의 내부 상태가 바뀜에 따라서 객체의 행동을 바꿀 수 있다. 마치 객체의 클래스가 바뀌는 것과 같은 결과를 얻을 수 있다.

![10/Untitled.png](/assets/img/design_pattern/state/Untitled.png)
sourcemaking 이미지 참고 

---

# 2. 스테이트 패턴 적용하기

## 2-1. 주식회사 왕뽑기 요청

> 아래와 같이 주식회사 왕 뽑기를 개발해 달라는 요청이 왔습니다.  o 는 상태를 의미하고, → 는 다른 상태로 넘어가기 위한 행동입니다.

![10/Untitled%201.png](/assets/img/design_pattern/state/Untitled%201.png)
Head first design patterns 이미지 


## 2-2. 상태 기계의 기초

> 앞에 다이어그램을 가지고 상태 기계를 구현하는 방법에 대해 살펴보자.

1. **4개의 상태**: 동전 있음, 동전 없음, 알맹이 매진, 알맹이 판매 
2. 현재 상태를 저장하기 위한 인스턴스 변수를 만들고 각 상태의 값을 정의 
    {% highlight java %}
        public class GumballMachine {
            // 1. 각 상태를 정의 
            final static int SOLD_OUT = 0;     // 알맹이 매진 
            final static int NO_QUARTER = 1;   // 동전 없음 
            final static int HAS_QUARTER = 2;  // 동전 있음
            final static int SOLD = 3;         // 알맹이 판매 
        
            int state = SOLD_OUT;              // 현재 상태 저장 
    {% endhighlight %}

3. 이 시스템에서 있을 수 있는 모든 행동을 모아 본다. 
     - 동전 투입, 동전 반환, 손잡이 돌림, 알맹이 내보냄
     - insertQuarter, ejectQuarter, turnCrank, dispense
4. 각 행동을 구현할 때, 조건문을 써서 상태별로 어떤 작업을 할지 결정합니다. 

### 상태 기계 기초를 바탕으로 코드 작성
- GumballMachine 클래스 정의
{% highlight java %}
    public class GumballMachine {
        // 1. 각 상태를 정의 
        final static int SOLD_OUT = 0;     // 알맹이 매진 
        final static int NO_QUARTER = 1;   // 동전 없음 
        final static int HAS_QUARTER = 2;  // 동전 있음
        final static int SOLD = 3;         // 알맹이 판매 
    
        int state = SOLD_OUT;              // 현재 상태 저장 
        int count = 0;                     // 기계에 들어 있는 알맹이의 개수 저장 
    
        public GumballMachine(int count) {
            this.count = count;
            if (count > 0) {
                state = NO_QUARTER;      // 동전을 넣기 기다리는 상태 
            }
        }
        // insertQuarter(): 동전을 사용자가 넣었을 경우, 각 상태 별로 다른 행동 진행 
        public void insertQuarter() {
            if (state == HAS_QUARTER) {
                System.out.println("You can't insert another quarter");
            } else if (state == NO_QUARTER) {
                state = HAS_QUARTER;
                System.out.println("You inserted a quarter");
            } else if (state == SOLD_OUT) {
                System.out.println("You can't insert a quarter, the machine is sold out");
            } else if (state == SOLD) {
                System.out.println("Please wait, we're already giving you a gumball");
            }
        }
    
        // ejectQuarter(): 사용자가 동전을 반환 하려는 경우 
        public void ejectQuarter() {
            if (state == HAS_QUARTER) {
                System.out.println("Quarter returned");
                state = NO_QUARTER;
            } else if (state == NO_QUARTER) {
                System.out.println("You haven't inserted a quarter");
            } else if (state == SOLD) {
                System.out.println("Sorry, you already turned the crank");
            } else if (state == SOLD_OUT) {
                System.out.println("You can't eject, you haven't inserted a quarter yet");
            }
        }
       // turnCrank(): 손잡이를 돌리는 경우 
        public void turnCrank() {
            if (state == SOLD) {
                System.out.println("Turning twice doesn't get you another gumball!");
            } else if (state == NO_QUARTER) {
                System.out.println("You turned but there's no quarter");
            } else if (state == SOLD_OUT) {
                System.out.println("You turned, but there are no gumballs");
            } else if (state == HAS_QUARTER) {
                System.out.println("You turned...");
                state = SOLD;
                dispense();
            }
        }
        // dispense(): 알맹이를 내보내는 경우 
        private void dispense() {
            if (state == SOLD) {
                System.out.println("A gumball comes rolling out the slot");
                count = count - 1;
                if (count == 0) {
                    System.out.println("Oops, out of gumballs!");
                    state = SOLD_OUT;
                } else {
                    state = NO_QUARTER;
                }
            } else if (state == NO_QUARTER) {
                System.out.println("You need to pay first");
            } else if (state == SOLD_OUT) {
                System.out.println("No gumball dispensed");
            } else if (state == HAS_QUARTER) {
                System.out.println("No gumball dispensed");
            }
        }
        
    }
{% endhighlight %}
- TEST
{% highlight java %}
    public class Main {
    
        public static void main(String[] args) {
            GumballMachine gumballMachine = new GumballMachine(5);
    
            System.out.println(gumballMachine);
    
            gumballMachine.insertQuarter();
            gumballMachine.turnCrank();
    
            System.out.println(gumballMachine);
    
            gumballMachine.insertQuarter();
            gumballMachine.ejectQuarter();
            gumballMachine.turnCrank();
    
            System.out.println(gumballMachine);
    
            gumballMachine.insertQuarter();
            gumballMachine.turnCrank();
            gumballMachine.insertQuarter();
            gumballMachine.turnCrank();
            gumballMachine.ejectQuarter();
    
            System.out.println(gumballMachine);
    
            gumballMachine.insertQuarter();
            gumballMachine.insertQuarter();
            gumballMachine.turnCrank();
            gumballMachine.insertQuarter();
            gumballMachine.turnCrank();
            gumballMachine.insertQuarter();
            gumballMachine.turnCrank();
    
            System.out.println(gumballMachine);
        }
    }
{% endhighlight %}
## 2-3. 변경 요청이 들어 왔어요.
> 10분의 1의 확률로 공짜 알맹이를 받도록 변경해 달라고 합니다.( 총 2개 알맹이가 나오도록)  다이어그램은 아래와 같습니다.

![10/Untitled%202.png](/assets/img/design_pattern/state/Untitled%202.png)
Head first design patterns 이미지 

### 만약 현재 코드에 상태를 한 개 더 추가한다면...?
- 모든 메소드에 해당 상태에 대한 조건문을 추가해야 합니다. 이런 식으로 개발하면 기존 코드에 없던 새로운 버그가 생길 수도 있습니다.

## 2-4. 새로운 디자인으로 적용하기

### 새로운 디자인 적용하기(스테이트 패턴 적용하기)

> 상태 객체들을 별도의 코드에 집어넣고 어떤 행동이 일어나면 현재 객체에서 필요한 작업을 처리하도록 변경합시다. 그러면 새로운 상태가 추가되도 새로운 상태 클래스만 구현하면 되기 때문에 기존 코드는 영향이 없습니다.  계획은 아래와 같습니다.
{% highlight text %}
    1. State 인터페이스를 만들어 뽑기 기계의 모든 행동 메소드 정의한다. 
    
    2. 기계의 모든 상태에 대해서 상태 클래스를 구현. 기계가 어떤 상태가 있다면, 그 상태에 대한 책임은 해당 상태 클래스가 지게 된다.
    
    3. 조건문 코드는 모두 없애고 상태 클래스에서 모든 작업을 위임한다.
{% endhighlight %}
### state 인터페이스 및 클래스 정의

![10/Untitled%203.png](/assets/img/design_pattern/state/Untitled%203.png)

- State 인터페이스 정의
{% highlight java %}
    // State: 상태 인터페이스
    public interface State {
        public void insertQuarter();
        public void ejectQuarter();
        public void turnCrank();
        public void dispense();
    }
{% endhighlight %}
- NoQuarterState 클래스 정의
{% highlight java %}
    // NoQuarterState: 동전 없음 상태를 구현
    public class NoQuarterState implements State {
        GumballMachine gumballMachine;
    
        public NoQuarterState(GumballMachine gumballMachine) {
            this.gumballMachine = gumballMachine;
        }
    
        public void insertQuarter() {
            System.out.println("You inserted a quarter");
            gumballMachine.setState(gumballMachine.getHasQuarterState());  // 상태를 동전 있음으로 변경
        }
    
        public void ejectQuarter() {
            System.out.println("You haven't inserted a quarter");
        }
    
        public void turnCrank() {
            System.out.println("You turned, but there's no quarter");
        }
    
        public void dispense() {
            System.out.println("You need to pay first");
        }
    }
{% endhighlight %}
- HasQuarterState 클래스 정의
{% highlight java %}
    // HasQuarterState: 동전 있는 상태
    public class HasQuarterState implements State {
        GumballMachine gumballMachine;
    
        public HasQuarterState(GumballMachine gumballMachine) {
            this.gumballMachine = gumballMachine;
        }
    
        public void insertQuarter() {
            System.out.println("You can't insert another quarter");
        }
    
        public void ejectQuarter() {
            System.out.println("Quarter returned");
            gumballMachine.setState(gumballMachine.getNoQuarterState());  // 동전을 돌려주고 동전상태를 변경
        }
    
        public void turnCrank() {
            System.out.println("You turned...");
            gumballMachine.setState(gumballMachine.getSoldState());  // 손잡이 돌리고, 판매 상태로 변경
        }
    
        public void dispense() {
            System.out.println("No gumball dispensed");
        }
    }
{% endhighlight %}
- SoldState 클래스 정의
    - dispense(): 알맹이 내보내고, 알맹이 수에 따른 상태 변경
{% highlight java %}
    // SoldState: 알맹이 판매 상태 정의
    public class SoldState implements State {
    
        GumballMachine gumballMachine;
    
        public SoldState(GumballMachine gumballMachine) {
            this.gumballMachine = gumballMachine;
        }
    
        public void insertQuarter() {
            System.out.println("Please wait, we're already giving you a gumball");
        }
    
        public void ejectQuarter() {
            System.out.println("Sorry, you already turned the crank");
        }
    
        public void turnCrank() {
            System.out.println("Turning twice doesn't get you another gumball!");
        }
    		// dispense(): 알맹이 내보내고, 알맹이 수에 따른 상태 변경 
        public void dispense() {
            gumballMachine.releaseBall();   // 알맹이 한 개 주고, 알맹이 수 -1 
            if (gumballMachine.getCount() > 0) {
                gumballMachine.setState(gumballMachine.getNoQuarterState());
            } else {
                System.out.println("Oops, out of gumballs!");
                gumballMachine.setState(gumballMachine.getSoldOutState());
            }
        }
    }
{% endhighlight %}
- SoldOutState 클래스 정의
{% highlight java %}
    public class SoldOutState implements State {
        GumballMachine gumballMachine;
    
        public SoldOutState(GumballMachine gumballMachine) {
            this.gumballMachine = gumballMachine;
        }
    
        public void insertQuarter() {
            System.out.println("You can't insert a quarter, the machine is sold out");
        }
    
        public void ejectQuarter() {
            System.out.println("You can't eject, you haven't inserted a quarter yet");
        }
    
        public void turnCrank() {
            System.out.println("You turned, but there are no gumballs");
        }
    
        public void dispense() {
            System.out.println("No gumball dispensed");
        }
    }
{% endhighlight %}
- GumballMachine 클래스 변경
{% highlight java %}
    public class GumballMachine {
        // 상태: 정수가 아닌 클래스로 정의
        State soldOutState;
        State noQuarterState;
        State hasQuarterState;
        State soldState;
    
        State state;   //현재 상태 변수
        int count = 0;
    
        public GumballMachine(int numberGumballs) {
            soldOutState = new SoldOutState(this);
            noQuarterState = new NoQuarterState(this);
            hasQuarterState = new HasQuarterState(this);
            soldState = new SoldState(this);
    
            this.count = numberGumballs;
            if (numberGumballs > 0) {
                state = noQuarterState;
            } else {
                state = soldOutState;
            }
        }
    
        public void insertQuarter() {
            state.insertQuarter();
        }
    
        public void ejectQuarter() {
            state.ejectQuarter();
        }
    		// dispense를 따로 정의하지 않은 이유는 사용자는 알맹이 내보내기 메소드를 사용하지 않음
    		// 머신을 돌리면 자동으로 돌맹이가 나옴 
        public void turnCrank() {
            state.turnCrank();
            state.dispense();  
        }
    
        void releaseBall() {
            System.out.println("A gumball comes rolling out the slot...");
            if (count != 0) {
                count = count - 1;
            }
        }
    
        int getCount() {
            return count;
        }
    
        // setState: 다른 객체에서 뽑기 기계의 상태를 변경하기 위한 메소드
        void setState(State state) {
            this.state = state;
        }
    
        public State getState() {
            return state;
        }
    
        public State getSoldOutState() {
            return soldOutState;
        }
    
        public State getNoQuarterState() {
            return noQuarterState;
        }
    
        public State getHasQuarterState() {
            return hasQuarterState;
        }
    
        public State getSoldState() {
            return soldState;
        }
    
    }
{% endhighlight %}
## 2-5. 공짜 알맹이 당첨 기능 추가하기

> WinnerState 클래스를 추가해서 당첨 기능을 넣어 봅니다. SoldState에서 두 번 보내면 되지만, 한 클래스에는 단일 역할만 하도록 해야 합니다.(단일 원칙)

![10/Untitled%204.png](/assets/img/design_pattern/state/Untitled%204.png)

- GumballMachine 클래스 수정
    - winnerState 상태 관련 추가
{% highlight java %}
    public class GumballMachine {
        // 상태: 정수가 아닌 클래스로 정의
        State soldOutState;
        State noQuarterState;
        State hasQuarterState;
        State soldState;
        State winnerState;
    
        State state;   //현재 상태 변수
        int count = 0;
    
        public GumballMachine(int numberGumballs) {
            soldOutState = new SoldOutState(this);
            noQuarterState = new NoQuarterState(this);
            hasQuarterState = new HasQuarterState(this);
            soldState = new SoldState(this);
            winnerState = new WinnerState(this);
    
            this.count = numberGumballs;
            if (numberGumballs > 0) {
                state = noQuarterState;
            } else {
                state = soldOutState;
            }
        }
    
        // 기타 메소드 생략... 
    
        public State getWinnerState(){return winnerState;}
    
    }
{% endhighlight %}
- WinnerState  클래스 추가
{% highlight java %}
    public class WinnerState implements State {
        GumballMachine gumballMachine;
    
        public WinnerState(GumballMachine gumballMachine) {
            this.gumballMachine = gumballMachine;
        }
    
        public void insertQuarter() {
            System.out.println("Please wait, we're already giving you a Gumball");
        }
    
        public void ejectQuarter() {
            System.out.println("Please wait, we're already giving you a Gumball");
        }
    
        public void turnCrank() {
            System.out.println("Turning again doesn't get you another gumball!");
        }
    
        // dispense() 두 번 알맹이 내보내기
        public void dispense() {
            gumballMachine.releaseBall();
            if (gumballMachine.getCount() == 0) {
                gumballMachine.setState(gumballMachine.getSoldOutState());
            } else {
                gumballMachine.releaseBall();
                System.out.println("YOU'RE A WINNER! You got two gumballs for your quarter");
                if (gumballMachine.getCount() > 0) {
                    gumballMachine.setState(gumballMachine.getNoQuarterState());
                } else {
                    System.out.println("Oops, out of gumballs!");
                    gumballMachine.setState(gumballMachine.getSoldOutState());
                }
            }
        }
    }
{% endhighlight %}
- HasQuarterState 수정
    - 당첨 기능 넣기 : 난수 변수와 turnCrank() 메소드 수정
{% highlight java %}    
    import java.util.Random;
    
    // HasQuarterState: 동전 있는 상태
    public class HasQuarterState implements State {
        // 난수 발생기 추가 
        Random randomWinner = new Random(System.currentTimeMillis());
        GumballMachine gumballMachine;
    
        public HasQuarterState(GumballMachine gumballMachine) {
            this.gumballMachine = gumballMachine;
        }
    
        public void insertQuarter() {
            System.out.println("You can't insert another quarter");
        }
    
        public void ejectQuarter() {
            System.out.println("Quarter returned");
            gumballMachine.setState(gumballMachine.getNoQuarterState());
        }
    
        public void turnCrank() {
            System.out.println("You turned...");
            int winner = randomWinner.nextInt(10);   // 10분의 1로 당첨 기능 넣기 
            if ((winner == 0) && (gumballMachine.getCount() > 1)) {   // 당첨 상태 
                gumballMachine.setState(gumballMachine.getWinnerState());
            } else {   // 기본 판매 상태 
                gumballMachine.setState(gumballMachine.getSoldState());
            }
        }
    
        public void dispense() {
            System.out.println("No gumball dispensed");
        }
        
    }
{% endhighlight %}
- 테스트
{% highlight java %}
    public class GumballMachineTestDrive {
    
        public static void main(String[] args) {
            GumballMachine gumballMachine =
                    new GumballMachine(10);
    
            System.out.println(gumballMachine);
    
            gumballMachine.insertQuarter();
            gumballMachine.turnCrank();
    
            System.out.println(gumballMachine);
    
            gumballMachine.insertQuarter();
            gumballMachine.turnCrank();
            gumballMachine.insertQuarter();
            gumballMachine.turnCrank();
    
            System.out.println(gumballMachine);
    
        }
    }
{% endhighlight %}
---

# 참고

- Head first design patterns 책

---

# 소스 코드

- [https://github.com/JUNGEEYOU/StatePattern](https://github.com/JUNGEEYOU/StatePattern)
