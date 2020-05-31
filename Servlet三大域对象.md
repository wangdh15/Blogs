---
title: Servlet三大域对象
date: 2019-07-10 10:21:42
categories: JavaWeb学习
tags:
- Java
- Web
---

## 1. Servlet三大域对象

在Servlet中，为了共享数据，设置了一些域对象。其中`request/session/application(ServletContext)`有不同的范围。

### 1.1. request

request代表一次请求，发出一个请求就有一个request。

- 作用域：`只在当前请求中有效`
- 用处：用于服务器间同一页面之间的参数传递，转发，表单的控件值传递。
- 方法：`request.setAttribute(); request.getAttribute(); request.removeAttribute(); request.getParameter()`

```java
// File1
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/demo1")
public class Demo1 extends HttpServlet {

    public static void main(String[] args) {

    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("demo1");
        req.setAttribute("test", "aaaa");
        req.getRequestDispatcher("/demo2").forward(req, resp);
    }
}

// File2
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/demo2")
public class Demo2 extends HttpServlet {

    public static void main(String[] args) {


    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        Object aaa = req.getAttribute("test");
        System.out.println(aaa);
        System.out.println("demo2");
    }
}
```

### 1.2 session

服务器为每个对话创建一个session对象，session中的数据可供当前会话中的所有sevlet共享。

- 用处：常用于登录页面验证。(当用户登录成功后浏览器分配其一个session键值对)
- 方法：`session.setAttribute(); session.getAttribute(); session.removeAttribute()`

```java
// File1
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.io.IOException;

@WebServlet("/sessionDemo1")
public class SessionDemo1 extends HttpServlet {

    public static void main(String[] args) {


    }


    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        HttpSession sess = req.getSession();
        sess.setAttribute("test", "aaa");
    }
}

// File2
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.io.IOException;

@WebServlet("/sessionDemo2")
public class SessionDemo2 extends HttpServlet {

    public static void main(String[] args) {


    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        HttpSession reqSession = req.getSession();
        Object test = reqSession.getAttribute("test");
        System.out.println(test);
    }
}
```

### 1.3 application(ServletContext)

所有用户都可以访问到的信息，一个Web站点有一个ServletContext对象，在服务器启动时创建，关闭时销毁。

用处：一般在多个客户端之间共享数据使用。如此站点一共被访问了多少次。

方法：`setAttribute();getAttribute()`

```java
// File1
import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/appDemo1")
public class AppDemo1 extends HttpServlet {

    public static void main(String[] args) {


    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletContext servletContext = getServletContext();
        servletContext.setAttribute("test", "aa");
    }
}

// File2
import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/appDemo2")
public class AppDemo2 extends HttpServlet {

    public static void main(String[] args) {


    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletContext servletContext = getServletContext();
        Object test = servletContext.getAttribute("test");
        System.out.println(test);
    }
}
```

### 1.4 小结

总的来说，上述三个域对象的范围是由小变大的`request<session<ServletContext`，在使用的时候需要根据数据的传播范围来获取相对应的对象，然后设置属性：值对，供其他地方使用。

