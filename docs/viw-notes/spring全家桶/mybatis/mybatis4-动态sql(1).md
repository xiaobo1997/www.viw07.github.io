[TOC]

# 1.Dynamic SQL(动态sql)—mybatis的灵魂

## 概念和基本讲解

动态 SQL 是 MyBatis 的强大特性之一。如果你使用过 JDBC 或其它类似的框架，你应该能理解根据不同条件拼接 SQL 语句有多痛苦，例如拼接时要确保不能忘记添加必要的空格，还要注意去掉列表最后一个列名的逗号。利用动态 SQL，可以彻底摆脱这种痛苦。

使用动态 SQL 并非一件易事，但借助可用于任何 SQL 映射语句中的强大的动态 SQL 语言，MyBatis 显著地提升了这一特性的易用性。

如果你之前用过 JSTL 或任何基于类 XML 语言的文本处理器，你对动态 SQL 元素可能会感觉似曾相识。在 MyBatis 之前的版本中，需要花时间了解大量的元素。借助功能强大的基于 OGNL 的表达式，MyBatis 3 替换了之前的大部分元素，大大精简了元素种类，现在要学习的元素种类比原来的一半还要少。

**主要标签：**

- if
- choose (when, otherwise)
- trim (where, set)
- foreach

## 基本环境

pom.xml

```xml
 <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.1.2</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
```

XML

Copy

bean实体类：

dao中的mapper：

resource项下的 全局配置mybatis-config.xml 和 xxxMapper.xml映射文件：

application.properties中的 各个配置：

打印sql日志：
`mybatis.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl`

## 1.1 if_判断语句 & OGNL

### 遇到的一个小错误

错误： mybatis 报一个绑定错误 ，和load application faild

解决： 因为我把我的dao项下的 employee 的dao 起名EmpDao 我改成 EmployeeDao 就好了

### if_基本使用按条件拼接sql

**mapper:**

```sql
 <!-- if -->
    <select id="getEmpsByConditionIf" resultType="com.study.mybatis.bean.Employee">
        select * from employee
        <!-- where -->
        <where>
            <!-- test：判断表达式（OGNL表达式 github搜搜就知道了）
                    c:if  test
             从参数中取值进行判断
             遇见特殊符号应该去写转义字符：
             &&：会报错
             -->
            <!--id不等于空 才拼接下面的id=#{id} id是参数中的值-->
            <if test="id!=null">
                id=#{id}
            </if>

            <if test="lastName !=null and  lastName!= '' ">
                and lastName like #{lastName}
            </if>
            <!--email 是string  trim() 截取 不为null 表明有值 -->
            <if test="email!=null and email.trim()!="" ">
                and email =#{email}
            </if>
        <!-- ognl会进行字符串与数字的转换判断  "0"==0 -->
            <if test="gender == 0  or gender == 1">
                and gender = #{gender}
            </if>

        </where>

    </select>
```

SQL

Copy

**dao:**

```java
    public List<Employee> getEmpsByConditionIf(Employee employee);
```

Java

Copy

**测试：**

```java
  @Autowired
    EmployeeDao empDao;
    @Test
    public void contextLoads() {
        Employee employee = new Employee(1,"%1%","%163.com%",null);
        List<Employee> employeeList  = empDao.getEmpsByConditionIf(employee);
        System.out.println(Arrays.asList(employeeList));
    }
```

Java

Copy

**console:**

```java
==>  Preparing: select * from employee WHERE id=? and lastName like ? and email =? 
==> Parameters: 1(Integer), %1%(String), %163.com%(String)
<==      Total: 0
```

Java

Copy

### 总结和注意 ：

使用`if`标签达到了我们的要求 按照条件 拼接sql， 我们给gender传 null， 就没有拼接上我们的gender条件

> 注意转义字符的使用，有些是需要转义才能跑sql

## 1.2 动态sql*where*查询条件

### 场景：

```
where id=#{id} and name like #{name}
```

这一条sql 如果我们的 `id=#{id}`不传参数， 那么这一条sql就变成了 `where and name like #{name}` 就会报错

### 解决：

- 1.给where后面加上1=1，以后的条件都and xxx.
- 2.mybatis使用where标签来将所有的查询条件包括在内。mybatis就会将where标签中拼装的sql，多出来的and或者or去掉
  where只会去掉第一个多出来的and或者or。

## 1.3 trim_自定义字符串截取

### 场景

我们把 and 或者是 or 写在第一个开始的每一个条件后面

```
  <where>
            <if test="id!=null">
                id=#{id} and
            </if>
            <if test="lastName !=null and  lastName!= '' ">
                 lastName like #{lastName} and
            </if>
            <!--email 是string  trim() 截取 不为null 表明有值 -->
            <if test="email!=null and email.trim()!="" ">
                 email =#{email} and
            </if>
            <if test="gender == 0  or gender == 1">
                 gender = #{gender}
            </if>
        </where>
```

报错：

```
Cause: java.sql.SQLSyntaxErrorException: You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '' at line 10
```

### 解决：

trim 标签的4个属性

```xml
 <trim prefix="" suffix="" prefixOverrides="" suffixOverrides="">

 </trim>
```

XML

Copy

sql案例代码

```xml
 <!-- 后面多出的and或者or where标签不能解决
     prefix="":前缀：trim标签体中是整个字符串拼串 后的结果。
             prefix给拼串后的整个字符串加一个前缀
     prefixOverrides="":
             前缀覆盖： 去掉整个字符串前面多余的字符
     suffix="":后缀
             suffix给拼串后的整个字符串加一个后缀
     suffixOverrides=""
             后缀覆盖：去掉整个字符串后面多余的字符
     -->
    <select id="getEmpsByConditionTrim"  resultType="com.study.mybatis.bean.Employee">
        select  * from employee
<!-- 自定义字符串的截取规则 -->
        <trim prefix="where" suffixOverrides="and">
            <if test="id!=null">
                id=#{id} and
            </if>
            <if test="lastName !=null and  lastName!= '' ">
                 lastName like #{lastName} and
            </if>
            <!--email 是string  trim() 截取 不为null 表明有值 -->
            <if test="email!=null and email.trim()!="" ">
                 email =#{email} and
            </if>
            <if test="gender == 0  or gender == 1">
                 gender = #{gender}
            </if>
        </trim>
    </select>
```

XML

Copy

console:

```java
==>  Preparing: select * from employee where id=? and lastName like ? and email =? 
==> Parameters: 1(Integer), %1%(String), %163.com%(String)
<==      Total: 0
```

Java

Copy

## 1.4 choose_分支选择 swtich-case

### 场景和使用

如果 条件中带了我们最希望的，比如说 有id和name ，我们希望有id 就用id查询就行了，只用一个

```
 <select id="getEmpsByConditionChoose"  resultType="com.study.mybatis.bean.Employee">
        select  * from  employee
        <where>
            <choose>
                <when test="id!=null">
                    id = #{id}
                </when>
                <when test="lastName != null">
                    lastName like  #{lastName}
                </when>
                <when test="email !=null">
                    email =#{email}
                </when>
                <!--都不满足 1=1 查询所有-->
                <otherwise>
                    1= 1
                </otherwise>
            </choose>
        </where>
    </select>
```

console

```java
==>  Preparing: select * from employee WHERE id = ? 
==> Parameters: 1(Integer)
<==    Columns: id, lastName, email, gender, deptId
<==        Row: 1, 111, 11@163.com, 1, 1
<==      Total: 1
Closing non transactional SqlSession [org.apache.ibatis.session.defaults.DefaultSqlSession@62315f22]
[[Employee [id=1, lastName=111, email=11@163.com, gender=1]]]
```

Java

Copy

## 1.5 set + if 的动态更新

### 使用 set+if实现

**场景：** 根据字段传参值 来判断

dao 接口：

```java
public     void updateEmp(Employee employee);
```

Java

Copy

mapper:

```xml
 <update id="updateEmp" parameterType="com.study.mybatis.bean.Employee">
        update  employee

        <set>
        <if test="lastName != null">
            lastName = #{lastName},
        </if>
        <if test="email != null">
            email = #{email},
        </if>
        <if test="gender!= null">
            gender = #{gender}
        </if>
        </set>
        <where>
         id = #{id}
        </where>
    </update>
```

XML

Copy

### 使用 `trim` 标签 实现

mapper:

- prefix=””:前缀：trim标签体中是整个字符串拼串 后的结果。

  ```
         prefix给拼串后的整个字符串加一个前缀
  ```

- prefixOverrides=””:

  ```
       前缀覆盖： 去掉整个字符串前面多余的字符
  ```

- suffix=””:后缀

  ```
       suffix给拼串后的整个字符串加一个后缀
  ```

- suffixOverrides=””

  ```
        后缀覆盖：去掉整个字符串后面多余的字符
  ```

```xml
 <update id="updateEmp" parameterType="com.study.mybatis.bean.Employee">
        update  employee
        <trim prefix="set"  suffixOverrides=",">
            <if test="lastName != null">
                lastName = #{lastName},
            </if>
            <if test="email != null">
                email = #{email},
            </if>
            <if test="gender!= null">
                gender = #{gender}
            </if>

        </trim>
        <where>
         id = #{id}
        </where>
    </update>
```

XML

Copy

console:

```java
入参为  Employee employee = new Employee(1,"bbb","bbb@163.com",null);
==>  Preparing: update employee set lastName = ?, email = ? WHERE id = ? 
==> Parameters: bbb(String), bbb@163.com(String), 1(Integer)
<==    Updates: 1
```

Java

Copy

### 注意

> 注意 set +if中的 `,`是在第一个开始每一个语句的后面(除最后一个)

## 1.6 foreach_遍历集合

场景： 我们要取出来使用id在集合中的数据

### 使用

dao:

```java
 /**
     * 取出所有id在list的数据
     *  @Param
     * 如果你的映射方法接受多个参数，就可以使用这个注解自定义每个参数的名字。否则在默认情况下，
     * 除 RowBounds 以外的参数会以 "param" 加参数位置被命名。例如 #{param1}, #{param2}。
     * 如果使用了 @Param("person")，参数就会被命名为 #{person}。
     * @param ids
     * @return
     */
    public  List<Employee> getEmpsByConditionForeach(@Param("ids") List<Integer> ids);
```

Java

Copy

**mapper:**

```java
   <!--
             collection：指定要遍历的集合：
                 list类型的参数会特殊处理封装在map中，map的key就叫list
             item：将当前遍历出的元素赋值给指定的变量
             separator:每个元素之间的分隔符
             open：遍历出所有结果拼接一个开始的字符
             close:遍历出所有结果拼接一个结束的字符
             index:索引。遍历list的时候是index就是索引，item就是当前值
                           遍历map的时候index表示的就是map的key，item就是map的值
             #{变量名}就能取出变量的值也就是当前遍历出的元素
           -->
    <select id="getEmpsByConditionForeach" resultType="com.study.mybatis.bean.Employee">
        select  * from employee  where id in
        <foreach collection="ids" item="item_id" separator="," open="(" close=")" >
            #{item_id}
        </foreach>
    </select>
```

Java

Copy

console:

```java
==>  Preparing: select * from employee where id in( ? , ? , ? ) 
==> Parameters: 1(Integer), 2(Integer), 3(Integer)
<==    Columns: id, lastName, email, gender, deptId
<==        Row: 1, bbb, bbb@163.com, 1, 1
<==        Row: 2, aaa, aaa.@163.com, 0, 1
<==        Row: 3, 123, 1221, 1, 2
<==      Total: 3
```