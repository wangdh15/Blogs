---
title: Java反射机制
date: 2019-07-25 09:16:04
categories:
	- Java
tags:
	- Java
---

## 1. 反射的概念

> Java 反射机制在程序**运行时**，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性。这种 **动态的获取信息** 以及 **动态调用对象的方法** 的功能称为 **java 的反射机制**。
>
> 反射机制很重要的一点就是“运行时”，其使得我们可以在程序运行时加载、探索以及使用编译期间完全未知的 `.class` 文件。换句话说，Java 程序可以加载一个运行时才得知名称的 `.class` 文件，然后获悉其完整构造，并生成其对象实体、或对其 fields（变量）设值、或调用其 methods（方法）。

个人理解：Java反射机制在各大框架中使用得比较多。因为反射机制可以使得在不改变源码、不再次编译的情况下，向外界暴露了借口，通过这个接口设定运行过程中需要加载的`class`文件的全限定名，就可以将自己编写的`class`整合到已有的框架中。修改配置文件就可以实现功能。

和反射相关的包都在`java.lang.reflect`下

## 2. Class对象

任何一个java类字节码在加载到JVM中的时候，会为其创建一个`Class`对象`(源码在java.lang.Class)`。这个对象包含这个类的所有信息。包括`成员变量、构造器、方法等`。可以通过以下三种方式获得这个对象。

### 2.1 Class.forName()

直接使用`Class`类的静态方法`forName()`就可以将字节码文件加载到JVM中，并返回这个类的`Class`对象。

```java
public static Class<?> forName(String className)
```

在进行数据库连接的时候通常利用这种方法加载数据库驱动。

### 2.2 直接获取某个对象的class

```java
Class<?> a = int.class;
Class<?> b = Integer.TYPE;
```

### 2.3 通过调用某个对象的`getCLass（）`方法

```java
Integer a = new Integer(3);
Class<?> b = a.getClass();
```

## 3. 通过反射生成对象

通过反射生成对象主要有两种方式：

1. 使用Class对象的newInstance()方法来创建

```java
Class<String> a = String.class;
String b = a.newInstance();
```

2. 先通过Class对象获取指定的Constructor对象，再调用Constructor对象的newInstance()方法来创建实例。这种方法可以用指定的构造器构造类的实例。

```java
Class<String> a = String.class;        
b = a.getConstructor(String.class).newInstance("fdaf"); // 在获取特定参数的构造方法的时候，将参数的class对象传入即可。
System.out.println(b);
```

## 4. 获取某个类的方法

可以通过一个类的`Class`对象获取到这个类的方法列表。

1. `getDeclaredMethods` 方法返回类或接口声明的所有方法，包括公共、保护、默认（包）访问和私有方法，但不包括继承的方法。

```java
public Method[] getDeclaredMethods() throws SecurityException
```

2. `getMethods` 方法返回某个类的所有公用（public）方法，包括其继承类的公用方法。

```java
public Method[] getMethods() throws SecurityException
```

3. `getMethod`方法返回一个特定的方法，其中第一个参数为方法名称，后面的参数为方法的参数对应Class的对象。

```java
public Method getMethod(String name, Class<?>... parameterTypes)
```

实例：

```java
    public void methodTest() throws NoSuchMethodException {
        Class<String> a = String.class;
        Method[] m = a.getDeclaredMethods();
        for (Method c: m
             ) {
            System.out.println(c);
        }

        System.out.println("-------------------");
        Method[] m1 = a.getMethods();
        for (Method c :
                m) {
            System.out.println(c);
        }
        System.out.println("-------------------");

        Method m2 = a.getMethod("toString", null);
        System.out.println(m2);
    }
```

## 5. 获取构造器信息

通过`getConstructor(Class<?> …)`方法 来获取对应参数的构造器，然后可以通过构造器的`newInstnace()`方法，并传入相对应的参数，来创建一个类的对象。

```java
public T newInstance(Object ... initargs)
```

## 6. 获取成员变量的信息

1. `getFiled`：访问公有的成员变量
2. `getDeclaredField`：所有已声明的成员变量，但不能得到其父类的成员变量

```java
@Test
public void fieldTest() throws NoSuchFieldException {
  Class<Integer> stringClass = Integer.class;
  Field[] f = stringClass.getFields();
  for (Field f1 :
       f) {
    System.out.println(f1);
  }
  Field f2 = stringClass.getField("MAX_VALUE");
  System.out.println(f2);
}
```

## 7. 调用方法

获取到一个类的某个方法之后，我们可以使用这个方法对象和具体的类的对象来调用这个方法。使用方法对象的`invoke()`方法。其原型为：

```java
public Object invoke(Object obj, Object... args)
  throws IllegalAccessException, IllegalArgumentException,
InvocationTargetException
// 第一个参数是这个类的实例
// 第二个参数是这个方法需要的参数
```

实例：

```java
@Test
public void methodRunTest() throws NoSuchMethodException, InvocationTargetException, IllegalAccessException {
  Class<MyAdd> a = MyAdd.class;
  Method m = a.getMethod("add", int.class, int.class);
  MyAdd o = new MyAdd();
  System.out.println(m.invoke(o, 1, 2));
}

class MyAdd{

  public static int add(int a1, int a2){
    return a1+a2;
  }

  public static int sub(int a1, int a2){
    return a1 - a2;
  }
}
```

## 8. 访问私有成员，调用私有方法

利用反射，可以访问到类的私有成员，也可以调用类的私有方法。

在获取到`Field`对象或者`Method`对象之后，可以调用其方法`setAccessible(true)`来使其可以被访问或执行。

在获取`Field`和`Method`需要使用`getDeclaredField`和`getDeclaredMethod`方法，才能够获得私有成员和私有方法。

实例：

```java
@Test
public void fielsAccTest() throws NoSuchFieldException, IllegalAccessException, InstantiationException {
  Class<MyAdd> myAddClass = MyAdd.class;
  Field field = myAddClass.getDeclaredField("a");
  MyAdd add = myAddClass.newInstance();
  field.setAccessible(true);  // 设置访问权限为true
  field.set(add, 3);
  System.out.println(field.get(add));
}

@Test
public void privateTest() throws NoSuchMethodException, InvocationTargetException, IllegalAccessException {
  Class<MyAdd> myAddClass = MyAdd.class;
  Method m = myAddClass.getDeclaredMethod("sub", int.class, int.class);
  m.setAccessible(true);   // 设置访问权限为true
  MyAdd a = new MyAdd();
  System.out.println(m.invoke(a, 2, 1));
}

class MyAdd{

  private int a;

  public static int add(int a1, int a2){
    return a1+a2;
  }

  private static int sub(int a1, int a2){
    return a1 - a2;
  }
}
```



## 9. 创建数组

下面是一个利用反射创建数组的例子：

```java
@Test
public void arrayTest() {
  Class<String> stringClass = String.class;
  Object o = Array.newInstance(stringClass, 10);
  Array.set(o, 0, "123");
  System.out.println(Array.get(o, 0));
}
```

利用发射创建对象会消耗一些系统资源，效率比直接创建低。而且通过反射可以忽略权限检查，访问类中的私有成员或者调用私有方法。









