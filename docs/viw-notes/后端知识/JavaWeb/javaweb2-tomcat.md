[TOC]



# tomcat



## 概念

> tomcat是一个轻量级的javaweb服务器（通过对jsp和servlet容器支持）
>
> 其他Web服务器还有 jboss  weblogic Websphere等

## 应用服务器和web服务器

> web服务器工作流程

![image-20201201012635737](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201201012635737.png)

## 版本兼容支持

> tomcat服务器和servlet版本支持

| **Servlet Spec** | **JSP Spec** | **EL Spec** | **WebSocket Spec** | **JASPIC Spec** | **Apache Tomcat version** | **Actual release revision** | **Supported Java Versions**             |
| :--------------- | :----------- | :---------- | :----------------- | :-------------- | :------------------------ | :-------------------------- | :-------------------------------------- |
| 4.0              | TBD (2.4?)   | TBD (3.1?)  | TBD (1.2?)         | 1.1             | 9.0.x                     | 9.0.0.M9 (alpha)            | 8 and later                             |
| 3.1              | 2.3          | 3.0         | 1.1                | 1.1             | 8.5.x                     | 8.5.4                       | 7 and later                             |
| 3.1              | 2.3          | 3.0         | 1.1                | N/A             | 8.0.x (superseded)        | 8.0.35 (superseded)         | 7 and later                             |
| 3.0              | 2.2          | 2.2         | 1.1                | N/A             | 7.0.x                     | 7.0.70                      | 6 and later (7 and later for WebSocket) |
| 2.5              | 2.1          | 2.1         | N/A                | N/A             | 6.0.x                     | 6.0.45                      | 5 and later                             |
| 2.4              | 2.0          | N/A         | N/A                | N/A             | 5.5.x (archived)          | 5.5.36 (archived)           | 1.4 and later                           |
| 2.3              | 1.2          | N/A         | N/A                | N/A             | 4.1.x (archived)          | 4.1.40 (archived)           | 1.3 and later                           |
| 2.2              | 1.1          | N/A         | N/A                | N/A             | 3.3.x (archived)          | 3.3.2 (archived)            | 1.1 and later                           |

```xml
1 <?xml version="1.0" encoding="UTF-8"?>  
2    
3 <web-app  
4         version="3.0"  
5         xmlns="http://java.sun.com/xml/ns/javaee"  
6         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
7         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd">  
8    
9 </web-app>
```

**web.xml 3.0**

## 安装使用

 百度

![image-20201130002121773](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201130002121773.png)

## 目录结构

![image-20201130002515934](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201130002515934.png)

![image-20201130002508942](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201130002508942.png)



## 启动和停止

> 点击 startup.bat启动

![image-20201130002718925](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201130002718925.png)

![image-20201130002724229](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201130002724229.png)

> bin下 cmd 命令行  `catalina run`



关闭：

> cmd  关闭执行的窗口

> cmd中 `ctrl+c`

> 点击 shotdown.bat



## 修改端口

server.xml中

![image-20201130003231198](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201130003231198.png)



## 部署工程

1.把web工程放入webapps就可以了

2./catalina/localhost下面 写配置文件

```java
<Context path="/hello" docBase="D:\xiaobo" reloadable="true" />
```

path 表示工程访问路径 可有可没有，没有就是你工程名

docBase:工程所在路径

3.**打开tomcat下conf/server.xml，在<Host> </Host>标签之间输入项目配置信息**

```java
<Context path="/WebProject" docBase="D:/WebProject" reloadable="true" />
```



## IDEA配置tomcat

![image-20201130010253060](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201130010253060.png)



## IDEA创建动态javaweb工程

![image-20201130010644266](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201130010644266.png)

![image-20201130010704377](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201130010704377.png)



### 工程目录介绍

![image-20201130010819155](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201130010819155.png)

### 导包

1.把jar包直接放入 lib目录即可

2.或者是 放到你相应的工程下面![image-20201130011305371](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201130011305371.png)

![image-20201130011431585](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201130011431585.png)

改下名字