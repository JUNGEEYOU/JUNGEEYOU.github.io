---
layout: post
title: "2.옵저버 패턴(observer pattern) "
date: 2019-11-06
excerpt: ""
tags: [observer, Observer Pattern, 옵저버 패턴, head first design patterns, design pattern]
comments: true
design_patterns: true
---

# 👀 학습 목표

- 옵저버 패턴을 이해하자.
- 자바 라이브러리를 이용해서 옵저버 패턴을 만들어보자.

---

# 1. 옵저버 패턴이란?

    한 객체(주제)의 상태가 바뀌면 그 객체에 의존하는 다른 객체들(옵저버)한테
    연락이 가고 자동으로 내용이 갱신되는 방식으로 일대다 의존성을 정의한다. 

![](/assets/img/design_pattern/observer/1-ca6df5e6-f5aa-45c3-976f-555808dcd260.png)

예를 들면, 유명한 `펭수(주제)` 인스타그램을 팔로우하면, 모든 `팔로워(옵저버)`는 펭수가 게시물을 올릴때(주제 객체 상태 변경)하면 펭수에 대한 대한 피드를 받을 수 있다.

- 여기서 주제와 옵저버 관계는 `느슨한 결합 관계`이다.
    - ***느슨한 결합관계란?*** 상호작용은 하지만 서로에 대해서는 모른다. 따라서 서로 변경되어도 영향을 주지 않아 유연한 객체지향 시스템을 구현할 수 있다.

---

# 2.  옵저버 패턴 이해
기상 스테이션에서 데이터를 가져오는 weatherData 객체가 존재하고, 우리가 작업해야할 것은  1. 디스플레이 장비에 현재 조건, 기상 통계, 기상 예보를 갱신하면서 보여줘야한다.이 디스플레이스 현재에는 3가지 화면만 있지만, 확장 가능해야한다.  2. 또한, 변경되는 데이터를 api로도 만들어야한다.

- 구현하고자하는 UML은 아래와 같다.

    - ***Subject 인터페이스***: 옵저버 등록, 제거, 옵저버에게 알림 3개의 메소드 존재하며 이는 추상 메소드이다
    - ***WeatherData***: 옵저버 등록, 제거, 알림 메소드 실제 정의, 여기서는 추가적으로 날씨 관련 데이터를 테스트를 위해 정의한 메소드 정의
    - ***Observer 인터페이스***: update 메소드로 Subject가 옵저버에게 데이터 갱신을 알릴 때 사용하는 메소드
    - ***CurrentConditionsDisplay, StatisticsDisplay 등 클래스***: 날씨 관련 변경된 데이터가 필요한 Observer를 구현한 클래스들
    - ***DisplayElement 인터페이스***: 화면 출력을 위한 인터페이스로 변경되는 행동으로 따로 캡슐화 진행

![](/assets/img/design_pattern/observer/Untitled-141e8fe8-dd57-45b4-bbef-396971bd2c41.png)

- 인터페이스 정의
    - 실제로는 각 클래스가 다른 파일로 구성되었습니다. 간단히 보기 위해 소스를 통합하였습니다.
{% highlight java %}
    /* 1. Subject 인터페이스 정의: 옵저버 등록, 제거, 데이터 업데이트 시, 일림   */
    public interface Subject {
        public void registerObserver(Observer o);
        public void removeObserver(Observer o);
        public void notifyObservers();
    }
    
    /* 2. Observer 인터페이스 정의: 데이터를 전달 받아 디스플레이 업데이트   */
    public interface Observer {
        public void update(float temp, float humidity, float pressure);
    }
    
    /* 3. DisplayElement 인터페이스 정의: 화면에 출력하기 위한 행위   */
    public interface DisplayElement {
        public void display();
    }
{% endhighlight %}  
- 클래스 정의
{% highlight java %}
    /* 1. WeatherData: 옵저버 등록 제거 역할, 데이터 변경시, 옵저버에게 알림     */
    public class WeatherData implements Subject {
        private ArrayList observers;
        private float temperature;
        private float humidity;
        private float pressure;
    
        public WeatherData(){
            observers = new ArrayList();
        }
        public void registerObserver(Observer o){
            observers.add(o);
        }
        public void removeObserver(Observer o){
            int i = observers.indexOf(o);
            if(i >= 0 ){
                observers.remove(i);
            }
        }
        public void notifyObservers(){
            for(int i = 0; i < observers.size() ; i++){
                Observer observer = (Observer) observers.get(i);
                observer.update(temperature, humidity, pressure);
            }
        }
        public void measurementsChanged(){
            notifyObservers();
        }
        /* 실제 기상 스테이션이 없기 때문에 테스트를 위한 메소드로 measurementsChanged() 메소드 호출 */
        public void setMeasurements(float temperature, float humidity, float pressure){
            this.temperature = temperature;
            this.humidity = humidity;
            this.pressure = pressure;
            measurementsChanged();
        }
    }

    
    /* 2. CurrentConditionsDisplay: 옵저버 중 하나로, 현재의 조건(온도, 습도, 기압) 출력    */
    public class CurrentConditionsDisplay implements Observer, DisplayElement {
        private float temperature;
        private float humidity;
        private Subject weatherData;
    
        /* CurrentConditionsDisplay: 객체 생성과 동시에 옵저버 등록*/
        public CurrentConditionsDisplay(Subject weatherData) {
    
            this.weatherData = weatherData;
            weatherData.registerObserver(this);  
        }
    		/* update: 추상 메소드, Subject에서 데이터 업데이트 시, 사용할 메소드 */
        public void update(float temperature, float humidity, float pressure) {
            this.temperature = temperature;
            this.humidity = humidity;
            display();
        }
    		/* display: 화면에 노출  */
        public void display() {
            System.out.println("Current conditions: " + temperature
                    + "F degrees and " + humidity + "% humidity");
        }
    }
    
    
    /* 3. CurrentConditionsDisplay: 옵저버 중 하나로, 현재의 조건(온도, 습도, 기압) 출력    */
    public class CurrentConditionsDisplay implements Observer, DisplayElement {
        private float temperature;
        private float humidity;
        private Subject weatherData;
    
        /* CurrentConditionsDisplay: 객체 생성과 동시에 옵저버 등록*/
        public CurrentConditionsDisplay(Subject weatherData) {
    
            this.weatherData = weatherData;
            weatherData.registerObserver(this);  
        }
    		/* update: 추상 메소드, Subject에서 데이터 업데이트 시, 사용할 메소드 */
        public void update(float temperature, float humidity, float pressure) {
            this.temperature = temperature;
            this.humidity = humidity;
            display();
        }
    		/* display: 화면에 노출  */
        public void display() {
            System.out.println("Current conditions: " + temperature
                    + "F degrees and " + humidity + "% humidity");
        }
    }
    
    /* 4. StatisticsDisplay: 기상통계로, 최대온도 최소온도 온도 평균 노출     */
    public class StatisticsDisplay implements Observer, DisplayElement {
        private float maxTemp = 0.0f;
        private float minTemp = 200;
        private float tempSum= 0.0f;
        private int numReadings;
        private WeatherData weatherData;
    
        public StatisticsDisplay(WeatherData weatherData) {
            this.weatherData = weatherData;
            weatherData.registerObserver(this);
        }
        public void update(float temp, float humidity, float pressure) {
            tempSum += temp;
            numReadings++;
    
            if (temp > maxTemp) {
                maxTemp = temp;
            }
    
            if (temp < minTemp) {
                minTemp = temp;
            }
    
            display();
        }
        public void display() {
            System.out.println("Avg/Max/Min temperature = " + (tempSum / numReadings)
                    + "/" + maxTemp + "/" + minTemp);
        }
    }
    ...
{% endhighlight %}  

- Main 정의
{% highlight java %}
    public class Main {
    
        public static void main(String[] args) {
            WeatherData weatherData = new WeatherData();         //Subject 생성 
            CurrentConditionsDisplay currentConditionsDisplay = new CurrentConditionsDisplay(weatherData); //생성과 동시에 옵저버로 등록
            ForecastDisplay forecastDisplay = new ForecastDisplay(weatherData);
            StatisticsDisplay statisticsDisplay = new StatisticsDisplay(weatherData);
    
            weatherData.setMeasurements(80, 65, 4f);  
    				// Current conditions: 80.0F degrees and 65.0% humidity
    				// Forecast: Watch out for cooler, rainy weather
    				// Avg/Max/Min temperature = 80.0/80.0/80.0
            weatherData.setMeasurements(70, 22, 5f);
            weatherData.setMeasurements(60, 11, 6f);
    
        }
    }
{% endhighlight %}
## 요약

    1. Subject 역할: 데이터가 갱신되면, 등록된 옵저버에게 바로 알린다. 옵저버는 한 명이다.
    2. Observer 역할: 등록만 되어 있으면, 데이터가 자동 갱신됨. 

---

# 3. 자바 내장 옵저버 패턴 사용

## 3-1. 자바 내장 옵저버 특징

### 자바 내장 옵저버 라이브러리 사이트

- [https://docs.oracle.com/javase/9/docs/api/java/util/Observable.html](https://docs.oracle.com/javase/9/docs/api/java/util/Observable.html)

### 자바 내장 옵저버 특징

- 푸시(Subject가 옵저버에게 모든 데이터 전달)  풀 방식(옵저버가 Subject에 존재하는 필요한 데이터만 가져감) 모두 존재
- Observable(Subject) 는 인터페이스가 아니라 클래스로 정의되어 상속 받아서 사용해야한다.
- Observable's notifyObservers() 메소드 호출 시,  옵저버의 update()메소드를 호출된다.

### 자바 내장 옵저버 Deprecated

- java 내장 observable, observer 은 Java se 9 버전부터 Deprecated 됨. (위 사이트에서 확인 가능)

> This class and the Observer interface have been deprecated. The event model supported by Observer and Observable is quite limited, the order of notifications delivered by Observable is unspecified, and state changes are not in one-for-one correspondence with notifications. For a richer event model, consider using the java.beans package. For reliable and ordered messaging among threads, consider using one of the concurrent data structures in the java.util.concurrent package. For reactive streams style programming, see the Flow API.

### 자바 내장 옵저버 문제점

- Observable은 클래스라서 Observable에 기능추가와 재사용성에 제약이 생긴다.
- Observable 클래스의 핵심 메소드를 외부에서 호출 못한다. setChanged() 메소드는 protected 라서 서브 클래스만 호출 가능하다.

## 3-2. 자바 내장 옵저버 구현하기 
### 날씨 예제를 내장 자바 옵저버로 구현하기 > pull 방식으로 구현

![](/assets/img/design_pattern/observer/Untitled-88ba689e-3a16-41a8-bed4-05d50b674fbc.png)

- 클래스 정의
{% highlight java %}
    /*1. WeatherData: Observable 상속받아 java 내장  Subject를 구현 */
    public class WeatherData extends Observable {
        private float temperature;
        private float humidity;
        private float pressure;
    
        public WeatherData() { }
    
        public void measurementsChanged() {
            setChanged();
            notifyObservers();
        }
    
        public void setMeasurements(float temperature, float humidity, float pressure) {
            this.temperature = temperature;
            this.humidity = humidity;
            this.pressure = pressure;
    		//push 방식
    		//notifyObservers(temperature);
    
    		// pull 방식 
            measurementsChanged();
        }
    
        public float getTemperature() {
            return temperature;
        }
    
        public float getHumidity() {
            return humidity;
        }
    
        public float getPressure() {
            return pressure;
        }
    }
    
    
    /* 2. CurrentConditionsDisplay: Observer 구현한다.  */
    public class CurrentConditionsDisplay implements Observer, DisplayElement {
        Observable observable;
        private float temperature;
        private float humidity;
    
        public CurrentConditionsDisplay(Observable observable) {
            this.observable = observable;
            observable.addObserver(this);
        }
    
        public void update(Observable obs, Object arg) {
            if (obs instanceof WeatherData) {
                WeatherData weatherData = (WeatherData)obs;
                this.temperature = weatherData.getTemperature();  //pull 방식이라 필요 데이터 가져감 
                this.humidity = weatherData.getHumidity();
                display();
            }
        }
    
        public void display() {
            System.out.println("Current conditions: " + temperature
                    + "F degrees and " + humidity + "% humidity");
        }
    }
    ...
{% endhighlight %}
- main 정의
{% highlight java %}
    public class Main {
    
        public static void main(String[] args) {
            WeatherData weatherData = new WeatherData();
            CurrentConditionsDisplay currentConditions = new CurrentConditionsDisplay(weatherData);
            StatisticsDisplay statisticsDisplay = new StatisticsDisplay(weatherData);
            ForecastDisplay forecastDisplay = new ForecastDisplay(weatherData);
    
            weatherData.setMeasurements(80, 65, 30.4f);
            weatherData.setMeasurements(82, 70, 29.2f);
            weatherData.setMeasurements(78, 90, 29.2f);
    
        }
    }
{% endhighlight %}
---

# 4. JDK에서 옵저버 패턴 쓰는 부분

- 스윙 api  JButton 에서 이벤트를 감지하는 리스너가 옵저버라서 버튼을 누르는 이벤트가 발생할 경우 옵저버(리스너)에게 감지가 된다.
{% highlight java %}
    public class SwingObserverExample {
        JFrame frame;
    
        public static void main(String[] args) {
            SwingObserverExample example = new SwingObserverExample();
            example.go();
        }
    
        public void go() {
            frame = new JFrame();
    
            JButton button = new JButton("Should I do it?");
    
    		// 버튼 리스너(오저버) 추가 
            button.addActionListener(event ->
                    System.out.println("Don't do it, you might regret it!")
            );
            button.addActionListener(event ->
                    System.out.println("Come on, do it!")
            );
            frame.getContentPane().add(BorderLayout.CENTER, button);
    
            // Set frame properties
            frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
            frame.getContentPane().add(BorderLayout.CENTER, button);
            frame.setSize(300, 300);
            frame.setVisible(true);
        }
    }
{% endhighlight %}
---

# 참고

- Head first design patterns 책

---

# 소스 코드 
[**소스코드**](https://github.com/JUNGEEYOU/Observer_Pattern)

