[TOC]



# 1.监听器



## 概念



> 监听器 listener	主要是对 三大域对象监听， servletContext,HttpSession,servletRequest
>
> 对他们的创建销毁监听
>
> 对他们的属性添加修改删除监听



- 1.ServletContext
  - ServletContextListener
    - 1.public void **contextInitialized**([ServletContextEvent](../javax.servlet.ServletContextEvent.html)  sce) ：初始化所以ServletContextListener,在init servlet 和过滤器之前 监听
    - 2.public void **contextDestroyed**([ServletContextEvent](../javax.servlet.ServletContextEvent.html)  sce) ：关闭销毁所有 servletContextListener 监听
  - ServletContextAttributeListener
    - 1.void attributeAdded(ServletContextAttributeEvent scab)在servlet上下文中添加一个属性，在添加属性后调用
    - 2.void attributeRemoved(ServletContextAttributeEvent scab)：通知属性已删除，删除后调用
    - 3.void attributeReplaced(ServletContextAttributeEvent scab)：已替换属性，替换后调用
- 2.**HttpSession**
  - HttpSessionListener
    - 1.void sessionDestroyed(HttpSessionEvent se)，通知某一个会话要即将失效
    - 2.void sessionCreated(HttpSessionEvent se)：通知创建了一个会话。 通知事件
  - HttpSessionAttributeListener
    - 1.void attributeAdded(HttpSessionBindingEvent se)：通知已将属性添加到会话。在添加属性之后调用
    - 2.void attributeRemoved(HttpSessionBindingEvent se):通知属性已从会话中移除。在移除属性之后调用。 
    - 3.void attributeReplaced(HttpSessionBindingEvent se)通知已替换会话中的一个属性。在替换属性之后调用。
- 3.ServletRequest
  - ServletRequestListener
    - ![image-20201208013138280](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201208013138280.png)
  - ServletRequestAttributeListener
    - ![image-20201208013148209](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201208013148209.png)



还要两个和javabean有关的监听器：

- 1.HttpSessionBindingListener:使对象在被绑定到会话或从会话中取消对它的绑定时得到通知。

  - ### public void **valueBound**([HttpSessionBindingEvent](../javax.servlet.http.HttpSessionBindingEvent.html)  event) :通知对象它将被绑定到某个会话并标识该会话。 

  - ### public void **valueUnbound**([HttpSessionBindingEvent](../javax.servlet.http.HttpSessionBindingEvent.html)  event) :通知对象要从某个会话中取消对它的绑定并标识该会话。 

事件对象：

![image-20201208012843709](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201208012843709.png)



## 使用



> 1.实现具体接口
>
> 2.注册监听器

```java
package com.viw.listener;

import javax.servlet.ServletContext;
import javax.servlet.http.HttpSessionEvent;
import javax.servlet.http.HttpSessionListener;

public class SessionListener implements HttpSessionListener {
    // 创建一个session监听
    @Override
    public void sessionCreated(HttpSessionEvent httpSessionEvent) {
        //得到一个servlet上下文对象
        ServletContext servletContext = httpSessionEvent.getSession().getServletContext();
        // 得到session id
        System.out.println(httpSessionEvent.getSession().getId());
        //获取servlet属性
        Integer online =(Integer) servletContext.getAttribute("online");
        if (online==null) {
            // 第一次调用
            online = new Integer(1);
        }else {
            int count = online.intValue();
            online = new Integer(count+1);
        }
        servletContext.setAttribute("online",online);
    }
    // 销毁一个session就触发
    @Override
    public void sessionDestroyed(HttpSessionEvent httpSessionEvent) {
        //得到一个servlet上下文对象
        ServletContext servletContext = httpSessionEvent.getSession().getServletContext();
        // 得到session id
        System.out.println(httpSessionEvent.getSession().getId());
        //获取servlet属性
        Integer online =(Integer) servletContext.getAttribute("online");
        if (online==null) {
            // 第一次调用
            online = new Integer(0);
        }else {
            int count = online.intValue();
            online = new Integer(count-1);
        }
        servletContext.setAttribute("online",online);
    }
}

```



```
<%--
  Created by IntelliJ IDEA.
  User: Pactera
  Date: 2020/12/7
  Time: 17:20
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<%
%>
<head>
    <title>Title</title>
</head>
<body>
<h1>当前在线<%=this.getServletConfig().getServletContext().getAttribute("online")%> 人</h1>
</body>
</html>

```

```xml
  <listener>
        <listener-class>com.viw.listener.SessionListener</listener-class>
    </listener>
```

