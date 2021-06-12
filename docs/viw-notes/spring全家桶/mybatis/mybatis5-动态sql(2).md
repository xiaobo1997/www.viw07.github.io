[TOC]





# DynamicSQL_base

## 1.forearch_批量插入

场景：我们需要通过foreach 批量插入

### 使用，mysql下批量插入

dao:

```java
 /**
     * 批量保存
     * @param employees
     */
    public  void addEmps(@Param("emps") List<Employee> employees);
```

Java

Copy

mapper:

```xml
<!--方法1-->
    <!--
    insert into employee(lastName.email,gender,deptId) values('a2','a2@163.com','0',1),('a3','a3@163.com','0',1)
    -->
    <!--foreach批量保存-->
    <!--MySQL下批量保存：可以foreach遍历   mysql支持values(),(),()语法-->
    <insert id="addEmps" >
       insert  into  employee(lastName,email,gender,deptId) values
        <!--emps就是我们的List<Employee>的值，因为这个是多个参，我们不给param 就会默认的param1,param2
        遍历出来的每一个元素值辅给emp
        -->
       <foreach collection="emps"  item="emp" separator=",">
           (#{emp.lastName},#{emp.email},#{emp.gender},#{emp.dept.id})
       </foreach>
    </insert>
 <!--方法2-->
 需要开启 允许一条语句有多个分号来分隔执行
    <insert id="addEmps" >
        <foreach collection="emps"  item="emp" separator=";">
            insert  into  employee(lastName,email,gender,deptId) values   (#{emp.lastName},#{emp.email},#{emp.gender},#{emp.dept.id})
            <!--emps就是我们的List<Employee>的值，因为这个是多个参，我们不给param 就会默认的param1,param2
            遍历出来的每一个元素值辅给emp
            -->
        </foreach>
    </insert>
```

XML

Copy

> 方法2 需要注意开启allowMultiQueries=true
>
> ```xml
> spring.datasource.url=jdbc:mysql://localhost:3306/eesy_mybatis?allowMultiQueries=true&useUnicode=true&characterEncoding=utf-8&useSSL=false&serverTimezone=UTC
> ```
>
> XML
>
> Copy

console:

```
==>  Preparing: insert into employee(lastName,email,gender,deptId) values ( ?,?,?,?) , ( ?,?,?,?) 
==> Parameters: a2(String), a2@163.com(String), 0(String), 1(Integer), a3(String), a3@163.com(String), 0(String), 1(Integer)
<==    Updates: 2
Closing non transactional SqlSession [org.apache.ibatis.session.defaults.DefaultSqlSession@4925f4f5]
[[Employee [id=null, lastName=a2, email=a2@163.com, gender=0], Employee [id=null, lastName=a3, email=a3@163.com, gender=0]]]
```

## 2.内置参数_parameter && _databaseId

### 使用

```
 <!-- 两个内置参数：
     不只是方法传递过来的参数可以被用来判断，取值。。。
     mybatis默认还有两个内置参数：
     _parameter:代表整个参数(就是在dao中定义的接口方法的所有入参)
         单个参数：_parameter就是这个参数
         多个参数：参数会被封装为一个map；_parameter就是代表这个map
     _databaseId:如果配置了databaseIdProvider标签。在全局配置文件mybatis-config.xml中的配置 其他数据库的环境
         _databaseId就是代表当前数据库的别名oracle
  -->
```

#### _databaseId来切换不同数据库的执行sql

mapper:

```
    <select id="getEmpsInnerParameter" resultType="com.study.mybatis.bean.Employee">
        <if test="_databaseId == 'mysql'">
            select * from employee
        </if>
    </select>
```

我们的全局配置文件 mybatis-config.xml 这就是我们指定的数据库别名

```
<!-- <databaseIdProvider type="DB_VENDOR">
         <property name="DB2" value="db2"/>
         <property name="Oracle" value="oracle"/>
         <property name="MySQL" value="mysql"/>
     </databaseIdProvider>-->
```

在 mybatis-config.xml中的 `environments`标签中切换 比如说我们改成 Oracle 就是以的 Oracle环境执行的也是oracle的sql， 一般开发中我们都是写好了几个，直接设置值调用即可

```
 <!--  <environments default="mysql">
```

#### _parameter 取入参值

dao接口方法入参有值才传入lastName属性 来

```
      <select id="getEmpsInnerParameter" resultType="com.study.mybatis.bean.Employee">

        <if test="_databaseId == 'mysql'">
            select * from employee
            <if test="_parameter != null">
                /*  where lastName like  #{lastName}*/
                where lastName list #{_parameter.lastName}
            </if>
        </if>
    </select>
```

## 3.bind_绑定标签

标签：

```xml
 <bind name="" value=""/>
```

XML

Copy

### 使用

mapper:

```
 <select id="getEmpsInnerParameter" resultType="com.study.mybatis.bean.Employee">
        <!-- bind：可以将OGNL表达式的值绑定到一个变量中，方便后来引用这个变量的值 -->
        <!--将lastName 通过拼接成  %lastName% 再赋给 lastNameLike  后续查询 我们就不用手写 %%了，比如说 直接输入 e 自动为我们
         拼接成  %e% 也就是lastName 中带e的 有哪些

         '%#{lastName}%' 不可以拼  '%${lastName}%'可以 因为'$'可以动态拼sql只是不安全
         where lastName list #{_parameter.lastName}
         -->
        <bind name="lastNameLike" value="'%'+lastName+'%'"/>
      <!--  <if test="_databaseId == 'mysql'">-->
            select * from employee
            <if test="_parameter != null">
                  where lastName like  #{lastNameLike}
          <!--  </if>-->
        </if>
    </select>
```

> 注意：切换数据库环境的，因为我这里已经用的是mysql，虽然我就把`_databaseId`注释，要不然报错，主要是一个思路，需要改相应的驱动 改相应的配置，放在不管你是写在mybatis-config.xml还是 application.properties，主要是以mysql为主，很多公司也以 oracle 和 db2，还是要都稍微学习下

test:

```java
  Employee employee = new Employee();
        employee.setLastName("e");
         empDao.getEmpsInnerParameter(employee);
```

Java

Copy

console:

```
==>  Preparing: select * from employee where lastName like ? 
==> Parameters: %e%(String)
<==      Total: 0
```

可以看到已经拼接上了 `%`

## 4.sql标签_抽取重用的sql片段

标签：

```
    <sql id="" databaseId="" lang=""></sql>
```

### 使用

sql标签 可重用的sql

```
<!--     include就是我们引入的外部sql  用id来标识-->
    <sql id="insertColumn" >
        lastName,email,gender,deptId
    </sql>
```

在insert中引入 重用片段sql的id

注意：**写在include 标签中**

```
<insert id="addEmps" >
        <foreach collection="emps"  item="emp" separator=";">
            insert  into  employee(
            <include refid="insertColumn"></include>
            ) values   (#{emp.lastName},#{emp.email},#{emp.gender},#{emp.dept.id})
            <!--emps就是我们的List<Employee>的值，因为这个是多个参，我们不给param 就会默认的param1,param2
            遍历出来的每一个元素值辅给emp
            -->
        </foreach>
    </insert>
```

测试的console:

```
==>  Preparing: insert into employee( lastName,email,gender,deptId ) values (?,?,?,?) 
==> Parameters: a4(String), a4@163.com(String), 0(String), 1(Integer)
<==    Updates: 1
```