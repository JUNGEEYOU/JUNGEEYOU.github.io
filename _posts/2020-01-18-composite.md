---
layout: post
title: "9-2. 컴포지트 패턴(Composite Pattern)"
date: 2020-01-18
excerpt: ""
tags: [Composite, Composite Pattern, 컴포지트 패턴, design pattern]
comments: true
design_patterns: true
---


# 👀 학습 목표

- 컴포지트 패턴을 이해한다.
- 컴포지트 패턴 예제를 이해한다.

# 1. 컴포지트 패턴 정의

## 1-1. 컴포지트 패턴이란

> 객체들을 트리 구조로 구성하여 부분과 전체를 나타내는 계층구조로 만들 수 있습니다. 이 패턴을 이용하면 클라이언트에서 개별 객체와 다른 객체들로 구성된 복합 객체를 똑같은 방법을 다룰 수 있습니다.

- **부분과 전체 계층구조**: 폴더-파일, 디저트메뉴-초코케이크 등

![9%202/Untitled.png](/assets/img/design_pattern/composite/Untitled.png)

# 2. 컴포지트 패턴 적용하기

## 2-1. 객체마을 식당에서 메뉴 안에 들어가는 서브메뉴를 만들어 달라고 한다...

> 디저트 메뉴가 있고, 그 아래에 서브메뉴를 넣고 싶다고 합니다.  기존에는 단순히 메뉴만 관리해서 넣어서 리팩토링이 필요해 졌습니다.

![9%202/Untitled%201.png](/assets/img/design_pattern/composite/Untitled%201.png)

- MenuComponent
    - Component로 복합노드(폴더) + 잎 노드(파일) 정의 인터페이스
{% highlight java %}
    // MenuComponent: Component로 복합노드(폴더) + 잎 노드(파일) 정의 인터페이스 
    public abstract class MenuComponent {
        public void add(MenuComponent menuComponent){
            throw new UnsupportedOperationException();
        }
        public void remove(MenuComponent menuComponent){
            throw new UnsupportedOperationException();
        }
        public MenuComponent getChild(int i){
            throw new UnsupportedOperationException();
        }
        public String getName(){
            throw new UnsupportedOperationException();
        }
        public String getDescription(){
            throw new UnsupportedOperationException();
        }
        public double getPrice(){
            throw new UnsupportedOperationException();
        }
        public boolean isVegetarian() {
            throw new UnsupportedOperationException();
        }
        public void print() {
            throw new UnsupportedOperationException();
        }
    }
{% endhighlight %}
- Menu
    - 복합 객체 클래스
{% highlight java %}
    
    import java.util.Iterator;
    import java.util.ArrayList;
    
    // Menu: 복합 객체 클래스
    public class Menu extends MenuComponent {
        ArrayList menuComponents = new ArrayList();
        String name;
        String description;
    
        public Menu(String name, String description) {
            this.name = name;
            this.description = description;
        }
    
        public void add(MenuComponent menuComponent) {
            menuComponents.add(menuComponent);
        }
    
        public void remove(MenuComponent menuComponent) {
            menuComponents.remove(menuComponent);
        }
    
        public MenuComponent getChild(int i) {
            return (MenuComponent)menuComponents.get(i);
        }
    
        public String getName() {
            return name;
        }
    
        public String getDescription() {
            return description;
        }
    
        public void print() {
            System.out.print("\n" + getName());
            System.out.println(", " + getDescription());
            System.out.println("---------------------");
    
            Iterator iterator = menuComponents.iterator();   // 서브메뉴들을 출력하기 위함
            while (iterator.hasNext()) {
                MenuComponent menuComponent =
                        (MenuComponent)iterator.next();
                menuComponent.print();
            }
        }
    }
{% endhighlight %}
- MenuItem
    - leaf 클래스로 예를들면, 파일에 해당
{% highlight java %}
    // MenuItem: leaf 클래스로 예를들면, 파일에 해당
    public class MenuItem extends MenuComponent {
        String name;
        String description;
        boolean vegetarian;
        double price;
    
        public MenuItem(String name,
                        String description,
                        boolean vegetarian,
                        double price)
        {
            this.name = name;
            this.description = description;
            this.vegetarian = vegetarian;
            this.price = price;
        }
        public String getName() {
            return name;
        }
    
        public String getDescription() {
            return description;
        }
    
        public double getPrice() {
            return price;
        }
    
        public boolean isVegetarian() {
            return vegetarian;
        }
    
        public void print() {
            System.out.print("  " + getName());
            if (isVegetarian()) {
                System.out.print("(v)");
            }
            System.out.println(", " + getPrice());
            System.out.println("     -- " + getDescription());
        }
    }
{% endhighlight %}
- Waitress
    - 클라이언트로 MenuComponent를 사용
{% highlight java %}
    // Waitress: 클라이언트로 MenuComponent를 사용
    public class Waitress {
        MenuComponent allMenus;
    
        public Waitress(MenuComponent allMenus) {   // 최상위 메뉴만 전달 받음!!
            this.allMenus = allMenus;
        }
    
        public void printMenu() {
            allMenus.print();
        }
    }
{% endhighlight %}
- MenuTestDrive
    - 아래와 같은 구성으로 메뉴를 구성

![9%202/Untitled%202.png](/assets/img/design_pattern/composite/Untitled%202.png)

head first design patterns 책 이미지 참고 
{% highlight java %}
    package composite_basic;
    
    public class MenuTestDrive {
    
        public static void main(String[] args) {
            // Menu는 복합 노드, MenuItem는 leaf 노드
            // 1. 기본 메뉴 추가 : 팬케이크메뉴, 카페메뉴,
            MenuComponent pancakeHouseMenu =
                    new Menu("팬케이크 하우스 메뉴", "아침 메뉴");
            MenuComponent dinerMenu =
                    new Menu("객체마을 식당 메뉴", "점심 메뉴");
            MenuComponent cafeMenu =
                    new Menu("카페 메뉴", "저녁 메뉴");
            MenuComponent dessertMenu =
                    new Menu("디저트 메뉴 ", "디저트를 즐기세요!");
    
            MenuComponent allMenus = new Menu("전체 메뉴", "전체 메뉴");
    
            // 2. 복합 객체의 add를 이용하여 각 메뉴들을 추가
            allMenus.add(pancakeHouseMenu);
            allMenus.add(dinerMenu);
            allMenus.add(cafeMenu);
    
            // 2. 각 메뉴 항목을 추가
            pancakeHouseMenu.add(new MenuItem(
                    "K&B's Pancake Breakfast",
                    "Pancakes with scrambled eggs, and toast",
                    true,
                    2.99));
            pancakeHouseMenu.add(new MenuItem(
                    "Regular Pancake Breakfast",
                    "Pancakes with fried eggs, sausage",
                    false,
                    2.99));
            pancakeHouseMenu.add(new MenuItem(
                    "Blueberry Pancakes",
                    "Pancakes made with fresh blueberries, and blueberry syrup",
                    true,
                    3.49));
            pancakeHouseMenu.add(new MenuItem(
                    "Waffles",
                    "Waffles, with your choice of blueberries or strawberries",
                    true,
                    3.59));
    
            dinerMenu.add(new MenuItem(
                    "Vegetarian BLT",
                    "(Fakin') Bacon with lettuce & tomato on whole wheat",
                    true,
                    2.99));
            dinerMenu.add(new MenuItem(
                    "BLT",
                    "Bacon with lettuce & tomato on whole wheat",
                    false,
                    2.99));
            dinerMenu.add(new MenuItem(
                    "Soup of the day",
                    "A bowl of the soup of the day, with a side of potato salad",
                    false,
                    3.29));
            dinerMenu.add(new MenuItem(
                    "Hotdog",
                    "A hot dog, with saurkraut, relish, onions, topped with cheese",
                    false,
                    3.05));
            dinerMenu.add(new MenuItem(
                    "Steamed Veggies and Brown Rice",
                    "Steamed vegetables over brown rice",
                    true,
                    3.99));
    
            dinerMenu.add(new MenuItem(
                    "Pasta",
                    "Spaghetti with Marinara Sauce, and a slice of sourdough bread",
                    true,
                    3.89));
    
            dinerMenu.add(dessertMenu);
    
            dessertMenu.add(new MenuItem(
                    "Apple Pie",
                    "Apple pie with a flakey crust, topped with vanilla icecream",
                    true,
                    1.59));
    
            dessertMenu.add(new MenuItem(
                    "Cheesecake",
                    "Creamy New York cheesecake, with a chocolate graham crust",
                    true,
                    1.99));
            dessertMenu.add(new MenuItem(
                    "Sorbet",
                    "A scoop of raspberry and a scoop of lime",
                    true,
                    1.89));
    
            // 웨이터에게 최상위 메뉴를 전달
            Waitress waitress = new Waitress(allMenus);
            // 웨이터가 모든 메뉴를 출력
            waitress.printMenu();
        }
    
    }
{% endhighlight %}
## 2-2. 두가지 역할을 하는 MenuComponent

> 원래 한 클래스에서 한 역할만 맡아야 한다. 그런데 컴포지트 패턴에서는 단일 역할 원칙을 깨고, 대신에 투명성을 확보하기 위한 패턴이다.

- MenuComponent은 복합 노드, 잎 노드의 역할 모두 한다.
- 투명성: 클라이언트가 복합 객체와 잎 노드를 똑같은 방식으로 처리할 수 있도록 하는 것이다.

---

# 참고

- Head first design patterns 책

---

# 소스 코드

- [https://github.com/JUNGEEYOU/Adapter-Pattern](https://github.com/JUNGEEYOU/Adapter-Pattern)
