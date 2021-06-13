[TOC]



# 1.web.xml配置

> web.xml的作用：

web.xml是web应用的配置文件，web.xml和tomcat是相互独立的

> web.xml中的标签元素

![image-20201130225301497](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201130225301497.png)

## 1.欢迎页

```java
<welcome-file-list>
    <welcome-file>index1.jsp</welcome-file>
    <welcome-file>index2.jsp</welcome-file>
    <welcome-file>index3.jsp</welcome-file>
    <welcome-file>index4.jsp</welcome-file>
    <welcome-file>/target/redirectAndFoward.jsp</welcome-file>
</welcome-file-list>
```

> 欢迎页是启动工程时默认访问的首页，相当于 http://ip:port/工程名/index.jsp
>
> 当启动工程访问时首先寻找index1.jsp，没有就index2.jsp 然后最后一个都没有找到就报404



## 2.配置servlet和mapping

> servlet标签是来定义servlet的

```xml
  <servlet>
        <!--servlet的名字，与你要注册的servlet名字尽量保持一致-->
        <servlet-name>MyServlet</servlet-name>
        <!--当前servlet所在类的全限定类名-->
        <servlet-class>com.viw.servlet.MyServlet</servlet-class>
        <!--定义的参数名和参数值-->
        <init-param>
            <param-name>username</param-name>
            <param-value>xiaobo</param-value>
        </init-param>
        <!--servlet在工程启动时的加载顺序，越小越先加载-->
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <!--约定与上面保持一致，-->
        <servlet-name>MyServlet</servlet-name>
        <!--当前servlet的访问路径，http://ip:port/工程名/myservlet1  访问这个注册的servlet-->
        <url-pattern>/myservlet1</url-pattern>
    </servlet-mapping>

```



## 3.url--匹配规则

- 1.精确匹配  `/xxxxServlet` 开头

  - ```xml
    <!--如我当前 工程名   viw-->
    http://ip:port/viw/myservlet1
    <url-pattern>/myservlet1</url-pattern>
    http://ip:port/servlet/myservlet1
    <url-pattern>/servlet/myservlet1</url-pattern>
    ```

- 2.路径匹配  `/servlet/*`

- 3.扩展名匹配   `/.do`



## 4.listener监听器

> 监听器一般是来监听客户端请求，服务器做的操作等，

一般监听器是来监听session的

![image-20201201232525473](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201201232525473.png)

还有其他监听器 如：

- 1.ServletContextListener:主要监听web的事件，如工程服务的启动和销毁等，
- 2.ServletContextAttributeListener:监听web应用属性的变化，如在web应用中添加修改删除属性，监听时间为ServletContextAttributeEvent
- 3.HttpSessionBindingListener: 是一个不需要在web.xml中注册的listener，它是一个接口，我们实现这个接口，实现他的两个方法，valueBound和valueUnbound，只要对象进入session和从session删除，容器都会调用这两个方法
- 4.HttpSessionAttributeListener：用于监听HttpSession中的属性的操作，当session里面增加一个属性时，触发attributeAdded(HttpSessionBindEvent se)方法；当在session中删除一个属性时，触发attributeRemoved(HttpSessionBindEvent se)方法；当session属性被重新设置时，触发attributeReplaced(HttpSessionBindingEvent se)方法。
  - 这个监听器对所有session
- 5.HttpSessionListener：监听HttpSession。当创建一个session时，触发sessionCreated(HttpSessionEvent se)方法；当销毁一个session时，会触发sessionDestoryed(HttpSessionEvent se)方法
- 6.HttpSessionActivationListener： 这个是在虚拟机会话数据迁移有关系，侦听通知它们会话将被钝化(持久化到硬盘上)和会话将被激活的容器事件
- 7.ServletRequestListener：监听request的创建和销毁
- 8.ServletResponseListener:监听response的属性增删改

```xml
    <listener>
        <!--监听器的全限定类名，编写监听器需要在web.xml中注册-->
        <listener-class>com.viw.listener.UserListener</listener-class>
    </listener>
    
```





## 5.过滤器

> 一般处理字符，页面权限等

```xml
<filter>
    <!--过滤器名字 和创建的保持一致-->
    <filter-name>EncodeFilter</filter-name>
    <!--全限定类名-->
    <filter-class>com.viw.filter.EncodeFilter</filter-class>
</filter>
<filter-mapping>
    <!--和上面保持一致-->
    <filter-name>EncodeFilter</filter-name>
    <!--否则过滤的路径，下面的意思是 当访问 *.jsp就会触发 过滤器-->
    <url-pattern>*.jsp</url-pattern>
</filter-mapping>
```



##  6Context上下文配置

> 在context配置中标签的  应用于整个过程。整个过程都是共享的。

```xml
  <context-param>
        <param-name>port</param-name>
        <param-value>8080</param-value>
    </context-param>
```

在servlet中可以取

```java
  @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println(getServletContext().getInitParameter("port"));
    }
    //console
    8080
```

## 7.mime-mapping

> 指定对应格式文件在浏览器打开的处理方式

```java
 <mime-mapping>
        <extension>pdf</extension>
        <mime-type>application/pdf</mime-type>
    </mime-mapping>
    <mime-mapping>
        <extension>txt</extension>
        <mime-type>text/plain</mime-type>
    </mime-mapping>

```

> 常见的mime的配置

| **类  型**         | **后  缀**  | **MIME类型**             |
| ------------------ | ----------- | ------------------------ |
| 超文本标记语言文本 | .htm、.html | text/html                |
| 普通文本           | .txt        | text/plain               |
| RFT文本            | .rtf        | application/rtf          |
| GIF图形            | .gif        | image/gif                |
| JPEG图形           | .jpg、.jpeg | image/jpeg               |
| au声音文件         | .au         | audio/basic              |
| MIDI音乐文件       | .mid、.midi | audio/midi、audio/x-midi |
| RealAudio音乐文件  | .ra、.ram   | audio/x-pn-realaudio     |
| MPEG文件           | .mpg、.mpeg | video/mpeg               |
| AVI文件            | .avi        | video/x-msvideo          |
| GZIP文件           | .gz         | application/x-gzip       |
| TAR文件            | .tar        | application/x-tar        |



## session-config

> 这个是 指定session失效时间的

```xml

    <session-config>
        <session-timeout>30</session-timeout>
    </session-config>
```



## 元素加载顺序

> context>listener>filtrer>servle



