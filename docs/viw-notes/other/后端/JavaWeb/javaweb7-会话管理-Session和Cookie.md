[TOC]



# 1.会话管理Cookie和Session





## 概念



> session会话跟踪是一种一种**维护用户状态**（数据）的方法
>
> Http协议是**无状态**的，因此我们需要使用会话跟踪技术来维护状态。 每次用户向服务器请求时，服务器都会将该请求视为新请求。 因此，我们需要**维护用户的状态**以识别特定用户。

无状态意味着：

![image-20201205003419632](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201205003419632.png)



http会话管理主要是使用了 下面四个

- 1.Cookie
- 2.HttpSession
- 3.Hidden Form Field
- 4.URL Rewriting



## cookie

> cookie是服务端产生，保存在客户端，相当于起到本地缓存的作用。安全性比较差



> 服务器增加cookie： response对象(response.addCookie(Cookie cookie))，然后客户端去取cookie:resquest 对象( requset.getCookie )

**只能一次性把所有的cookie对象获取，然后通过 getName() getValue() 来 获取这样的key-value形式的键值对**



> 一个cookie 大概 4kb
>
> 一个服务器最多可以向浏览器发送20个 cookie
>
> 一个浏览器最多 保存 300个cookie



> cookie的有效时间

`cookie.setMaxAge(long second)` 来设置cookie的运行时间

 second >0 浏览器把cookie保存到硬盘中

second<0 cooke在当前浏览器没有关闭的情况下生效，在内存中存在，

second = 0 浏览器会马上删除这个cookie



> cookie 的路径

Cookie 的 path 属性可用于限制指定 Cookie 的发送范围的文件目录。 



> cookie的域

通过 `cookie.setDomain(".baidu.com")` 来设置 以 .baidu.com 后缀都可以共享cookie

然后 通过   cookie.setPath("/"); 来实现在访问 百度的二级域名中共享 cookie

如 在 www.baidu.com   zhidao.baidu.com 共享cookie

> servlet cookie方法

|      | ** public void setDomain(String pattern)**该方法设置 cookie 适用的域，例如 tutorialspoint.com。 |
| ---- | ------------------------------------------------------------ |
| 2    | **public String getDomain()**该方法获取 cookie 适用的域，例如 tutorialspoint.com。 |
| 3    | **public void setMaxAge(int expiry)**该方法设置 cookie 过期的时间（以秒为单位）。如果不这样设置，cookie 只会在当前 session 会话中持续有效。 |
| 4    | **public int getMaxAge()**该方法返回 cookie 的最大生存周期（以秒为单位），默认情况下，-1 表示 cookie 将持续下去，直到浏览器关闭。 |
| 5    | **public String getName()**该方法返回 cookie 的名称。名称在创建后不能改变。 |
| 6    | **public void setValue(String newValue)**该方法设置与 cookie 关联的值。 |
| 7    | **public String getValue()**该方法获取与 cookie 关联的值。   |
| 8    | **public void setPath(String uri)**该方法设置 cookie 适用的路径。如果您不指定路径，与当前页面相同目录下的（包括子目录下的）所有 URL 都会返回 cookie。 |
| 9    | **public String getPath()**该方法获取 cookie 适用的路径。    |
| 10   | **public void setSecure(boolean flag)**该方法设置布尔值，表示 cookie 是否应该只在加密的（即 SSL）连接上发送。 |
| 11   | **public void setComment(String purpose)**该方法规定了描述 cookie 目的的注释。该注释在浏览器向用户呈现 cookie 时非常有用。 |
| 12   | **public String getComment()**该方法返回了描述 cookie 目的的注释，如果 cookie 没有注释则返回 null。 |



## session

> session保存在服务端
>
> **session依赖于 cookie，底层是cookie，session是servlet三大域之一（request,session,application）。**
>
> session实现了 一种对网站跨多个页面请求访问来识别用户标存储有关用户的信息。



> session的实现机制





> session会话
>
> 从浏览器打开到关闭就是一次会话
>
> 客户端和服务端一次会话通过jsessionid 一一对应（区分session）
>
> 服务端把生成的jsessionid 放入cookie中， name=jsessjionid
>
> value就是jsessionid的值（前面session自生成的sessionid）
>
> 里面可以保存一些登录信息什么的，下一次再访问时或n次后访问时，服务端就直接通过jsessionid来匹配（就通过浏览器cookie中的jsessionid 和服务中的 session  sessionid）匹配成功就说明这个用户不是第一次登录，就无需登录了，
>
> 第一次访问是没有jsessionid 就创建了，后面再服务就不需要创建，直接匹配，没有再创建
>
> ![image-20201206204339179](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201206204339179.png)

![image-20201206203940007](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201206203940007.png)



> session 的一些方法
>
> 通过  HttpSession session = request.getSession();

| 1    | **public Object getAttribute(String name)**该方法返回在该 session 会话中具有指定名称的对象，如果没有指定名称的对象，则返回 null。 |
| ---- | ------------------------------------------------------------ |
| 2    | **public Enumeration getAttributeNames()**该方法返回 String 对象的枚举，String 对象包含所有绑定到该 session 会话的对象的名称。 |
| 3    | **public long getCreationTime()**该方法返回该 session 会话被创建的时间，自格林尼治标准时间 1970 年 1 月 1 日凌晨零点算起，以毫秒为单位。 |
| 4    | **public String getId()**该方法返回一个包含分配给该 session 会话的唯一标识符的字符串。 |
| 5    | **public long getLastAccessedTime()**该方法返回客户端最后一次发送与该 session 会话相关的请求的时间自格林尼治标准时间 1970 年 1 月 1 日凌晨零点算起，以毫秒为单位。 |
| 6    | **public int getMaxInactiveInterval()**该方法返回 Servlet 容器在客户端访问时保持 session 会话打开的最大时间间隔，以秒为单位。 |
| 7    | **public void invalidate()**该方法指示该 session 会话无效，并解除绑定到它上面的任何对象。(退出登录或注销等) |
| 8    | **public boolean isNew(**如果客户端还不知道该 session 会话，或者如果客户选择不参入该 session 会话，则该方法返回 true。 |
| 9    | **public void removeAttribute(String name)**该方法将从该 session 会话移除指定名称的对象。 |
| 10   | **public void setAttribute(String name, Object value)**该方法使用指定的名称绑定一个对象到该 session 会话。 |
| 11   | **public void setMaxInactiveInterval(int interval)**该方法在 Servlet 容器指示该 session 会话无效之前，指定客户端请求之间的时间，以秒为单位。 |

> session共享的问题









## 使用



### cookie



> 设置cookie

```html
<form action="myservlet3" method="GET">
    名字：<input type="text" name="name"/> <br>
    密码：<input type="password" name="password"/><br>
   <%-- 记住我： <input type="checkbox" name="rememberme" />--%>
    <input type="submit" name="sub"/>
</form>
```



```java
  @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //设置请求响应字符集
        req.setCharacterEncoding("UTF-8");
        resp.setContentType("text/html;charset=UTF-8");

        //获取表单数据
        String name = req.getParameter("name");
        String password = req.getParameter("password");
        // 设置cookie
        Cookie cookiename = new Cookie("name",name);
        Cookie cookiepassword = new Cookie("password",password);
        // 设置cookie生效时间 秒为单位
        cookiename.setMaxAge(60);
        cookiepassword.setMaxAge(60);
        //设置相应格式
        resp.setContentType("text/html");
        resp.addCookie(cookiename);
        resp.addCookie(cookiepassword);
    }
```



> 获取cookie

```java
 resp.setContentType("text/html");
    // 实例化cookie
    String name1 = null;
    String value = null;
    Cookie cookie =null;
    Cookie[] cookies = null;
    // 获取所有cookie
    cookies = req.getCookies();
    for (int i = 0;i<cookies.length&&cookies!=null;i++){
         name1 = cookies[i].getName();
         value = cookies[i].getValue();
    }
    resp.getWriter().println("<h3>"+name1+"</h3>");
    resp.getWriter().println("<h3>"+value+"</h3>");
}
```



### 使用cookie记住用户名



> 流程

![image-20201206174133190](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201206174133190.png)



> 首先 `login.jsp` 里面写一个表单，
>
> 然后我们提交表单给服务器，服务器接收表单，通过 `request.getParameter(String val1)`  取出 表单参数的值，然后通过 `response.addCookie("")` 实例化cookie对象，然后让响应请求的时侯把这个生成的cookie 给客户端，然后 客户端保存，并设置和cookie的生效时间  `cookie.setMaxAge()`
>
> 此时模拟重定向 `resp.sentRedirect()` 到 `check.jsp`
>
> 然后再一次发 login.jsp 请求，可以看到  用户名已经自动填充好了



代码：

login.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<%!
    // 全局变量
    String namea;
%>
<%
    Cookie[] cookies = request.getCookies();
    for (Cookie cookie : cookies) {
        if (cookie.getName().equals("namea")) {
            namea = cookie.getValue();
        }
    }
%>
<head>
    <title>Title</title>
</head>
<body>
<form action="check.jsp" method="POST">
    用户名： <input type="text" name="namea" value="<%=(namea==null?"":namea)%>"/><br/>
    密码： <input type="password" name="password"/>
    <input type="submit" value="提交"/>
</form>
</body>
</html>

```



check.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<%
    //    设置编码
    request.setCharacterEncoding("utf-8");
    // 取出请求参数的值
    String name = request.getParameter("namea");
    String password = request.getParameter("password");
    // 把 name放入 cookie
    Cookie cookie = new Cookie("namea", name);
    // 设置cookie 时间
    cookie.setMaxAge(60);
    // 添加到response中
    response.addCookie(cookie);
    // 重定向 到 result.jsp,
    response.sendRedirect("result.jsp");
%>
</body>
</html>

```



success.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
</body>
</html>
```





### session

> 通过session来保存用户登录信息

登录页面，

```jsp
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
<form action="a.jsp" method="POST">
  用户名： <input  type="text" name="name" /><br/>
  密码： <input type="password" name="password" />
  <input type="submit" value="提交"/>
</form>
</body>
</html>
```

验证页面

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>显示cookie</title>
</head>
<body>
<%
    // 设置请求过来的字符编码
request.setCharacterEncoding("utf-8");
    String name = request.getParameter("name");
    String password = request.getParameter("password");
    if (name.equals("aaa")&& password.equals("aaa")) {
        //登录成功
        // 实例化session
        HttpSession httpSession = request.getSession();
        // 设置session生命
        httpSession.setMaxInactiveInterval(10);
        // 设置session name password
        httpSession.setAttribute("name",name);
        httpSession.setAttribute("password",password);
        // 请求转发
        request.getRequestDispatcher("b.jsp").forward(request,response);
    }else {
        // 登录失败
        response.sendRedirect("index.jsp");
    }
%>
</body>
</html>

```



登录成功

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<%

%>
<head>
    <title>取出cookie</title>
</head>
<body>
welcome： <%  String name = (String) session.getAttribute("name");
    if (name==null) {
        response.sendRedirect("index.jsp");
    }
    out.print(name);
%>
</body>
</html>
```





## session和cookie的区别

|            | session  | cookie   |
| ---------- | -------- | -------- |
| 保存的位置 | 服务端   | 客户端   |
| 安全性     | 比较安全 | 比较危险 |
| 保存的内容 | object   | string   |

