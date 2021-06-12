

[TOC]



# 1.全局配置文件

mtbatis有两大配置文件，一个是全局配置文件，一个是映射文件

- 全局配置文件：包含数据库 事务管理 等等信息
- 映射文件：sql映射信息

## 1.1 dtd

dtd 约束

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
```

## 1.2 properties文件

作用：引入外部资源

resource:引入类路径下的资源，resource项下
url:网络连接下的资源

```
<properties url="xxx"></properties>
    <properties resource="mybatis.xxx"></properties>
```

**取值用法：** 比如我们有一个jdbc.properties，存我们数据库连接信息 `${jdbc.driver}` `${jdbc.url}`

## 1.3 settings运行时行为配置

mybatis 有很多重要的运行时配置项参数，比如 开启缓存，开启驼峰命名 等等

**标签为：**
name:所要设置项参数的名字
value ： 设置项参数的值 如数字 ，true or false

```
  <settings>
        <setting name="xxx" value="xx"/>
    </settings>
```

如驼峰命名

```
  <settings>
        <setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>
```

## 1.4 typeAliases 别名处理器

相当于就是 把 我们的java domin中 的对象字段 起一个别名

后续使用 直接使用别名即可，就不要写java bean的全类名了

**标签:**

- type:指定要起的类型全类名 不写 alias ，自动默认别名 首字母小写
- alias : 指定新别名

```
<typeAliases>
        <typeAlias  alias="xxx" type="xxx.bean.xxxx"/>
    </typeAliases>
```

**如下:**

```
<typeAliases>
        <typeAlias  alias="Author" type="domein.blog.Author"/>
    </typeAliases>
```

**批量起别名:**

- name: 指定包名(为项下的所有类起个首字母小写的默认别名)

```
<typeAliases>
        <!--<typeAlias  alias="Author" type="domein.blog.Author"/>-->
        <package name="com.study.mybatis.domin"/>
    </typeAliases>
```

**当前类指定别名：**

```
/**
 * @Author: xiaobo
 * @Date: 2020/8/24 22:31
 */
@Alias("book11")
public class book {
    private  String name;
}
```

**有很多Java中的数据类型 mybatis已经为我们起了别名**

如：

| _byte | byte    |
| :---- | :------ |
| _long | long    |
| int   | Integer |

基本类型别名是 前面加了个下划线

包装类 的别名是 基本数据类型

> 注意：别名不区分大小写

## 1.5 typeHandlers 类型处理器

作用： java bean 对象类型和数据库类型的 桥梁

**比如:**

把java中 的string 保存到数据库时所兼容的 varchar 等等

把 数据库类型 Integer 转换 java 的 int integer

**注意**：

> 在jdk 1.8 添加JSR-310 API 对 Data and Time API 已经实现了 JSR310规范
> 我们可以直接编写 各种日期时间类型处理器

## 1.6 plugins 插件

mybatis 中，我们可以用插件对 sql进行拦截,主要使用的是 动态代理

主要是对下面四大对象和方法

- Executor(执行器，可以通过自定义executor和方法达到我们需要的业务效果)
- ParameterHandler(参数处理器，设置sql预编译参数等)
- ResultSetHandler(结果处理器，获取结果被封装java对象等操作)
- statementHandler(sql语句处理器 )

## 1.7 environments 运行环境

environments标签 可以配多个环境

environments 标签必须有两个子标签： 1.dataSource 2.transactionManager

**标签：**

```
   <!--配置环境  default 动态的为我们切换 开发环境 测试环境等-->
    <environments default="mysql">
        <!--配置mysql的环境,id为当前环境的唯一标识-->
        <environment id="mysql">
            <!--配置事务的类型 type 有两种 JDBC(JDBC事物  commit  rollback)  MANAGED(J2EE 应用程序 context来控制事务) 还可以自定义实现DataSourceFactory-->
            <transactionManager type="JDBC"/>
            <!--配置数据源（连接池）,因为创建连接和销毁会浪费资源 支持 UNPOOLED（不使用连接池）  POOLED(使用)  JNDI- 三种
           POOLED mybatis自带的连接池 -->
            <dataSource type="POOLED"> 
                <!--配置连接数据库的4个基本信息 -->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/eesy_mybatis"/>
                <property name="username" value="root"/>
                <property name="password" value="1234"/>
            </dataSource>
        </environment>
    </environments>
```

## 1.8 databaseIdProvider 多数据库支持

作用：根据不同的数据库类型标识，执行对应的sql

**标签**：

- type=”DB_VENDOR” 获取数据库产商标识

如

```
<databaseIdProvider type="DB_VENDOR">
        <property name="DB2" value="db2"/>
        <property name="Oracle" value="oracle"/>
         <property name="MySQL" value="mysql"/>
    </databaseIdProvider>
```

**步骤：**

1.在mybatis-config.xml引入 标识 标签

1. 指定databaseId （指明下面sql在mysql环境执行）

```
<mapper namespace="Emp">
<select id="" databaseId="mysql"></select>
</mapper>
```

**注意：**

不同的sql 导不同的驱动

## 1.9 mappers sql映射注册

**作用：**

将sql 映射 注册到 全局配置文件中

**标签：**

```
<mappers>
<mapper resource="xxx/XxxXxxMapper.xml"></mapper>

</mappers>
```

**如：**

```
<mappers>
<mapper resource="com/study/mapper/StudentMapper.xml"/> <!-- mapper配置文件的路径 -->
<mappers/>
```

mapper 有三种属性 分别是：

- resource 引入类连接下的sql 映射文件
- url ： 网络资源或者 磁盘路径 file://var/mappers/xxxMapper.xml
- class：注册接口，使用接口的全类名，如 com.study.mybatis01.dao.BookMapper
  - 1.有sql映射文件需要注意把 EmpMapper.java 和EmpMapper.xml 在同一package下
  - 2.没有sql映射文件，所有sql利用注解 写在接口上
  - 一般情况 重要的接口我们写映射文件， 不重要dao接口 注解 解决

**批量注册**：

## 1.10 总结

注意 mybatis-config.xml 中标签的编写顺序