---
title: Java日志、门面模式
date: 2019-08-02 17:11:18
Categories:
	- Java
tags:
	- Java
	- 设计模式
---

## Java日志、门面模式

> 日志就是记录程序的运行轨迹，方便查找关键信息，也方便快速定位解决问题。

> Java 程序员在开发项目时都是依赖 Eclipse/ Idea 等开发工具的 Debug 调试功能来跟踪解决 Bug，在开发环境可以这么做，但项目发布到了测试、生产环境呢？你有可能会说可以使用远程调试，但实际并不能允许让你这么做。

> 日志的作用就是在测试、生产环境没有 Debug 调试工具时开发、测试人员定位问题的手段。日志打得好，就能根据日志的轨迹快速定位并解决线上问题，反之，日志输出不好不能定位到问题不说反而会影响系统的性能。

> 优秀的项目都是能根据日志定位问题的，而不是在线调试

常见的Java日志框架有如下几类：

- ### Logging

- ### Log4j

- ### commons-logging

- ### Slf4j

- ### Logback

### 1. Logging

`java.util.logging.Logger`是JDK自带的日志记录类。

#### 1.1 创建Logger对象

1. `static Logger getLogger(String name) `查找或创建一个logger，如果已经存在，则返回存在的logger
2. `static Logger getLogger(String name, String resourceBundleName) `为指定子系统查找或创建一个 logger.

#### 1.2 Logger级别

按照从高到低的级别如下：

- SEVERE
- WARNING
- INFO
- CONFIG
- FINE
- FINER
- FINEST

此外还有OFF值，代表关闭日志功能。ALL代表启用所有消息的日志功能。

logger默认的级别是INFO，比INFO更低的日志将不显示。

#### 1.3 Handler

Handler 对象从 Logger 中获取日志信息，并将这些信息导出。例如，它可将这些信息写入控制台或文件中，也可以将这些信息发送到网络日志服务中，或将其转发到操作系统日志中。
可通过执行 setLevel(Level.OFF) 来禁用 Handler，并可通过执行适当级别的 setLevel 来重新启用。Handler 类通常使用 LogManager 属性来设置 Handler 的 Filter、Formatter 和 Level 的默认值。

默认的日志方式是xml格式，我们使用日志就是为了能够清晰的看到操作的相关信息而这种格式有点多，乱所以我们要自定义logger的格式。需要用Formatter来定义

#### 1.4 Logger的Formatter

Formatter 为格式化 LogRecords 提供支持。 
一般来说，每个日志记录 Handler 都有关联的 Formatter。Formatter 接受 LogRecord，并将它转换为一个字符串。

LogRecord 对象用于在日志框架和单个日志 Handler 之间传递日志请求。
LogRecord(Level level, String msg) 

#### 1.5实例

```java
public class LogTest2 {

    @Test
    public void TestOne(){
        Logger a = Logger.getLogger("log"); // 获取一个Logger(工厂方法)
        Logger b = Logger.getLogger("log");
        System.out.println(a == b);  // 同名为同一个Logger,单例模式？
        a.setLevel(Level.ALL);
        FileHandler ft = null;
        try {
            ft = new FileHandler("./log.txt", false); // 创建一个输出到文件的Handler，第一个参数为文件路径，第二个为是否追加，默认为false(不追加)
        } catch (IOException e) {
            e.printStackTrace();
        }
        ConsoleHandler ch = new ConsoleHandler(); // 定义控制台输出Handler
        ch.setLevel(Level.ALL); // 设置输出等级，高于这个等级的会输出到对应位置
        ch.setFormatter(new MyFormatter()); // 设置输出格式，传入一个对象
        a.addHandler(ch);   // 将这个Handler添加到对应的Logger上
        ft.setLevel(Level.ALL);
        ft.setFormatter(new MyFormatter()); 
        a.addHandler(ft);
        a.info("fdaf");
        a.finer("fdsa");

        try{
            int i = 1 / 0;
        }catch (Exception e){
            a.log(Level.SEVERE, "除零了");
        }
    }
}

class MyFormatter extends Formatter{

		/**
		重新定义日志的输出格式，默认是XML格式
		*/
    @Override
    public String format(LogRecord record) { // 传入的是代表信息的类，可通过其get方法获取各个属性值。
        return record.getLevel() + "：内容：" + record.getMessage() + "\n";
    }
}
```

### 2. 门面模式

> 门面模式，其核心为**外部与一个子系统的通信必须通过一个统一的外观对象进行，使得子系统更易于使用**。用一张图来表示门面模式的结构为

![](Java日志、门面模式/1.png)

![image-20190802232139297](Java日志、门面功能/2.png)

门面模式的核心为Facade即门面对象，门面对象核心为几个点：

- 知道所有子角色的功能和责任
- 将客户端发来的请求委派到子系统中，没有实际业务逻辑
- 不参与子系统内业务逻辑的实现

**在软件开发领域有这样一句话：计算机科学领域的任何问题都可以通过增加一个间接的中间层来解决。而门面模式就是对于这句话的典型实践**

代码实例:

```java
public class SystemA {
    public void operationA(){
        System.out.println("operation a...");
    }
}

public class SystemB {
    public void operationB() {
        System.out.println("operation b...");
    }
}

public class SystemC {
    public void operationC() {
        System.out.println("operation c...");
    }
}

public class Facade {
    public void wrapOperation() {
        SystemA a = new SystemA();
        a.operationA();
        SystemB b = new SystemB();
        b.operationB();
        SystemC c = new SystemC();
        c.operationC();
    }
}

public class Client {
    public static void main(String[] args) {
        Facade facade = new Facade();
        facade.wrapOperation();
    }
}
```

### 3. self4j

《阿里巴巴Java开发手册》中有这样一句话：

![image-20190802232740095](Java日志、门面模式/3.png)

为了在应用中屏蔽掉底层日志框架的具体实现。这样的话，即使有一天要更换代码的日志框架，只需要修改jar包，最多再改改日志输出相关的配置文件就可以了。这就是解除了应用和日志框架之间的耦合。

> 日志门面就像饭店的服务员，而日志框架就像是后厨的厨师。对于顾客这个应用来说，我到饭店点菜，我只需要告诉服务员我要一盘番茄炒蛋即可，我不关心后厨的所有事情。因为虽然主厨从把这道菜称之为『番茄炒蛋』A厨师换成了把这道菜称之为『西红柿炒鸡蛋』的B厨师。但是，顾客不需要关心，他只要下达『番茄炒蛋』的命令给到服务员，由服务员再去翻译给厨师就可以了。

对于一个设计的全面、完善的日志门面来说，他也应该是天然就兼容了多种日志框架的。所以，底层框架的更换，日志门面几乎不需要改动。

**日志门面的一个比较重要的好处——解耦**

Java简易日志门面（Simple Logging Facade for Java，缩写SLF4J），是一套包装Logging 框架的界面程式，以外观模式实现。可以在软件部署的时候决定要使用的 Logging 框架，目前主要支援的有Java Logging API、Log4j及logback等框架。

**SLF4J其实只是一个门面服务而已，他并不是真正的日志框架，真正的日志的输出相关的实现还是要依赖Log4j、logback等日志框架的。**

**请不要在你的Java代码中出现任何Log4j等日志框架的API的使用，而是应该直接使用SLF4J这种日志门面**

#### 使用方法

```java
@Test
public void testSlf4j() {
  Logger logger = LoggerFactory.getLogger(Object.class);
  logger.error("123");
}
```

在配置依赖的时候，还需要配置具体的日志实现框架：

```xml
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>1.2.17</version>
</dependency>

<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-api</artifactId>
  <version>1.7.26</version>
</dependency>

<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>1.7.26</version>
</dependency>
```

### 4. log4j

Log4j有三个主要的组件：

- Loggers(记录器):日志类别和级别;
- Appenders (输出源):日志要输出的地方;
- Layouts(布局):日志以何种形式输出

#### 4.1 Logger

Loggers组件在此系统中被分为五个级别：DEBUG、INFO、WARN、ERROR和FATAL。这五个级别是有顺序的，DEBUG < INFO < WARN < ERROR < FATAL，分别用来指定这条日志信息的重要程度，明白这一点很重要，
Log4j有一个规则：**只输出级别不低于设定级别的日志信息**，假设Loggers级别设定为INFO，则INFO、WARN、ERROR和FATAL级别的日志信息都会输出，而级别比INFO低的DEBUG则不会输出。

#### 4.2 Appenders

禁用和使用日志请求只是Log4j的基本功能，Log4j日志系统还提供许多强大的功能，比如允许把日志输出到不同的地方，如控制台（Console）、文件（Files）等，可以根据天数或者文件大小产生新的文件，可以以流的形式发送到其它地方等等。
 **常使用的类如下：**

- org.apache.log4j.ConsoleAppender（控制台）
- org.apache.log4j.FileAppender（文件）
- org.apache.log4j.DailyRollingFileAppender（每天产生一个日志文件）
- org.apache.log4j.RollingFileAppender（文件大小到达指定尺寸的时候产生一个新的文件）
- org.apache.log4j.WriterAppender（将日志信息以流格式发送到任意指定的地方）

#### 4.3 Layouts

Log4j可以在Appenders的后面附加Layouts来完成这个功能。
 Layouts提供四种日志输出样式，如根据**HTML样式**、**自由指定样式**、**包含日志级别与信息的样式**和**包含日志时间、线程、类别等信息的样式**。
 **常使用的类如下：**

- org.apache.log4j.HTMLLayout（以HTML表格形式布局）
- org.apache.log4j.PatternLayout（可以灵活地指定布局模式）
- org.apache.log4j.SimpleLayout（包含日志信息的级别和信息字符串）
- org.apache.log4j.TTCCLayout（包含日志产生的时间、线程、类别等信息）

**PatternLayout选项：**

ConversionPattern=%m%n：设定以怎样的格式显示消息。
 **格式化符号说明：**

- %p：输出日志信息的优先级，即DEBUG，INFO，WARN，ERROR，FATAL。
- %d：输出日志时间点的日期或时间，默认格式为ISO8601，也可以在其后指定格式，如：%d{yyyy/MM/dd HH:mm:ss,SSS}。
- %r：输出自应用程序启动到输出该log信息耗费的毫秒数。
- %t：输出产生该日志事件的线程名。
- %l：输出日志事件的发生位置，相当于%c.%M(%F:%L)的组合，包括类全名、方法、文件名以及在代码中的行数。例如：test.TestLog4j.main(TestLog4j.java:10)。
- %c：输出日志信息所属的类目，通常就是所在类的全名。
- %M：输出产生日志信息的方法名。
- %F：输出日志消息产生时所在的文件名称。
- %L:：输出代码中的行号。
- %m:：输出代码中指定的具体日志信息。
- %n：输出一个回车换行符，Windows平台为"\r\n"，Unix平台为"\n"。
- %x：输出和当前线程相关联的NDC(嵌套诊断环境)，尤其用到像java servlets这样的多客户多线程的应用中。
- %%：输出一个"%"字符。

#### 4.4 实例

```properties
### set log levels ###
### 根日志器
### og4j.rootLogger = [ level ] , appenderName1, appenderName2, …
### log4j.additivity.org.apache=false：表示Logger不会在父Logger的appender里输出，默认为true。
log4j.rootLogger = DEBUG,stdout,D,E

### log4j.appender.appenderName = className
##(1)org.apache.log4j.ConsoleAppender（控制台）
##(2)org.apache.log4j.FileAppender（文件）
#(3)org.apache.log4j.DailyRollingFileAppender（每天产生一个日志文件）
#(4)org.apache.log4j.RollingFileAppender（文件大小到达指定尺寸的时候产生一个新的文件）
#(5)org.apache.log4j.WriterAppender（将日志信息以流格式发送到任意指定的地方）
log4j.appender.stdout = org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target = System.out
log4j.appender.stdout.layout = org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern = [%-5p] %d{yyyy-MM-dd HH:mm:ss,SSS} method:%l%n%m%n

log4j.appender.D = org.apache.log4j.DailyRollingFileAppender
log4j.appender.D.File = ./log.txt
log4j.appender.D.Append = true
log4j.appender.D.Threshold = ALL

# log4j.appender.appenderName.layout=className展现形式
log4j.appender.D.layout = org.apache.log4j.PatternLayout
log4j.appender.D.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss}  [ %t:%r ] - [ %p ]  %m%n

log4j.appender.E = org.apache.log4j.DailyRollingFileAppender
log4j.appender.E.File =F://logs/error.log
log4j.appender.E.Append = true
log4j.appender.E.Threshold = ERROR
log4j.appender.E.layout = org.apache.log4j.PatternLayout
log4j.appender.E.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss}  [ %t:%r ] - [ %p ]  %m%n
```

```properties
# 全面的配置文件
log4j.rootLogger=DEBUG,console,dailyFile,im
log4j.additivity.org.apache=true
# 控制台(console)
log4j.appender.console=org.apache.log4j.ConsoleAppender
log4j.appender.console.Threshold=DEBUG
log4j.appender.console.ImmediateFlush=true
log4j.appender.console.Target=System.err
log4j.appender.console.layout=org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=[%-5p] %d(%r) --> [%t] %l: %m %x %n
# 日志文件(logFile)
log4j.appender.logFile=org.apache.log4j.FileAppender
log4j.appender.logFile.Threshold=DEBUG
log4j.appender.logFile.ImmediateFlush=true
log4j.appender.logFile.Append=true
log4j.appender.logFile.File=D:/logs/log.log4j
log4j.appender.logFile.layout=org.apache.log4j.PatternLayout
log4j.appender.logFile.layout.ConversionPattern=[%-5p] %d(%r) --> [%t] %l: %m %x %n
# 回滚文件(rollingFile)
log4j.appender.rollingFile=org.apache.log4j.RollingFileAppender
log4j.appender.rollingFile.Threshold=DEBUG
log4j.appender.rollingFile.ImmediateFlush=true
log4j.appender.rollingFile.Append=true
log4j.appender.rollingFile.File=D:/logs/log.log4j
log4j.appender.rollingFile.MaxFileSize=200KB
log4j.appender.rollingFile.MaxBackupIndex=50
log4j.appender.rollingFile.layout=org.apache.log4j.PatternLayout
log4j.appender.rollingFile.layout.ConversionPattern=[%-5p] %d(%r) --> [%t] %l: %m %x %n
# 定期回滚日志文件(dailyFile)
log4j.appender.dailyFile=org.apache.log4j.DailyRollingFileAppender
log4j.appender.dailyFile.Threshold=DEBUG
log4j.appender.dailyFile.ImmediateFlush=true
log4j.appender.dailyFile.Append=true
log4j.appender.dailyFile.File=D:/logs/log.log4j
log4j.appender.dailyFile.DatePattern='.'yyyy-MM-dd
log4j.appender.dailyFile.layout=org.apache.log4j.PatternLayout
log4j.appender.dailyFile.layout.ConversionPattern=[%-5p] %d(%r) --> [%t] %l: %m %x %n
# 应用于socket
log4j.appender.socket=org.apache.log4j.RollingFileAppender
log4j.appender.socket.RemoteHost=localhost
log4j.appender.socket.Port=5001
log4j.appender.socket.LocationInfo=true
# Set up for Log Factor 5
log4j.appender.socket.layout=org.apache.log4j.PatternLayout
log4j.appender.socket.layout.ConversionPattern=[%-5p] %d(%r) --> [%t] %l: %m %x %n
# Log Factor 5 Appender
log4j.appender.LF5_APPENDER=org.apache.log4j.lf5.LF5Appender
log4j.appender.LF5_APPENDER.MaxNumberOfRecords=2000
# 发送日志到指定邮件
log4j.appender.mail=org.apache.log4j.net.SMTPAppender
log4j.appender.mail.Threshold=FATAL
log4j.appender.mail.BufferSize=10
log4j.appender.mail.From = xxx@mail.com
log4j.appender.mail.SMTPHost=mail.com
log4j.appender.mail.Subject=Log4J Message
log4j.appender.mail.To= xxx@mail.com
log4j.appender.mail.layout=org.apache.log4j.PatternLayout
log4j.appender.mail.layout.ConversionPattern=[%-5p] %d(%r) --> [%t] %l: %m %x %n
# 应用于数据库
log4j.appender.database=org.apache.log4j.jdbc.JDBCAppender
log4j.appender.database.URL=jdbc:mysql://localhost:3306/test
log4j.appender.database.driver=com.mysql.jdbc.Driver
log4j.appender.database.user=root
log4j.appender.database.password=
log4j.appender.database.sql=INSERT INTO LOG4J (Message) VALUES('=[%-5p] %d(%r) --> [%t] %l: %m %x %n')
log4j.appender.database.layout=org.apache.log4j.PatternLayout
log4j.appender.database.layout.ConversionPattern=[%-5p] %d(%r) --> [%t] %l: %m %x %n

# 自定义Appender
log4j.appender.im = net.cybercorlin.util.logger.appender.IMAppender
log4j.appender.im.host = mail.cybercorlin.net
log4j.appender.im.username = username
log4j.appender.im.password = password
log4j.appender.im.recipient = corlin@cybercorlin.net
log4j.appender.im.layout=org.apache.log4j.PatternLayout
log4j.appender.im.layout.ConversionPattern=[%-5p] %d(%r) --> [%t] %l: %m %x %n
```

```properties
# 常用的配置文件
log4j.rootLogger=DEBUG, stdout, logfile
 
log4j.category.org.springframework=ERROR
log4j.category.org.apache=INFO
 
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d %p [%c] - %m%n
 
log4j.appender.logfile=org.apache.log4j.RollingFileAppender
log4j.appender.logfile.File=${myweb.root}/WEB-INF/log/myweb.log
log4j.appender.logfile.MaxFileSize=512KB
log4j.appender.logfile.MaxBackupIndex=5
log4j.appender.logfile.layout=org.apache.log4j.PatternLayout
log4j.appender.logfile.layout.ConversionPattern=%d %p [%c] - %m%n
```

```properties
# 不同的Logger输出到不同文件
	private static Log logger = LogFactory.getLog(Test.class);
#然后在log4j.properties中加入:
log4j.logger.cn.com.Test= DEBUG, test
log4j.appender.test=org.apache.log4j.FileAppender
log4j.appender.test.File=${myweb.root}/WEB-INF/log/test.log
log4j.appender.test.layout=org.apache.log4j.PatternLayout
log4j.appender.test.layout.ConversionPattern=%d %p [%c] - %m%n
# 也就是让cn.com.Test中的logger使用log4j.appender.test所做的配置。
```

### 5. 日志规范

1. 正确的定义日志 `private static final Logger LOG = LoggerFactory.getLogger(this.getClass());` 通常一个类只有一个 LOG 对象，如果有父类可以将 LOG 定义在父类中。
    日志变量类型定义为门面接口（如 slf4j 的 Logger），实现类可以是 Log4j、Logback 等日志实现框架，不要把实现类定义为变量类型，否则日志切换不方便，也不符合抽象编程思想。

2. 使用参数化形式{}占位，[] 进行参数隔离 `LOG.debug("Save order with order no：[{}], and order amount：[{}]");` 这种可读性好，这样一看就知道[]里面是输出的动态参数，{}用来占位类似绑定变量，而且只有真正准备打印的时候才会处理参数，方便定位问题。
    如果日志框架不支持参数化形式，且日志输出时不支持该日志级别时会导致对象冗余创建，浪费内存，此时就需要使用 isXXEnabled 判断，如：

   ```java
   if(LOG.isDebugEnabled()){
       // 如果日志不支持参数化形式，debug又没开启，那字符串拼接就是无用的代码拼接，影响系统性能
       logger.debug("Save order with order no：" + orderNo + ", and order amount：" + orderAmount);
   }
   ```

   至少 debug 级别是需要开启判断的，线上日志级别至少应该是 info 以上的。 这里推荐大家用 SLF4J 的门面接口，可以用参数化形式输出日志，debug 级别也不必用 if 判断，简化代码。

3. 输出不同级别的日志，项目中最常用有日志级别是ERROR、WARN、INFO、DEBUG四种了，这四个都有怎样的应用场景呢。

   **ERROR（错误）** 一般用来记录程序中发生的任何异常错误信息（Throwable），或者是记录业务逻辑出错。
    **WARN（警告）** 一般用来记录一些用户输入参数错误、
    **INFO（信息）** 这个也是平时用的最低的，也是默认的日志级别，用来记录程序运行中的一些有用的信息。如程序运行开始、结束、耗时、重要参数等信息，需要注意有选择性的有意义的输出，到时候自己找问题看一堆日志却找不到关键日志就没意义了。
    **DEBUG（调试）** 这个级别一般记录一些运行中的中间参数信息，只允许在开发环境开启，选择性在测试环境开启。

**错误打日志的例子：**

1. 不要使用 `System.out.print..` 输出日志的时候只能通过日志框架来输出日志，而不能使用 System.out.print.. 来打印日志，这个只会打印到 tomcat 控制台，而不会记录到日志文件中，不方便管理日志，如果通过服务形式启动把日志丢弃了那更是找不到日志了。

2. 不要使用 `e.printStackTrace()` 首先来看看它的源码：

   ```java
   public void printStackTrace() {
       printStackTrace(System.err);
   }// 它其实也是利用 System.err 输出到了 tomcat 控制台。
   ```

3. 不要抛出异常后又输出日志 如捕获异常后又抛出了自定义业务异常，此时无需记录错误日志，由最终捕获方进行异常处理。不能又抛出异常，又打印错误日志，不然会造成重复输出日志。

   ```java
   try {
       // ...
   } catch (Exception e) {
       // 错误
       LOG.error("xxx", e);
       throw new RuntimeException();
   }
   ```

4. 不要使用具体的日志实现类 `InterfaceImpl interface = new InterfaceImpl();` 这段代码大家都看得懂吧？应该面向接口的对象编程，而不是面向实现，这也是软件设计模式的原则，正确的做法应该是。

   `Interface interface = new InterfaceImpl();` 日志框架里面也是如此，上面也说了，日志有门面接口，有具体实现的实现框架，所以大家不要面向实现编程。

5. 没有输出全部错误信息 看以下代码，这样不会记录详细的堆栈异常信息，只会记录错误基本描述信息，不利于排查问题。

   ```java
   try {
       // ...
   } catch (Exception e) {
       // 错误
       LOG.error('XX 发生异常', e.getMessage());
       // 正确
       LOG.error('XX 发生异常', e);
   }   
   ```

6. 不要在千层循环中打印日志 这个是什么意思，如果你的框架使用了性能不高的 Log4j 框架，那就不要在上千个 for 循环中打印日志，这样可能会拖垮你的应用程序，如果你的程序响应时间变慢，那要考虑是不是日志打印的过多了。

   ```java
   for(int i=0; i<2000; i++){
       LOG.info("XX");
   }// 最好的办法是在循环中记录要点，在循环外面总结打印出来。
   ```

7. 禁止在线上环境开启 debug 这是最后一点，也是最重要的一点。 一是因为项目本身 debug 日志太多，二是各种框架中也大量使用 debug 的日志，线上开启 debug 不久就会打满磁盘，影响业务系统的正常运行。

