[TOC]



# 解决创建骨架过慢的问题 

增加键值对

archetypeCatalog
internal

![image-20200901013446861](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200901013446861.png)

# 1.maven配置和使用

![image-20200910002208280](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910002208280.png)



> 创建一个新项目
>
> 下面是使用骨架帮助我们快速构建一个应用

![image-20200910002739712](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910002739712.png)

> 三个部分标识 
>
> 1.组  项目标识 一般是com.xxx 如 com.baidu 
>
> 2.项目名 你的项目名 如 mall
>
> 3.版本
>
> parent 是父工程名，依赖的父工程是哪个

![image-20200910002613437](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910002613437.png)

> maven项目的结构是统一的

![image-20200910003026892](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910003026892.png)

![image-20200910003329916](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910003329916.png)

> 依赖关系
>
> 依赖查找
>
> 

![image-20200910003547586](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910003547586.png)

![image-20200910003630588](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910003630588.png)



## 常用命令

- clear 清理
- compile 编译
- test  测试
- package 打包
- install 安装

# 2. web项目构建-tomcat部署

> 打包方式

```xml
 <parent>
        <artifactId>springmvc</artifactId>
        <groupId>com.study</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>springmvc-my-view</artifactId>
    <packaging>war</packaging>

    <name>springmvc-my-view Maven Webapp</name>
    <!-- FIXME change it to the project's website -->
    <url>http://www.example.com</url>
```

> web基本依赖

![image-20200910004048131](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910004048131.png)

```xml
   <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>servlet-api</artifactId>
      <version>2.5</version>
      <scope>provided</scope>
    </dependency>

    <dependency>
      <groupId>javax.servlet.jsp</groupId>
      <artifactId>jsp-api</artifactId>
      <version>2.0</version>
      <scope>provided</scope>
    </dependency>

    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>jstl</artifactId>
      <version>1.2</version>
    </dependency>
```

> webapp目录

![image-20200910004254715](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910004254715.png)

> 添加 tomcar server

![image-20200910004410501](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910004410501.png)

> 部署  添加 artifacts

![image-20200910004502292](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910004502292.png)

![image-20200910004619652](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910004619652.png)

## 生命周期

> 依赖在哪个过程有效

![image-20200910004804052](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910004804052.png)

![image-20200910004947558](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910004947558.png)



# 3.依赖

## 范围

> 依赖范围

![image-20200910005402029](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910005402029.png)



## 冲突和解决



> 依赖冲突

![image-20200910005703155](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910005703155.png)

> 解决依赖冲突

![image-20200910200644396](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910200644396.png)

### 1. 第一声明

>4.1.3版本声明在 5.0.5 前面，  5.0的不生效，

![image-20200910200914297](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910200914297.png)

### 2. 路径优先



> 简单来说 就是当两个依赖冲突时，谁匹配度高谁生效，

![image-20200910201110497](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910201110497.png)

![image-20200910201057737](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910201057737.png)

### 3.排除依赖

> 可以用 exclusions 排除依赖

```xml
 <exclusions>
        <exclusion>
          <groupId>org.springframework</groupId>
          <artifactId>spring-beans</artifactId>
        </exclusion>
      </exclusions>
```

![image-20200910201505242](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910201505242.png)

![image-20200910201518562](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910201518562.png)



### 4.版本锁定

![image-20200910201648650](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910201648650.png)

> 使用方式

![image-20200910201717265](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910201717265.png)

![image-20200910201834354](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910201834354.png)

![image-20200910201842243](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910201842243.png)

> 或者是 

```xml
  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--编译版本 1.8-->
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <spring.version>5.0.2.RELEASE</spring.version>
  </properties>
```



# 4. 分模块构建maven工程

## 

## 继承和拆分

> 拆分方式
>
> 把公共代码和配置提取到父工程中进行统一管理和配置

![image-20200910202136937](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910202136937.png)

> 继承的概念

![image-20200910202355914](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910202355914.png)



1.创建父工程

![image-20200910202647970](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910202647970.png)

2.编写 父pom.xml

```xml
 <parent>
        <artifactId>springmvc</artifactId>
        <groupId>com.study</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>
    <!--父工程打包方式为pom.xml-->
    <packaging>pom</packaging>
    <artifactId>mall</artifactId>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
            <version>2.2.5.RELEASE</version>
        </dependency>
    </dependencies>
```



3. 创建子工程

   > 继承父工程 mall

![image-20200910203052426](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910203052426.png)



4. 父工程和子过程的区别

![image-20200910203241916](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910203241916.png)

5.父工程和子工程的依赖

![image-20200910203323186](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910203323186.png)



## 聚合

> 概念

![image-20200910203525304](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910203525304.png)



**聚合和继承没有关联关系，是不同的概念，不继承也可以聚合，**

> 在父工程pom.xml编写 `module`
>
> 不继承也可以聚合

```xml
    <parent>
        <artifactId>springmvc</artifactId>
        <groupId>com.study</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>
    <!--父工程打包方式为pom.xml-->
    <packaging>pom</packaging>
    <artifactId>mall</artifactId>

    <modules>
        <module>../mall-controller</module>
    </modules>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
            <version>2.2.5.RELEASE</version>
        </dependency>
    </dependencies>
```

```xml
 <modules>
        <module>../mall-controller</module>
        <module>../springmvc-my-view</module>
    </modules>
```



> 打包父工程就可以了，
>
> 下面是输入 maven命令 和效果的作用范围

![image-20200910204249203](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910204249203.png)

![image-20200910204413144](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910204413144.png)



## 案例



> 创建 父工程和子工程

![image-20200910205103928](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910205103928.png)

> 继承和聚合

![image-20200910205403120](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910205403120.png)

> 父工程一般只是做版本锁定
>
> 子工程需要什么 依赖 在自己的 子工程下导依赖，就不需指定version

> 传递依赖 ，让service 依赖dao 
>
> 那么dao中的依赖 也就传递给了 service了

![image-20200910210612793](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910210612793.png)

# 5部署

> 部署只需要部署web工程即可
>
> 同一个父工程下的子工程是被打成 jar包在 web工程下的lib下面

# 6 maven私服(后续补充)

> 将本地jar包上传到私服

![image-20200910212055943](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910212055943.png)

> 仓库类型
>
> 所在磁盘目录  xxx\sonatype-work\nexus\storge\

![image-20200910212403952](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910212403952.png)

> 将项目发布到私服

![image-20200910212551606](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910212551606.png)

![image-20200910212606174](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200910212606174.png)



# 7.检查mvn配置情况



```java
//检查配置情况
mvn help:effective-settings
//检查setting.xml的读取顺序
mvn -X
```

