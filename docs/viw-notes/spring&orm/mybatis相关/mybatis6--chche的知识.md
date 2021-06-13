[TOC]

# mybatis0--6--chche



## 概念

缓存在内存中临时存储数据，速度也快，减少了数据库的访问次数，减轻压力

mybatis缓存可以实现提高执行相同查询sql的效率， mybatis对于缓存实现了非常方便的配置和定制。

对于一些经常查询的sql，不经常修改的数据，不是特别重要的数据都适用存储到缓存中。

在mybatis中使用了两种缓存：

- 1.**本地缓存**或称一级缓存(lcoal cache)：默认情况下只有一级缓存开启，它是基于SqlSession级别的缓存，

- 2.**二级缓存**(second level cache)：它是基于namespace级别的缓存，需要手动配置开启，只需在你的sql映射文件中加入 `<cache/>`

> 缓存只作用于 cache 标签所在的映射文件中的语句。

## 本地缓存(一级缓存)

### 本地缓存的概念

- 一级缓存：（本地缓存）：是sqlSession级别的缓存。SqlSession对象维护了一个Map集合, 一级缓存是一直开启的；与数据库同一次会话期间查询到的数据会放在本地缓存中。以后如果需要获取相同的数据，直接从缓存中拿，没必要再去查询数据库；

### 四种失效情况



**证明使用的一级缓存：**

```java
 @Autowired
    EmployeeDao employeeDao  ;
    @Test
    @Transactional(rollbackFor = Throwable.class)
    public void TestA (){
            Employee empById = employeeDao.getEmpById(1);
            Employee empById2 = employeeDao.getEmpById(1);
            System.out.println(empById == empById2);
        }
```

可以发现sql语句只执行了一次，后面那次查询的数据直接从缓存中取



- 一级缓存失效情况（没有使用到当前一级缓存的情况，效果就是，还需要再向数据库发出查询）：

  - 1、sqlSession不同。

  - 2、sqlSession相同，查询条件不同.(当前一级缓存中还没有这个数据)

  - 3、sqlSession相同，两次查询之间执行了增删改操作(这次增删改可能对当前数据有影响)

  - 4、sqlSession相同，手动清除了一级缓存（缓存清空）`flushCache=true`刷新缓存

  - ```xml
     <select id="getEmpById" resultType="com.study.mybatis.bean.Employee" flushCache="true">
            select * from Employee where id =#{id}
        </select>
    ```

    ![image-20200829231235992](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200829231235992.png)

- 当在一个缓存作用域中发生了update、insert、delete 动作后，将会触发缓存失效，下一次查询将命中数据库，从而保证不会查到脏数据。



##  二级缓存(全局缓存)

### 概念：

二级缓存：（全局缓存）：基于namespace级别的缓存：一个namespace对应一个二级缓存：

写在哪个namespace下 才会对该namespace下生效

工作机制：

- 1、一个会话，查询一条数据，这个数据就会被放在当前会话的一级缓存中；

- 2、如果会话关闭；一级缓存中的数据会被保存到二级缓存中；新的会话查询信息，就可以参照二级缓存中的内容；
- 3,sqlSession-->EmployeeMapper-->Employee，DepartmentMapper===>Department
  - 不同namespace查出的数据会放在自己对应的缓存中（map）
  - 效果：
    - 数据会从二级缓存中获取,查出的数据都会被默认先放在一级缓存中。只有会话提交或者关闭以后，一级缓存中的数据才会转移到二级缓存中
  - 使用：
    - 1）、开启全局二级缓存配置：`<setting name="cacheEnabled" value="true"/>`
    - 2）、去mapper.xml中配置使用二级缓存：`<cache></cache>`
    - 3）、POJO需要实现序列化接口![image-20200829233312907](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200829233312907.png)

### 使用：

1.mybatis-cinfig.xml中 

```xml
   <settings>
        <setting name="cacheEnabled" value="true"/>
    </settings>
```

2.因为是namespace层的好处 所以要在相应namespace中 加入 `<cache></cache>`

```xml
 <cache  eviction="FIFO" flushInterval="60000" readOnly="false" size="1024"></cache>
    <!--
	eviction:缓存的回收策略：
		• LRU – 最近最少使用的：移除最长时间不被使用的对象。
		• FIFO – 先进先出：按对象进入缓存的顺序来移除它们。
		• SOFT – 软引用：移除基于垃圾回收器状态和软引用规则的对象。
		• WEAK – 弱引用：更积极地移除基于垃圾收集器状态和弱引用规则的对象。
		• 默认的是 LRU。
	flushInterval：缓存刷新间隔
		缓存多长时间清空一次，默认不清空，设置一个毫秒值
	readOnly:是否只读：
		true：只读；mybatis认为所有从缓存中获取数据的操作都是只读操作，不会修改数据。
				 mybatis为了加快获取速度，直接就会将数据在缓存中的引用交给用户。不安全，速度快
		false：非只读：mybatis觉得获取的数据可能会被修改。
				mybatis会利用序列化&反序列的技术克隆一份新的数据给你。安全，速度慢
	size：缓存存放多少元素；
	type=""：指定自定义缓存的全类名；
			实现Cache接口即可；
	-->
```

3.测试

```java

  @Test
    public void TestB(){
        employeeDao.getEmpById(1);
        employeeDao.getEmpById(1);
    }
```

4. console

![image-20200829234212552](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200829234212552.png)

​	可以看到 第一次缓存命中率 0.0 第二次 0.5

5.如果在namespace中关闭二级缓存 效果如下

![image-20200829234627001](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200829234627001.png)

## 缓存相关标签属性和设置

### 概念：

1）、cacheEnabled=true：false：关闭缓存（二级缓存关闭）(一级缓存一直可用的)

2）、每个select标签都有useCache="true"：

false：不使用缓存（一级缓存依然使用，二级缓存不使用）

3）、【每个增删改标签的：flushCache="true"：（一级二级都会清除）】

	- 增删改执行完成后就会清楚缓存；
	- 测试：flushCache="true"：一级缓存就清空了；二级也会被清除；
	- 查询标签：flushCache="false"：
	- 如果flushCache=true;每次查询之后都会清空缓存；缓存是没有被使用的；

4）、sqlSession.clearCache();只是清除当前session的一级缓存；

5）、localCacheScope：本地缓存作用域：（一级缓存SESSION）；当前会话的所有数据保存在会话缓存中；

STATEMENT：可以禁用一级缓存；



## 缓存原理

### 作用流程图

![image-20200830010040971](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200830010040971.png)

cache接口

![image-20200830010326206](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200830010326206.png)

**缓存顺序**

通过mybatis发起的查询，作用顺序为：二级缓存->一级缓存->数据库 ，其中任何一个环节查到不为空的数据，都将直接返回结果

##  整合第三方缓存资源

mybatis 提高了一个缓存标准接口 `package org.apache.ibatis.cache项下的Cache类` 让扩展功能

常见 把缓存放入 redis   ehcache 等



### 环境配置

pom.xml导入相关依赖即可

```xml
  <!-- https://mvnrepository.com/artifact/org.ehcache/ehcache -->
        <dependency>
            <groupId>org.ehcache</groupId>
            <artifactId>ehcache</artifactId>
            <version>3.7.0</version>
        </dependency>

        <dependency>
            <groupId>org.mybatis.caches</groupId>
            <artifactId>mybatis-ehcache</artifactId>
            <version>1.1.0</version>
        </dependency>
```



> 注意如果你的ehcache.xml URL报错。那么可能是没联网添加，如下添加即可![image-20200830013026493](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200830013026493.png)

### 使用：

####  1.相应的mapper下namespace中 替换为ehcache的缓存标签

```xml

    <cache type="org.mybatis.caches.ehcache.EhcacheCache"></cache>
<!--     <cache  eviction="FIFO" flushInterval="60000" readOnly="false" size="1024"></cache>-->
```



#### 2.类路径下ehcache.xml

```xml

<?xml version="1.0" encoding="UTF-8"?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="http://ehcache.org/ehcache.xsd">
    <!-- 磁盘保存路径 -->
    <diskStore path="E:\mybatisCacheSpace\ehcache" />

    <defaultCache
            maxElementsInMemory="10000"
            maxElementsOnDisk="10000000"
            eternal="false"
            overflowToDisk="true"
            timeToIdleSeconds="120"
            timeToLiveSeconds="120"
            diskExpiryThreadIntervalSeconds="120"
            memoryStoreEvictionPolicy="LRU">
    </defaultCache>
        </ehcache>
        <!--
        属性说明：
        l diskStore：指定数据在磁盘中的存储位置。
        l defaultCache：当借助CacheManager.add("demoCache")创建Cache时，EhCache便会采用<defalutCache/>指定的的管理策略

        以下属性是必须的：
        l maxElementsInMemory - 在内存中缓存的element的最大数目
        l maxElementsOnDisk - 在磁盘上缓存的element的最大数目，若是0表示无穷大
        l eternal - 设定缓存的elements是否永远不过期。如果为true，则缓存的数据始终有效，如果为false那么还要根据timeToIdleSeconds，timeToLiveSeconds判断
        l overflowToDisk - 设定当内存缓存溢出的时候是否将过期的element缓存到磁盘上

        以下属性是可选的：
        l timeToIdleSeconds - 当缓存在EhCache中的数据前后两次访问的时间超过timeToIdleSeconds的属性取值时，这些数据便会删除，默认值是0,也就是可闲置时间无穷大
        l timeToLiveSeconds - 缓存element的有效生命期，默认是0.,也就是element存活时间无穷大
         diskSpoolBufferSizeMB 这个参数设置DiskStore(磁盘缓存)的缓存区大小.默认是30MB.每个Cache都应该有自己的一个缓冲区.
        l diskPersistent - 在VM重启的时候是否启用磁盘保存EhCache中的数据，默认是false。
        l diskExpiryThreadIntervalSeconds - 磁盘缓存的清理线程运行间隔，默认是120秒。每个120s，相应的线程会进行一次EhCache中数据的清理工作
        l memoryStoreEvictionPolicy - 当内存缓存达到最大，有新的element加入的时候， 移除缓存中element的策略。默认是LRU（最近最少使用），可选的有LFU（最不常使用）和FIFO（先进先出）
         -->
```

### 测试

保证我们的二级缓存是开启开用的

```java
    @Test
    public void TestB(){
        employeeDao.getEmpById(1);
      //  employeeDao.addEmp(new Employee(null,"a6","a6@163.com","0",new Department(1)));
        employeeDao.getEmpById(1);
    }

```



![image-20200830013815533](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200830013815533.png)

### 流程

![image-20200830014137478](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200830014137478.png)