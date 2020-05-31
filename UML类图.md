---
title: UML类图
date: 2019-08-01 11:29:24
categories:
	- Java
tags:
	- Java	
	- 设计模式
---

## UML类图

> UML(Unified Modeling Language)统一建模语言。是一种开放的方法，用于说明、可视化、构建和编写一个正在开发的、面向对象的、软件密集系统的制品的开放方法。UML展现了一系列最佳工程实践，这些最佳实践在对大规模，复杂系统进行建模方面，特别是在软件架构层次已经被验证有效。

类图是软件工程的统一建模语言一种静态结构图，该图描述了系统的类集合，类的属性和类之间的关系。帮助人们简化对系统的理解，它是系统分析和设计阶段的重要产物，也是系统编码和测试的重要模型依据。

### 1. 类的UML图示

在UML类图中，类使用包含类名，属性，方法名及其参数并且用分割线分隔的长方形表示。如下所示：

```java
// 定义一个Person类，其中包含那么name和age属性，对应的get、set方法
public class Person {

    private String name = null;
    private int age = 0;

    public String getName() {
        return name;
    }

    public void setName(String name = null) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age = 0) {
        this.age = age;
    }
}
```

上述类对应的UML图如下所示：

![image-20190801221743497](/Users/wangdh/Files/blogs/source/_posts/UML类图/1.png)

类名由三部分组成：类名、属性、方法

### 2. 类之间的关系

在UML类图中类与类之间存在多种关系，如

- 泛化(Generalization)关系
- 实现（Realization)关系
- 依赖(Dependence)关系
- 关联（Association）关系
- 聚合(Aggregation)关系
- 组合(Composition)关系

#### 2.1 泛化关系

泛化关系也就是Java中的继承，类和类，接口和接口都可以是继承关系，父类又称作基类或超类，子类又称作派生类，类继承父类后可以实现父类的所以功能，并能拥有父类没有的功能。在UML中，泛化关系可以用带空心三角形的直线来表示。建立两个类Teacher和Student，使其继承自Person。如下所示：

```java
public class Teacher extends Person {

    private int sId = 0;

    public int getsId() {
        return sId;
    }

    public void setsId(int sId) {
        this.sId = sId;
    }

    public void teaching(){
        System.out.println("正在上课。。。。");
    }
}

public class Student extends Person {

    private int sId = 0;

    public int getsId() {
        return sId;
    }

    public void setsId(int sId) {
        this.sId = sId;
    }

    public void study(){
        System.out.println("正在学习");
    }
}
```

上述两个类继承自Person类，并有自己新的成员和方法，则UML图(学生的和老师类似)如下所示:

![image-20190801223218018](/Users/wangdh/Files/blogs/source/_posts/UML类图/8.png)

#### 2.2 实现关系

实现关系在java中就是一个类和接口之间的关系，接口中一般是没有成员变量，所有操作都是抽象的（abstract修饰），只有声明没有具体的实现，具体实现需在实现该接口的类中。在UML中用与类的表示法类似的方式表示接口，区别可在UML中类图中看出。创建一个交通工具类接口IVehicle,并有一个形式速度方法声明travelSpeed。

```java

public interface IVehicle {

    int travelSpeed();
}

public class HighSpeedRail implements IVehicle {

    public int travelSpeed() {
        return 300;
    }
}

public class Bicycle implements IVehicle {

    public int travelSpeed() {
        return 20;
    }
}
```

UML图如下所示：

![image-20190801225058653](/Users/wangdh/Files/blogs/source/_posts/UML类图/2.png)

#### 2.3 依赖关系

依赖关系(Dependency) 是一种使用关系，特定事物的改变有可能会影响到使用该事物的其他事物，在需要表示一个事物使用另一个事物时使用依赖关系。**大多数情况下，依赖关系体现在某个类的方法使用另一个类的对象作为参数。**

在UML中，依赖关系用带箭头的虚线表示，由依赖的一方指向被依赖的一方。

```java
// 老师骑自行车去上学
public class Teacher extends Person {

    private int sId = 0;

    public int getsId() {
        return sId;
    }

    public void setsId(int sId) {
        this.sId = sId;
    }

    public void teaching(){
        System.out.println("正在上课。。。。");
    }

    public int moveSpeed(Bicycle bicycle){
        return bicycle.travelSpeed();
    }
}
```

UML图表示如下：

![image-20190801230921664](/Users/wangdh/Files/blogs/source/_posts/UML类图/4.png)

#### 2.4 关联关系

关联关系表示一个类和另一类有联系，例如在下面的举例中每个Teachers都有个家庭住址与之对应，而此时Teacher和Address就形成了一对一的关联关系

关联关系用**实线箭头**表示。数字表示包含关系：

- 1..1 表示另一个类的一个对象只与该类的一个对象有关系
- 0..* 表示另一个类的一个对象与该类的零个或多个对象有关系
- 1..* 表示另一个类的一个对象与该类的一个或多个对象有关系
- 0..1 表示另一个类的一个对象没有或只与该类的一个对象有关系
- \* 任意多个对象关联

![image-20190801232831640](/Users/wangdh/Files/blogs/source/_posts/UML类图/5.png)

#### 2.5 聚合关系

聚合关系是表示整体与部分的关系，但是部分可以脱离整体而存在。例如一个Teachers对象有一辆汽车Car，此时Car就是Teachers的一部分，但是Car可以脱离Teachers而存在。在UML类中聚合关系用带空心菱形的直线表示。

![image-20190801233637688](/Users/wangdh/Files/blogs/source/_posts/UML类图/6.png)

#### 2.6 组合关系

组合关系(Composition)也表示类之间整体和部分的关系，但是组合关系中部分和整体具有统一的生存期。一旦整体对象不存在，部分对象也将不存在，部分对象与整体对象之间具有同生共死的关系。

在组合关系中，成员类是整体类的一部分，而且整体类可以控制成员类的生命周期，即成员类的存在依赖于整体类。在UML中，组合关系用带实心菱形的直线表示。

如表示人和Figure的关系的时候：

![image-20190801233927431](/Users/wangdh/Files/blogs/source/_posts/UML类图/7.png)