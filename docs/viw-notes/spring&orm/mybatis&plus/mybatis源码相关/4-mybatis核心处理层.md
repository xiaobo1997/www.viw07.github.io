[TOC]







# mybatis核心处理组件



## 模块

1. 初始化
2. sqlNode和sqlSource
3. ResultSetHandler
4. StatementHandler
5. Executor组件
6. sqlsession组件

## 核心处理类



**mybatis初始化，解析配置文件相关的类**

- Configuration：核心中核心配置类，无论执行什么操作，都有他的存在。
- XMLConfigBuilder：解析mybatis-config.xml。
- XMLMapperBuilder：解析mapper.xml。
- XMLStatementBuilder：解析mapper.xml中的Sql语句。
- MapperAnnotationBuilder：解析Mapper接口的注解信息。
- MapperBuilderAssistant：解析mapper.xml的辅助工具类。
- MappedStatement：描述<select|update|insert|delete>或者@Select、@Update等注解配置的SQL信息。
- MapperRegistry：mapper注册器，用于将所有的mapper接口添加到内存中(缓存)。

**会话相关**



- sqlsessionfactorybuilder创建sqlsessionfactory工厂
- DefaultSqlSessionFactory获取SqlSession
- DefaultSqlSession默认的会话
- MapperProxyFactory：创建mapper接口的代理类工厂
- mapperproxy:mapper接口的代理类的回调

**executor**



- MapperMethod:执行SQL的描述信息，执行操作
- BoundSql:执行一次CRUD操作过程产生的中间数据的集中点
- Cache：缓存接口，自定义二级缓存实现这个接口
- BaseExecutor:基础执行器，
- CachingExecutor:二级缓存executor
- SimplExecutor:默认的executor
- PreparedStatementHandler：与PreparedStatement交互作用。
- PreparedStatement：预编译Sql执行。
- ResultSetHandler：将结果集resultSets转化成Java对象，（是一个接口）。
- DefaultResultSetHandler：默认的结果集处理器，实现ResultSetHandler接口。

**事务**

- TransactionIsolationLevel：事务隔离级别。（枚举类）。
- Transaction：定义事务抽象方法的接口。
- TransactionFactory：事务创建工厂，（是一个接口）。。
- JdbcTransactionFactory：创建JdbcTransaction工厂类。
- JdbcTransaction：JDBC的事务管理机制,就是利用java.sql.Connection对象完成对事务的提交。