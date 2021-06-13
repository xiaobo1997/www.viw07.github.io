[TOC]



# 1.转发和重定向



## 概念



> 转发

RequestDispatcher接口提供了将请求分派到另一个资源的功能，该资源可能是html，servlet或jsp。 此接口还可以用于包含其他资源的内容。 这是Servlet协作的一种方法。RequestDispatcher接口中定义了两种方法。

- 1.public void forward（ServletRequest请求，ServletResponse响应）throws ServletException，java.io.IOException：将请求从Servlet转发到服务器上的另一个资源（Servlet，JSP文件或HTML文件）。
- 2.public void include（ServletRequest request，ServletResponse response）throws ServletException，java.io.IOException：在响应中包含资源（servlet，JSP页面或HTML文件）的内容。

![image-20201204012123081](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201204012123081.png)

![image-20201204012140776](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201204012140776.png)

**第二个响应会被包含在第一个响应里面返回**

**解决了一次请求内不同servlet的数据共享问题,但存在重复提交数据的风险**

请求转发，是**服务器** 的行为，请求由**服务器** 转发给另外一个页面处理，如何转发，何时转发，转发几次，客户端是不知道的。请求转发时，从发送第一次到最后一次请求的过程中，web容器创建**一次**request和response对象，新的页面继续处理同一个请求。也可以理解为服务器将**request** 对象在页面之间传递。**请求转发实际上就是一个servlet中调用其他的servlet**

## **特点**

请求转发: 不管跳转几个页面, **都是一次请求. 通过请求对象实现**

1. \> 客户端浏览器发送一次请求,剩下的由服务器进行处理
2. \> 地址栏不会改变,**存在RRL中的数据重复提交的风险,容易造成安全问题**
3. \> 参数可以一直传递,**参与的servlet共享request和response**
4. \> 只能跳转到内部资源(项目中), 不能跳转外部资源(项目外)
5. \> 可以访问受保护的资源(WEB-INF)

**它是以request域作为消息传递的载体**

> 重定向

HttpServletResponse接口的sendRedirect（）方法可用于将响应重定向到另一个资源，它可以是servlet，jsp或html文件。它接受相对URL和绝对URL。它在客户端工作，因为它使用了URL的url栏。 浏览器发出另一个请求。 因此，它可以在服务器内部和外部工作。

**重定向可以解决请求转发的一个风险问题:数据重复提交**

重定向，是客户端的行为，每次请求重定向都是由客户端发起的，也就是说重定向一次，就刷新request对象的属性，之前的request对象的属性值就失效了。

**作用:**

保护第一次的请求,避免因为用户的刷新动作频繁触发第一次请求servlet的重复执行

(因为如果是get提交的方式,使用请求转发,地址栏的地址不会发生变化且带着相关的数据,刷新页面一次,等于提交一次数据,这就存在**脏数据和安全风险的问题**,如何解决这一问题,这就需要使用重定向技术)



## 特点:

响应重定向: 通过响应对象实现, 跳转几个页面就发送几次请求

\> 发送多次请求,最少两次

\> 地址栏会发生改变(最后一次请求的路径)

\> 参数不能一直传递, 需要手动传递(原因是不是同一次请求了)

\> 既可以跳转到内部资源, 也可以跳转到外部资源

\> 不能访问受保护的资源



重定向，是客户端的行为，每次请求重定向都是由客户端发起的，也就是说重定向一次，就刷新request对象的属性，之前的request对象的属性值就失效了。所以不能用request对象来传递共享数据,

## 区别



|      | 转发                                                         | 重定向                                      |
| ---- | ------------------------------------------------------------ | ------------------------------------------- |
|      | 一次请求                                                     | 两次请求                                    |
|      | 地址栏不会变化                                               | 地址栏变化                                  |
|      | forward（）方法在服务器端起作用。                            | sendRedirect（）方法在客户端运行。          |
|      | 它将相同的请求和响应对象发送到另一个servlet。                | 它总是发送一个新的请求。                    |
|      | 它只能在服务器内工作。                                       | 可以在服务器内部和外部使用。                |
|      | 示例：request.getRequestDispacher（“ servlet2”）。forward（request，response）; | 示例：response.sendRedirect（“ servlet2”）; |
|      | 两种获取RequestDispactcher对象：1.调用servletContext 2调用servletRequest |                                             |
|      | 源组件和目标组件共享servletRequest和servletResponse对象      |                                             |
|      | 依赖RequestDispactcher接口                                   |                                             |



## 使用

### 转发

​	![image-20201204015045054](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201204015045054.png)



- 1.表单

```php+HTML
<html>
<head>
    <title>$Title$</title>
</head>
<body>
<form action="myservlet1" method="post">
    名字：<input type="text" name="name"/> <br>
    <input type="submit" name="sub" />提交
</form>
</body>
</html>
```

- 2.编写两个处理类

```java

public class MyServlet1 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doGet(req, resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("servlet1:"+req.getParameter("name"));
        req.setAttribute("name","servlet1");
        RequestDispatcher myServlet2 = req.getRequestDispatcher("myservlet2");
        myServlet2.forward(req,resp);
    }
}
```

```java

public class MyServlet2 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doGet(req, resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("获取request域的name值:"+req.getAttribute("name"));
        System.out.println("转发view下的login.jsp");
        RequestDispatcher dispatcher = req.getRequestDispatcher("view/login.jsp");
        dispatcher.forward(req,resp);
    }
}

```

- 3.注册

```xml
<servlet>
    <servlet-name>MyServlet1</servlet-name>
    <servlet-class>com.viw.servlet.MyServlet1</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>MyServlet1</servlet-name>
    <url-pattern>/myservlet1</url-pattern>
</servlet-mapping>

<servlet>
    <servlet-name>MyServlet2</servlet-name>
    <servlet-class>com.viw.servlet.MyServlet2</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>MyServlet2</servlet-name>
    <url-pattern>/myservlet2</url-pattern>
</servlet-mapping>
```



- 4.分析
  - 1.首先浏览器访问
    - ![image-20201204015344364](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201204015344364.png)
  - 2.地址栏是没变化的
    - ![image-20201204015531406](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201204015531406.png)



### 重定向



> 上面的其他不变，就改变 MyServlet2

```java
public class MyServlet2 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doGet(req, resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("获取request域的name值:"+req.getAttribute("name"));
        System.out.println("转发view下的login.jsp");
//        RequestDispatcher dispatcher = req.getRequestDispatcher("view/login.jsp");
//        dispatcher.forward(req,resp);
        resp.sendRedirect("view/login.jsp");
    }
}
```



- 1.浏览器加载了什么

![image-20201204020022196](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201204020022196.png)

- 2.

![image-20201204020301472](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201204020301472.png)

- 3.以Location地址再发送一次请求

![image-20201204020347427](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201204020347427.png)



## 选择



> 请求转发

内部的servlet转发 使用请求转发

请求转发是 在一个servlet 调用另外一个servlet ,并且请求域共享数据

servletA->servletB-->*.jsp   如果使用重定向 就发起三次请求，而请求转发只是一次请求

> 重定向

页面跳转一般使用重定向，发送二次以上请求

并且不共享请求域数据

# 2.javaweb中  `/`的不同意义

> 如果 `/`被 浏览器解析， 得到的地址就是  http://ip:port/ 

如   `<a href="/" />`

> 如果被 服务器解析  就是  http://ip:port/工程路径

如：  web.xml中 的 servlet的mapping 的url

还有  获取绝对路径  servletContext.getRealPath("/")

还有   request.getRequestDispatcher("/")



> 特殊情况 重定向   response.sendRediect("/")  把资源url发送给浏览器解析