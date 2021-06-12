[TOC]





# 1.自动刷新页面



## 使用

> 使用 setIntHeader()来 刷新页面，



![image-20201207171808595](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201207171808595.png)

```jsp

<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<%
response.setCharacterEncoding("utf-8");
// 甚至响应头  并设置刷新时间
    response.setIntHeader("Refresh", 5);
response.setContentType("text/html");
  Calendar calendar =new    GregorianCalendar();
    int hour = calendar.get(Calendar.HOUR);
    int minute = calendar.get(Calendar.MINUTE);
    int second = calendar.get(Calendar.SECOND);
    String time = hour+":"+minute+":"+second;
    response.getWriter().write(time);
%>
<head>
    <title>Title</title>
</head>
<body>

</body>
</html>

```



# 2.计数器



## 使用

> 在init()时声明一个全局变量
>
> 每一次点击 增加

