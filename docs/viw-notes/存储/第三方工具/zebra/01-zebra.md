[TOC]


# 1.zebra介绍

zebra基于数据库客户端代理实现的数据库中间件
主流的数据库中间件代理分为：服务端代理，客户端代理
![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20231030194540.png)

服务端代理(proxy：代理数据库)中： 我们独立部署一个代理服务，这个代理服务背后管理多个数据库实例。而在应用中，我们通过一个普通的数据源(c3p0、druid、dbcp等)与代理服务器建立连接，所有的sql操作语句都是发送给这个代理，由这个代理去操作底层数据库，得到结果并返回给应用。在这种方案下，分库分表和读写分离的逻辑对开发人员是完全透明的。

客户端代理(datasource：代理数据源)： 应用程序需要使用一个特定的数据源，其作用是代理，内部管理了多个普通的数据源(c3p0、druid、dbcp等)，每个普通数据源各自与不同的库建立连接。应用程序产生的sql交给数据源代理进行处理，数据源内部对sql进行必要的操作，如sql改写等，然后交给各个普通的数据源去执行，将得到的结果进行合并，返回给应用。数据源代理通常也实现了JDBC规范定义的API，因此能够直接与orm框架整合。在这种方案下，用户的代码需要修改，使用这个代理的数据源，而不是直接使用c3p0、druid、dbcp这样的连接池。


数据源
![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20231030195159.png)


zebra不是直接和mysql-server交互，而是在数据库连接池上包了一层



# 2.zebra的架构图


## 2.1核心组件

### zebra-client

### zebra-api


### zebra-dao


