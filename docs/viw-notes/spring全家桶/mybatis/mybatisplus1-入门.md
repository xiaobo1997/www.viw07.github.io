[TOC]



# mybatisplus



## 概念

### 为什么用mybatis

- **无侵入**：只做增强不做改变，引入它不会对现有工程产生影响，如丝般顺滑
- **损耗小**：启动即会自动注入基本 CURD，性能基本无损耗，直接面向对象操作
- **强大的 CRUD 操作**：内置通用 Mapper、通用 Service，仅仅通过少量配置即可实现单表大部分 CRUD 操作，更有强大的条件构造器，满足各类使用需求
- **支持 Lambda 形式调用**：通过 Lambda 表达式，方便的编写各类查询条件，无需再担心字段写错
- **支持多种数据库**：支持 MySQL、MariaDB、Oracle、DB2、H2、HSQL、SQLite、Postgre、SQLServer2005、SQLServer 等多种数据库
- **支持主键自动生成**：支持多达 4 种主键策略（内含分布式唯一 ID 生成器 - Sequence），可自由配置，完美解决主键问题
- **支持 XML 热加载**：Mapper 对应的 XML 支持热加载，对于简单的 CRUD 操作，甚至可以无 XML 启动
- **支持 ActiveRecord 模式**：支持 ActiveRecord 形式调用，实体类只需继承 Model 类即可进行强大的 CRUD 操作
- **支持自定义全局通用操作**：支持全局通用方法注入（ Write once, use anywhere ）
- **支持关键词自动转义**：支持数据库关键词（order、key......）自动转义，还可自定义关键词
- **内置代码生成器**：采用代码或者 Maven 插件可快速生成 Mapper 、 Model 、 Service 、 Controller 层代码，支持模板引擎，更有超多自定义配置等您来使用
- **内置分页插件**：基于 MyBatis 物理分页，开发者无需关心具体操作，配置好插件之后，写分页等同于普通 List 查询
- **内置性能分析插件**：可输出 Sql 语句以及其执行时间，建议开发测试时启用该功能，能快速揪出慢查询
- **内置全局拦截插件**：提供全表 delete 、 update 操作智能分析阻断，也可自定义拦截规则，预防误操作
- **内置 Sql 注入剥离器**：支持 Sql 注入剥离，有效预防 Sql 注入攻击



## 使用

1. 创建表

   ```sql
   create table people
   (
       id    bigint      not null comment '主键ID'
           primary key,
       name  varchar(30) null comment '姓名',
       age   int         null comment '年龄',
       email varchar(50) null comment '邮箱'
   );
   
   
   ```

   

2. 创建springboot 项目![image-20201226153302968](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201226153302968.png)

   

3.引入依赖

```xml
   <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.0.5</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
```



4.idea安置lombok插件

![image-20201226160622192](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201226160622192.png)



5.实体类

```java
package com.viw.mybatispluslab1.domin;

import lombok.Data;

/**
 * @Author: xhb
 * @Date: 2020/12/26 16:24
 */
@Data
public class People {
    private Long id;
    private String name;
    private Integer age;
    private String email;
}

```

6.mapper

```java
package com.viw.mybatispluslab1.mapper;

import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.viw.mybatispluslab1.domin.People;
import org.springframework.stereotype.Repository;

/**
 * @Author: xhb
 * @Date: 2020/12/26 16:25
 */
@Repository
public interface PeopleMapper extends BaseMapper<People> {

}

```



7.application.properties

```properties
# 应用名称

#jdbc.driver=com.mysql.cj.jdbc.Driver
#jdbc.url=jdbc:mysql://localhost:3306/db_xmshop?useUnicode=true&characterEncoding=utf-8&useSSL=false&serverTimezone=UTC
#jdbc.username=root
#jdbc.password=root

spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/db_xmshop?serverTimezone=GMT%2B8
spring.datasource.username=root
spring.datasource.password=root
```



8.测试

```java
package com.viw.mybatispluslab1;

import com.viw.mybatispluslab1.domin.People;
import com.viw.mybatispluslab1.mapper.PeopleMapper;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import java.util.Arrays;
import java.util.List;

@SpringBootTest
public class MybatisplusLab1ApplicationTests {

    @Autowired
    private PeopleMapper peopleMapper;

    @Test
    public void contextLoads() {
        List<People> peopleList = peopleMapper.selectList(null);
        System.out.println(Arrays.asList(peopleList));
    }

}

```

9.日志

```properties
mybatis-plus.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl
```

10.测试结果

```
[[People(id=1, name=Jone, age=18, email=test1@baomidou.com), People(id=2, name=Jack, age=20, email=test2@baomidou.com), People(id=3, name=Tom, age=28, email=test3@baomidou.com)]]

```



包结构

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201226171501911.png" alt="image-20201226171501911" style="zoom:33%;" />



==总结==

1.使用lombok 注解 @Data 帮助我们编译出 get set 

2.mapper 继承 BaseMapper 接口

3.测试



### 添加操作

```java
peopleMapper.insert(people);
```

**MP自动生成id（主键）**



## mybatisplus 主键生成策略



1.自动增长

AUTO INCREMENT

2.uuid ，不方便排序

每一次生成随机

![image-20201226172321721](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201226172321721.png)

3.redis生成id

redis原子操作 +步长 实现

4.MP的自带策略

snowflake算法

> snowflake是Twitter开源的分布式ID生成算法，结果是一个long型的ID。其核心思想是：使用41bit作为毫秒数，10bit作为机器的ID（5个bit是数据中心，5个bit的机器ID），12bit作为毫秒内的流水号（意味着每个节点在每毫秒可以产生 4096 个 ID），最后还有一个符号位，永远是0。

![image-20201226173107288](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201226173107288.png)

**ID_WORKER**  数值类型

**ID_WORKER_STR**  字符串类型