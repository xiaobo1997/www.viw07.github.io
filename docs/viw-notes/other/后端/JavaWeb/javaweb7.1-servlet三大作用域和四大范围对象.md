[TOC]





# 1.三大作用域



# 2.四大范围对象

![image-20201206221801237](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201206221801237.png)

![image-20201206221929792](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201206221929792.png)

![image-20201206222642778](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201206222642778.png)



> setAttribute(a,b) 如果a 不存在，就创建
>
> 如果存在，就改为b



## 1. pageContext

> 当前页面有效





```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<%
    // 如果跳转其他页面就无效
    pageContext.setAttribute("aaa", "bbb");
%>
<%
    pageContext.getAttribute("aaa");
%>
</body>
</html>
```



## 2.request

> 同一次请求有效



第一个页面 跳转下一个页面

```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
requestDemo
</body>
<%
    request.setAttribute("aaa", "bbb");
    request.getRequestDispatcher("requestDemo1.jsp").forward(request,response);
%>
</html>
```



```jsp

<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
requestDemo1
</body>
<%
    request.getAttribute("aaa");
    response.getWriter().println(request.getAttribute("aaa"));
%>
</html>
```

![image-20201206223941052](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201206223941052.png)

**如果是重定向就不可以了，因为是两次请求**

## session

> 同一次会话有效
>
> 不管切换页面都可以，



## application

> application是全局对象
>
> 整个过程有效
>
> 不管是关闭浏览器还是切换页面 都有效，只要是这个项目运行中，



# JNDI 



> 多个项目共享，重启后仍然有效









> 两个主要是方法
>
> String getContextPath()
>
> String getRealPath()



```jsp
<%="虚拟路径："+application.getContextPath()%>

<%="绝对路径："+application.getRealPath("/javaweb2")%>
```

![image-20201206221103643](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201206221103643.png)