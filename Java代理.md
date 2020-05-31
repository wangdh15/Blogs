---
title: Java代理
date: 2019-07-31 00:22:32
Categories:
	- Java
tags:
	- Java
	- 设计模式	
---

## Java中的代理

### 1. 代理的作用

在写一个功能函数的时候，通常需要加入一些和业务没有直接关系的代码，如日志记录，信息发送，安全和事务支持等。这些枝节性代码虽然是必要的，但是存在以下的问题：

1. 枝节性代码和功能之间是相互独立的，不是函数的目的，是对面向对象思想的破坏
2. 枝节性代码造成功能代码对其他类的依赖，加深类之间的耦合，可重用性降低
3. 从逻辑上来说，枝节性代码应该`监视着功能性代码，然后采取行动，而不是功能性代码通知枝节代码采取行动`

### 2. 代理模式

>  代理(Proxy)是一种设计模式：为其他对象提供一个代理以控制对某个对象的访问，即通过代理对象访问目标对象.这样做的好处是:可以在目标对象实现的基础上,增强额外的功能操作,即扩展目标对象的功能.

编程中的一个思想：**不要随意去修改别人已经写好的代码或者方法,如果需改修改,可以通过代理的方式来扩展该方法**

代理模式的关键点是:**代理对象与目标对象.代理对象是对目标对象的扩展,并会调用目标对象**

### 3. 静态代理

静态代理在使用时,需要定义接口或者父类,被代理对象与代理对象一起实现相同的接口或者是继承相同父类.

**在编译期确定代理对象，在程序运行前代理类的.class文件就已经存在了。**

如：在代理对象中实例化被代理对象或者将被代理对象传入代理对象的构造方法

实例：

模拟保存动作,定义一个保存动作的接口:`IUserDao.java`,然后目标对象`UserDao.java`实现这个接口的方法,此时如果使用静态代理方式,就需要在代理对象(UserDaoProxy.java)中也实现`IUserDao`接口.调用的时候通过调用代理对象的方法来调用目标对象.代码如下：

```java
// IUserDao.java
public interface IUserDao {

    void save();
}

// UserDao.java
public class UserDao implements IUserDao {

    public void save() {
        System.out.println("保存数据。。。。");
    }
}

// UserDaoProxy.java
public class UserDaoProxy implements IUserDao {
		// 接受保存目标对象
    private IUserDao userDao;


    public UserDaoProxy(IUserDao userDao){
        this.userDao = userDao;
    }

    public void save() {
      // 对目标对象功能进行增强
        System.out.println("代理对象开始。。。");
        userDao.save();// 调用目标对象方法
        System.out.println("代理对象结束");
    }
}

// Test.java
public void staticProxyTest() {
  IUserDao userDao = new UserDao();
  UserDaoProxy userDaoProxy = new UserDaoProxy(userDao);
  userDaoProxy.save();
}
```

#### 静态代理的优缺点：

1. 可以做到在不修改目标对象的功能前提下,对目标功能扩展.
2. 代理类和委托类实现相同的接口，同时要实现相同的方法。这样就出现了大量的代码重复。如果接口增加一个方法，除了所有实现类需要实现这个方法外，所有代理类也需要实现此方法。增加了代码维护的复杂度。

### 4. 动态代理

> 动态代理的特点：
>
> 1. 在**运行期**，通过反射机制创建一个实现了一组给定接口的新类
> 2. 在运行时生成的class，必须提供一组interface给它，然后该class就宣称它实现了这些 interface。该class的实 例可以当作这些interface中的任何一个来用。但是这个Dynamic Proxy其实就是一个Proxy， 它不会替你作实质性的工作，在生成它的实例时你必须提供一个handler，由它接管实际的工 作。
> 3. 动态代理也叫做:JDK代理,接口代理
> 4. 接口中声明的所有方法都被转移到调用处理器一个集中的方法中处理（InvocationHandler.invoke）。这样，在接口方法数量比较多的时候，我们可以进行灵活处理，而不需要像静态代理那样每一个方法进行中转。而且动态代理的应用使我们的类职责更加单一，复用性更强

#### JDK中生成代理对象的API：

代理类所在的包是:`java.lang.reflect.Proxy`

JDK实现代理只需要使用newProxyInstance方法,但是该方法需要接收三个参数,完整的写法是:

```java
static Object newProxyInstance(ClassLoader loader, Class [] interfaces, InvocationHandler handler)
```

方法为静态方法，三个参数为：

1. ClassLoader：指定当前目标对象使用类加载器,用null表示默认类加载器
2. Class[] interfaces:需要实现的接口数组
3. InvocationHandler handler:调用处理器,执行目标对象的方法时,会触发调用处理器的方法,从而把当前执行目标对象的方法作为参数传入

`java.lang.reflect.InvocationHandler`：这是调用处理器接口，它自定义了一个 `invoke `方法，用于集中处理在动态代理类对象上的方法调用，通常在该方法中实现对委托类的代理访问。其原型是：

```java
// 该方法负责集中处理动态代理类上的所有方法调用。第一个参数既是代理类实例，第二个参数是被调用的方法对象
// 第三个方法是调用参数。
Object invoke(Object proxy, Method method, Object[] args)
```

实例：

```java
// IUserDao.java、UserDao.java和上述定义不变
// ProxyFactory.java 生成代理类的工厂
/*
对任意一个类的对象生成动态代理对象
*/
public class DynamicProxyFactory {
  
    private  Object target;

    public DynamicProxyFactory(Object target){
        this.target = target;
    }

    public Object getProxyInstance(){
        return Proxy.newProxyInstance(
          // 调用Proxy的静态方法生成目标对象的代理对象
          target.getClass().getClassLoader(), // 目标对象的类加载器
          target.getClass().getInterfaces(), // 目标对象的实现接口数组
                new InvocationHandler(){

                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                        // 可以对执行的方法进行判断，不同方法执行不同类型的增强。
                        /*
                        if(method == XXX){} else{}
                         */
                        System.out.println("代理开始。。");
                        Object returnValue = method.invoke(target, args); // 调用目标对象的方法
                        System.out.println("代理结束。。");
                        return returnValue; // 如果目标对象对应方法有返回值，可以返回
                    }
                }
        );
    }
}

// 测试方法
public void DynamicProxyTest() {
  IUserDao userDao = new UserDao();
  IUserDao dynamicProy = (IUserDao) new DynamicProxyFactory(userDao).getProxyInstance();
  dynamicProy.save();
}
```

#### 总结：

代理对象不需要实现接口,但是**目标对象一定要实现接口**,否则不能用动态代理。

### 5. Cglib代理

上面的静态代理和动态代理模式都是要求目标对象实现一个接口或者多个接口,但是有时候目标对象只是一个单独的对象,并没有实现任何的接口,这个时候就可以使用**构建目标对象子类的方式**实现代理,这种方法就叫做:Cglib代理

Cglib代理,也叫作子类代理,它是在内存中构建一个子类对象从而实现对目标对象功能的扩展.

>- Cglib是一个强大的高性能的代码生成包,它可以在运行期扩展java类与实现java接口.它广泛的被许多AOP的框架使用,例如Spring AOP和synaop,为他们提供方法的interception(拦截)
>- Cglib包的底层是通过使用**字节码处理框架ASM**来转换字节码并生成新的子类.
>- 代理的类不能为final,否则报错；目标对象的方法如果为final/static,那么就不会被拦截,即不会执行目标对象额外的业务方法.

下面是实例

```java
// UserDao.java 没有实现任何接口
public class UserDao2 {

    public void save() {
        System.out.println("保存数据。。。。");
    }
}

// ClassProxyFactory.java 传入目标对象，调用getProxyInstance()方法返回代理对象
public class ClassProxyFactory implements MethodInterceptor {
  // 实现这个接口需要实现方法intercept

    private Object target;
  // 维护目标对象

    public ClassProxyFactory(Object target){
        this.target = target;
    }

    public Object getProxyInstance(){
				// 使用工具类
        Enhancer enhancer = new Enhancer();
      // 设置父类
        enhancer.setSuperclass(target.getClass());
      // 设置回调函数，就是实现了MethodInterceptor接口中的intercept的类，本例就是this
        enhancer.setCallback(this);
      // 创建代理对象并返回
        return enhancer.create();
    }

    public Object intercept(Object obj, Method method, Object[] args, MethodProxy proxy) throws Throwable {
      // MethodInterceptor中定义的方法，在这个函数中对目标对象那个的函数进行增强
        System.out.println("代理开始。。。");
        Object returnValue = method.invoke(target, args); // 调用目标对象中的方法
        System.out.println("代理结束。。。");
        return returnValue;
    }
}

// 测试方法
public void ClassProxyTest() {
  IUserDao userDao = new UserDao();
  IUserDao classProxy = (IUserDao) new ClassProxyFactory(userDao).getProxyInstance();
  classProxy.save();
}
```

#### AOP(面向切面编程)

将日志记录，性能统计，安全控制，事务处理，异常处理等代码从业务逻辑代码中划分出来，通过对这些行为的分离，我们希望可以将它们独立到非业务逻辑的方法中，进而改变这些行为的时候不影响业务逻辑的代码---解耦。

在Spring的AOP编程中:

- 如果加入容器的目标对象有实现接口,用JDK代理
- 如果目标对象没有实现接口,用Cglib代理

### 6. 代理模式和装饰器模式

都是实现同一个接口，一个类包装另一个类。

>- 装饰器模式：能动态的新增或组合对象的行为
>  在不改变接口的前提下，动态扩展对象的功能
>- 代理模式：为其他对象提供一种代理以控制对这个对象的访问
>  在不改变接口的前提下，控制对象的访问

装饰模式是“新增行为”，而代理模式是“控制访问”。关键就是我们如何判断是“新增行 为”还是“控制访问”。你在一个地方写装饰，大家就知道这是在增加功能，你写代理，大家就知道是在限制。