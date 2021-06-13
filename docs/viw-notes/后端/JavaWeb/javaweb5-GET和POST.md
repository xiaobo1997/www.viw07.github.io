[TOC]



# 1.GET和POST



## 原理的区别

> 当你从浏览器向服务器传递一些数据和消息的时候，浏览器一般使用两种方法 GET方法和POST方法





> http定义了与服务器交互的不同方法，最基本的方法有4种，分别是**GET**,**POST**,**PUT** ,**DELETE**,这些都是资源描述符，
>
> 而这四个方法 分别表示对资源的 **查**，**改**，**增**，**删**，
>
> restful API 就是用这四个请求方法发请求的。完全足够了。



|                  | GET                                                          | POST                                                         |
| ---------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 在缓存中存取资源 | 可以                                                         | 不可以                                                       |
| 作用             | 获取/查询资源，不影响资源状态                                | 一般更新资源                                                 |
|                  | 安全(获取信息而不是修改信息)和幂等(同一个URL的多个请求应该返回相同的结果) |                                                              |
|                  | GET是浏览器的默认方法，也就是说你不指定一个表单method时默认是GET |                                                              |
|                  | GET有最长字符限制 1024k，GET的请求字符会暴露在URL地址栏中，比较危险 | 将信息传递给后台程序的一种更可靠的方法是 POST 方法           |
| 打包信息的方式   | GET 方法向页面请求发送已编码的用户信息。页面和已编码的信息用 ? 字符分隔， | POST把信息作为一个单独的消息发送。消息以标准输出的形式传到后台程序，你可以在你的处理过程中解析并使用这些标准输出。 |



## 使用一个案例来说明一下区别



> GET请求

- 1.首先有一个表单

```html
<%--
  Created by IntelliJ IDEA.
  User: xiaobo
  Date: 2020/11/30
  Time: 1:06
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
  <head>
    <title>web01</title>
  </head>
  <body>
  <h4>hello</h4>
  <form action="myservlet" method="get" >
    名字<input type="text" name="name"/></br>
    密码<input type="password" name="password"/></br>
    <input value="login" type="submit">
  </form>
  </body>
</html>

```

- 2.然后 编写 继承 HttpServlet的处理类

```java
public class MyServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println(getServletContext().getInitParameter("port"));
        resp.setContentType("text/html;charset=UTF-8");
        System.out.println("请求方法：" + req.getMethod());
        System.out.println("请求头：" + req.getHeader("Host"));
        System.out.println("URI:" + req.getRequestURL());
        Enumeration<String> headerNames = req.getHeaderNames();
        //获取所有请求头列表
        while (headerNames.hasMoreElements()) {
            System.out.println(headerNames.nextElement() + ":" + req.getHeader(headerNames.nextElement()));
        }

    }

```

- 3.

![image-20201203225631916](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201203225631916.png)

控制台

```java
请求方法：GET
请求头：localhost:8080
URIhttp://localhost:8080/web01/myservlet
host:keep-alive
cache-control:1
user-agent:text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
sec-fetch-site:navigate
sec-fetch-user:document
referer:gzip, deflate, br
accept-language:JSESSIONID=4192F99EAE37FF1CE7E514767BA4C4D2; Idea-a4784510=9975a5de-6639-4e75-95c3-4fc7ceed0174

```



**可以看到地址栏上有我们的请求信息，每一对值之间用 `&` 分割**



> POST

- 1.首先是改变表单提交方法，改为POST

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
  <title>web01</title>
</head>
<body>
<h4>hello</h4>
<form action="myservlet" method="post" >
  名字<input type="text" name="name"/></br>
  密码<input type="password" name="password"/></br>
  <input value="login" type="submit">
</form>
</body>
</html>
```

- 2.编写处理POST请求的处理方法  doPost()

```java
@Override
protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    // 设置请求字符集格式
    resp.setContentType("text/html;charset=UTF-8");
    System.out.println("请求方式" + req.getMethod());
    System.out.println("请求资源URI" + req.getRequestURI());
    System.out.println("请求URL" + req.getRequestURL());
    System.out.println("==========");
    System.out.println("请求头" + req.getHeader("Host"));
    // 获取访问者的浏览器user-agent信息
    System.out.println("请求头user-agent" + req.getHeader("User-Agent"));
    Enumeration<String> headerNames = req.getHeaderNames();
    //获取所有请求头列表
    while (headerNames.hasMoreElements()) {
        String s = headerNames.nextElement();
        String headerValue = req.getHeader(s);
        System.out.println(s + ":" + headerValue);
    }
    System.out.println("======");
    // 获取请求头的实体内容
    ServletInputStream inputStream = req.getInputStream();
    byte[] buff = new byte[1024];
    int i = 0;
    while ((i = inputStream.read(buff)) != -1) {
        System.out.println((char) i);
    }
}
```

- 3.浏览器地址栏的变化

![image-20201203231718751](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201203231718751.png)

**POST的请求参数是在 实体内容中**



## GET和POST总结

### 请求字符集乱码的问题



> 我们使用 HttpServletRequest来 获取请求参数
>
> 使用HttpServletResponse 来响应请求



> 关于字符编码的问题：
>
> 处理GET请求编码：  String name=request.getParameter("name");
> String names=new String(name.getBytes(“iso8859-1”),”utf-8”);
>
> 处理 POST：Request.setCharacterEncoding(“utf-8”);
>
> 或者设置服务器的字符集
>
> resp.setCharacterEncoding("UTF-8");
>
> 设置浏览器的字符集
>
> resp.serHeader("Content-Type","text/html;charset=UTF-8");
>
> 或者
>
> resp.setContentType("text/html;charset=UTF-8");

## 表单GET&POST案例 1.



> 1.首先一个普通form页面

```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
  <title>web01</title>
</head>
<body>
<h4>hello</h4>
<form action="myservlet2" method="GET" >
  名字<input type="text" name="name"/></br>
  密码<input type="password" name="password"/></br>
  性别: <input name="sex" type="radio" value="1"/>男
  <input name="sex" type="radio" value="0"/>
  期望薪资: <select name="money">
  <option value="1">10K-14K</option>
  <option value="2">14K-16K</option>
  <option value="3">16K-20K</option>
</select>
  爱好: <input  type="checkbox" name="ah" value="lq"/>篮球
  <input  type="checkbox" name="ah" value="zq"/>足球
  <input value="login" type="submit">
</form>
</body>
</html>
```



> 2.编写处理逻辑

```java
public class MyServlet2 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("请求体内容:"+req.getQueryString());

        System.out.println("name:"+req.getParameter("name")+"||"+"密码"+req.getParameter("password"));

        System.out.println("性别"+(req.getParameter("sex").equals("1")?"男":"女"));

        System.out.println("期望薪资"+req.getParameter("money"));

        Enumeration<String> parameterNames = req.getParameterNames();
        while (parameterNames.hasMoreElements())
        {
            String s = parameterNames.nextElement();
            System.out.println("爱好："+req.getParameter(s));
        }
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req,resp);
        ServletInputStream inputStream = req.getInputStream();
        int i = 0;
        while (( i =inputStream.read(new byte[1024]))!= -1){
            System.out.println((char)i);
        }
    }
}
```

![image-20201204002711123](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201204002711123.png)



地址栏：

![image-20201204002740861](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201204002740861.png)

参数：

![image-20201204002804701](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201204002804701.png)





