---
title: HTTP协议总结
date: 2019-07-06 21:26:07
categories: JavaWeb学习
tags:
    - Web
---

## HTTP 协议总结

### 1 HTTP简介

1. 超文本传输协议，定义了客户端和服务器通信时传输的数据格式
2. 基于TCP/IP的高级协议，默认端口号为80
3. 基于请求-响应模型，一次请求对应一次响应
4. 无状态，每次请求之间相互独立，不能交互数据。需要使用`Cookie`和`Session`来进行数据交互
5. 版本为1.0时，每次请求都会建立新的链接，1.1版本时可以复用链接。对应头部的`keep-alive`

在HTTP协议中，两者之间的交互模式如下：

1. 客户端向服务器请求资源，其中包含`请求行、请求头、请求空行、请求体`
2. 服务器收到请求之后，做出相关逻辑操作，向客户端发送响应，包含`响应行、响应头、响应空行、响应体`
3. 不同次的交互之间是相互独立，共享数据和记录状态需要使用`Cookie和Session`机制。

### 2 请求消息数据格式

#### 1.2.1 请求行

请求行包含三部分内容，`请求方式、请求url、请求协议/版本`，如`GET /login.html HTTTP/1.1`

- 请求方式：HTTP协议中一共有7中请求方式，但是常用的有两种，`GET POST`。
  - GET：
    1. 请求参数在请求行中，在url后
    2. 请求的url是有限制的
    3. 不太安全
    4. 示例`https://www.baidu.com/s?wd=test&rsv_spt=1&rsv_iqid=0xfbda17eb000278bf&issp=1&f=8&rsv_bp=1&rsv_idx=2&ie=utf-8&tn=baiduhome_pg&rsv_enter=1&rsv_sug3=3`
  - POST：
    1. 请求参数在请求体中
    2. 请求的url长度没有限制
    3. 相对安全

#### 1.2.2 请求头：

​	客户端浏览器告诉服务器的一些信息，格式为`请求头名称:请求头值`，常见的请求头：

1. User-Agent：浏览器告诉服务器，访问使用的浏览器版本信息。服务器可以获取该部分信息，解决浏览器兼容问题
2. Refer：告诉服务器是从哪个页面来的请求。可以用于`防盗链、统计`工作。

#### 1.2.3 请求空行：

空行，就是用于分割POST请求的请求头、请求体的。

#### 1.2.4 请求体：

封装POST请求消息的请求参数的。对于GET请求没有请求体。

#### 1.2.5 实例

下面是一个请求消息的实例：

```txt
POST /login.html	HTTP/1.1  //请求行
Host: localhost      //请求头
User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:60.0) Gecko/20100101 Firefox/60.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate
Referer: http://localhost/login.html
Connection: keep-alive
Upgrade-Insecure-Requests: 1
													//请求空行
username=zhangsan	       // 请求体
```



### 3 响应消息数据格式

响应消息是服务器端发送给客户端的数据，由`响应行、响应头、响应空行、响应体`组成。

#### 1.3.1 响应行

响应行由`协议/版本 响应状态码 状态码描述`组成

- 响应状态码：服务器告诉客户端浏览器本次请求和响应的一个状态，都是三位数字
  1. 1XX：服务器接受客户端消息，但是没有接收完成，等待一段时间后，发送1XX状态码
  2. 2XX：成功。如200
  3. 3XX：重定向。302(重定向)、304(访问缓存)
  4. 4XX：客户端错误。如404(请求的路径没有对应的资源)、405(请求方式没有对应的doXXX方法)
  5. 5XX：服务器端发生错误，代表500(服务器内部出现错误，如逻辑处理的时候发生除0的情况)

#### 1.3.2 响应头

格式为`头名称:值`。常见的响应头如下所示：

1. Content-Type：服务器告诉客户端响应体数据格式和编码格式
2. Content-disposition：服务器告诉客户端以什么格式打开数据
   - In-line ：默认，在页面中打开
   - Attachment;filename=xxx：以附件的形式打开响应体，文件下载



#### 1.3.3 响应体

代表传输的数据



#### 1.3.4 实例

```txt
		HTTP/1.1 200 OK
		Content-Type: text/html;charset=UTF-8
		Content-Length: 101
		Date: Wed, 06 Jun 2018 07:08:42 GMT

		<html>
		  <head>
		    <title>$Title$</title>
		  </head>
		  <body>
		  hello , response
		  </body>
		</html>
```

### 4 Cookie

一次会话中包含多次请求和响应。一次会话在浏览器第一次给服务器资源发送资源，会话建立，直到一方断开为止

有了会话，就可以在一次会话的范围内的多次请求间共享数据。

使用的方式有两种：

- Cookie：客户端会话技术
- Session：服务器端会话技术

Cookie是客户端会话技术，**将数据保存在客户端**。基于响应头的`set-cookie`和请求头的`cookie`实现。

- 一次可以发送多个cookie。
- 默认情况下当浏览器关闭之后，Cookie数据被销毁。但是可以在服务器端设置Cookie存活时间，写到硬盘持久化存储
- 单个cookie的大小有限制(4KB)，同一个域名下的总的cookie数量也有限制(20个)
- cookie一般存放少量不太敏感的数据，不登录的情况下，完成服务器对客户端的身份识别



### 5. Session

服务器端会话技术，将数据保存在服务器对象中。Session的实现是依赖于Cookie的。服务器端将SessionID以Cookie的形式发送给客户端，然后客户端每次请求将其附在Cookie中，服务器就能够识别这次请求是属于哪一个Session。

细节：

1. 客户端关闭后，服务器不关闭，两次的`Session`默认情况下不是一个`Session`。如果需要相同，则服务器可以创建一个Cookie`JSESSIONID`，设置最大存活时间，让cookie持久化存储。
2. 服务器关闭，客户端不关闭，两次获取的Session不是一个Session
3. Session销毁的时机：
   1. 服务器关闭
   2. session对象调用`invalidate()`
   3. session默认失效时间
4. session用于存储一次会话的多次请求数据，存在服务器端。可以存储任意类型，任意大小的数据

#### 5.1 Cookie和Session的区别

- session存储数据在服务器，cookie在客户端
- session没有数据大小限制，cookie有
- session数据安全，cookie相对不安全

![](HTTP协议总结/Session原理.bmp)

