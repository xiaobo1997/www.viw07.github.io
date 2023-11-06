[TOC]



# 1.servlet体系



## 概念



> servlet是什么

servlet运行在javaweb应用服务器中。servlet实例对象由servlet容器创建。

![image-20201201002228180](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201201002228180.png)

> javaweb体系

![image-20201130232642410](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201130232642410.png)



# 2.和tomcat联系

## 联系

> tomcat是servlet的容器，处理客户端请求，把请求传给servlet，让servlet处理以后，把对应的servlet响应发送给客户端







servlet处理请求和响应请求的过程

![image-20201201004022173](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201201004022173.png)

- 1.Tomcat将http请求文本接收并解析，然后封装成**HttpServletRequest**类型的**request对象**，所有的HTTP头数据读可以通过request对象调用对应的方法查询到。
- 2.Tomcat同时会把响应的信息封装为**HttpServletResponse**类型的**response对象**，通过设置response属性就可以控制要输出到浏览器的内容，然后将response交给tomcat，tomcat就会将其变成响应文本的格式发送给浏览器。



## servlet在tomcat的运行流程

- 1.实现servlet api规范， 如 request  response cookie session
- 2.启动socker监听端口，等待http请求
- 3.获取http请求，分发给不同的协议处理器。http和https是不一样的
- 4.封装request请求，构造成HttpServletRequest,把在socker中获取的用户请求字节流转java对象，构造httpResponse。
- 5.调用(若未创建，则先加载)servlet，调用init初始化，执行servlet.service()方法。
- 6.为httpResponse添加header等头部信息。
- 7.scoker回写流，返回满足http协议格式的数据给浏览器
- 8.实现JSP语法分析器，JSP标记解释器。JSP servlet实现和渲染引擎
- 9.线程池管理，创建线程池，并为每个请求分配线程。



# 3.入门IDEA中创建javaweb 并启动



- 1.首先新创建一个模块

![image-20201201005018079](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201201005018079.png)



- 2.目录结构

![image-20201201005126542](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201201005126542.png)



- 3.模块配置文件



![image-20201201005518802](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201201005518802.png)



![image-20201201005446008](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201201005446008.png)



- 4.工程tomcat配置文件

![image-20201201005819651](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201201005819651.png)



- 5.启动即可 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <welcome-file-list>
        <welcome-file>index.jsp</welcome-file>
    </welcome-file-list>
    
  
</web-app>
```

![image-20201201005910542](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201201005910542.png)



## 细节注意

> 工程路径和项目名称尽量保持一致，

![image-20201201010224497](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201201010224497.png)



> 修改端口号



> 修改资源热部署
>
> 修改静态资源不需要重启
>
> on update action 选  redeploy

![image-20201201010421143](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201201010421143.png)