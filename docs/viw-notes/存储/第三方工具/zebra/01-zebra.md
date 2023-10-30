[TOC]


# 1.zebra介绍

zebra基于数据库客户端代理实现的数据库中间件
主流的数据库中间件代理分为：服务端代理，客户端代理
![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20231030194540.png)

服务端代理(proxy：代理数据库)中： 我们独立部署一个代理服务，这个代理服务背后管理多个数据库实例。而在应用中，我们通过一个普通的数据源(c3p0、druid、dbcp等)与代理服务器建立连接，所有的sql操作语句都是发送给这个代理，由这个代理去操作底层数据库，得到结果并返回给应用。在这种方案下，分库分表和读写分离的逻辑对开发人员是完全透明的。

客户端代理(datasource：代理数据源)： 应用程序需要使用一个特定的数据源，其作用是代理，内部管理了多个普通的数据源(c3p0、druid、dbcp等)，每个普通数据源各自与不同的库建立连接。应用程序产生的sql交给数据源代理进行处理，数据源内部对sql进行必要的操作，如sql改写等，然后交给各个普通的数据源去执行，将得到的结果进行合并，返回给应用。数据源代理通常也实现了JDBC规范定义的API，因此能够直接与orm框架整合。在这种方案下，用户的代码需要修改，使用这个代理的数据源，而不是直接使用c3p0、druid、dbcp这样的连接池。

> 服务端：服务端是执行CRUD操作的部分，它把SQL语句翻译成对内存和文件的操作23。服务端用于持久化数据并为其提供查询接口(SQL)。

> 客户端：客户端是发送CRUD操作的部分，它对数据库/表进行操作，而不是直接对文件进行操作23。客户端的目的是让你使用那个查询接口

数据源
![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20231030195159.png)


zebra不是直接和mysql-server交互，而是在数据库连接池上包了一层
![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20231030204450.png)


zebra的优点

Zebra客户端做读写分离、分库分表、打点、监控
RDS、DBA管理平台维护配置信息
Lion监听配置更新，通知客户端生效变更
MHA保障主库的高可用性
zebra-monitor(自研)保障丛库的高可用性

> MHA：主库高可用

主库高可用
![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20231030205137.png)

从库高可用
![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20231030205217.png)

常见的数据库中间件
![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20231030204805.png)


# 2.zebra的架构图

![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20231030204920.png)

最上层是ShardDataSource，用于进行分库分表。
中间一层是GroupDataSource，主要用于读写分离。
最下一层是SingleDataSource，主要用于和mysql集群中的单个mysql实例直接建立连接。支持6种连接池：dbcp、dbcp2、druid、tomcat-jdbc、c3p0，hikaricp。


## 2.1核心组件

### zebra-client

- filter链,spi扩展
- dataSource
- zebra-dao
- zdbra-ds-monitor
- trace打点


### zebra-api

GroupDataSource

GroupDataSource初始化时，会从配置平台读取所有数据库节点的配置信息。然后依据这些信息来创建两种类型的代理数据源d FailOverDataSource和LoadBalancedDataSource。其中，FailOverDataSource用来连接和管理主节点故障的拒绝写等。LoadBalancedDataSource则是用来连接所有从节点，并负责从节点读流量的路由。


读写分离策略
![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20231030210245.png)

路由策略
![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20231030210026.png)


ShardDataSource

ShardDataSource中的DefaultShardRouter负责分库分表：
RouterRule路由规则：根据分库分表规则和运行时参数，计算出应该到的库名和表名
SQLRewrite负责RouterRule计算的结果改写SQL，形成带有物理表名的SQL


### zebra-dao


异步化
MyBatis中每个DAO都对应一个MapperProxy，zebra-dao中则是AsyncMapperProxy。

分页PageInterceptor

sql解析和改写
使用来Druid的 Sql Parser。


参考：
https://dorgenjones.github.io/2019/01/03/database/zbera/zebra/
https://community.sphere-ex.com/t/topic/601
https://codeantenna.com/a/2QHjsWFBa0/

