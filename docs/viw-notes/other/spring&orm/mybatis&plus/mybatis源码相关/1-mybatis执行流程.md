[TOC]



# 认识mybatis源码

## 流程



### **mybatis初始化启动**

![](https://user-gold-cdn.xitu.io/2019/10/30/16e1cc3e6a150dc2?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

1. SqlSessionFactoryBuilder调用build()生成得到一个SqlSessionFactory对象，
2. SqlSessionFactory创建得到SqlSession对象
3. 通过SqlSession得到Mapper对象
4. 通过MapperProxy调用Mapper中的增删改查方法



### 工作流程



1. 读取mybatis配置文件mybatis-config.xml
2. 加载映射文件 xxxMapper.xml
3. 构建SqlSessionFactory会话工厂
4. 创建SqlSession会话对象（包含了执行sql的所有方法）
5. Executor执行器,mybatis底层定义了一个执行器接口来操作数据库，根据sqlsession参数动态的生成执行的sql（委托来执行sqlsession中的方法）
6. mapperStatement对象，在executor接口中定义有mapperstatement类型的参数，这个是对要映射sql的id，参数的信息封装
7. 输入参数映射，输入类型可以是map,list等集合，也可以是POJO类型和基本数据类型。相当于JDBC中的设置preparedStatement参数的过程
8. 输出结果映射。输出结果可以是map，list 也可以是基本数据类型和 POJO，相当于JDBC中对结果集的解析

==mybatis在解析配置文件并初始化是使用了工厂模式SqlSessionFactory来创建sqlsession会话,构建者模式SqlSessionFactoryBuilder构建configuration对象==







## mybatis结构

### 分层

![image-20201223012209948](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201223012209948.png)

### 四大核心对象



1. sqlsession会话对象，这个对象里面包含了执行sql的方法，获取映射器，管理事务
2. Executor接口，根据sqlsession传递的参数动态的生成需要执行的sql，负责缓存的维护，相当于JDBC中preparestatement
3. MapperStatement对象，这个是对映射sql的封装，存储要映射sql语句的id，参数信息
4. resultHandler对象，处理返回结果集，得到这个想要的数据格式，可以自定义



## mybatis执行sql的流程



### 流程



图来自 `https://blog.csdn.net/liuqi199707/article/details/109712299?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_title-2&spm=1001.2101.3001.4242`

![](https://img-blog.csdnimg.cn/20201117133844881.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdXFpMTk5NzA3,size_16,color_FFFFFF,t_70#pic_center)

1. 首先发起一条sql查询请求
2. 通过sqlSessionFactory的openSession()得到SqlSession实例，默认的DefaultSqlSession
3. JDK动态代理，实例化MapperProxy创建一个MapperProxyFactory代理类
4. 获取mapper代理类实例，然后回调mapper中的方法并执行，并且把得到的执行器executor缓存到MapperProxy  methodcache集合中,然后通过全限定类名回去mapperstatement实例方法执行的sql对象，入参信息和返回类型
5. 默认是开启缓存的，如果指定了缓存就先去查缓存中的，没有就去调用baseexecutor查询，
6. 如果当前有缓存记录 直接返回结果，没有就发起一级缓存
7. 执行子类executor发查询方法处理器（根据statementType选择sql执行器），![image-20201224004504617](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201224004504617.png)
8. 默认情况使用预编译executor，PrepareStatementHadnler,
9. 执行sql
10. 封装返回结果集











参考`https://juejin.cn/post/6844903984352722951`