---
title: Ajax、jQuery
date: 2019-07-15 22:19:33
categories:
	- JavaWeb学习
tags:
	- Java
	- Web
---

## 1. Ajax

Asynchronous JavaScript and XML,用JavaScript执行异步网路请求。

HTTP请求是一次请求对应一个页面。但是如果希望用户停留在当前页面同时发出新的HTTP请求，需要用`JavaScript`来发送这个请求并用`JavaScript`来更新页面信息。这样感觉停留在当前页面，但是内容却在更新。

Ajax是异步执行的，所以需要回调函数来获得响应并更新页面。Ajax 是一种在无需重新加载整个网页的情况下，能够更新部分网页的技术。通过在后台与服务器进行少量数据交换，Ajax 可以使网页实现异步更新。这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。传统的网页（不使用 Ajax）如果需要更新内容，必须重载整个网页页面。

下面是一个使用`jQuery`来实现Ajax请求的示例：

```javascript
// $.get(url, [data], [callback], [type])
// $.post(url, [data], [callback], [type])
/*
				* 参数：
				* url：请求路径
				* data：请求参数
				* callback：回调函数
				* type：响应结果的类型
*/
function  fun() {
  $.get("ajaxServlet",{username:"rose"},function (data) {
    alert(data);
  },"text");
}
// 定义了一个Ajax请求函数，可以在某个按钮绑定这个函数，则点击之后就会发送Ajax请求，
// 在服务器端定义一个处理请求路径的Servlet，就可以进行逻辑处理并返回数据。
```

## 2. jQuery

​	`jQuery`是一个快速、简洁的JavaScript框架。它封装JavaScript常用的功能代码，提供一种简便的JavaScript设计模式，优化HTML文档操作、事件处理、动画设计和Ajax交互。具体使用方法可以查看文档。

其在文档加载之后需要制定的代码只需要使用下面的方式即可：

```javascript
$(function(){
  //...
});
// 可以定义多个这种模块，可在文档加载完成之后对时间进行绑定，动态发送Ajax请求，获得数据之后展示页面等等。
```

