[TOC]



# 1.mapper映射文件

概念：
对应着mybatis中如何进行数据库增删改查的操作

主要标签：

- cacahe
- cacahe-ref
- resultMap
- sql
- insert
- update
- delete
- select

## 1.1 极速体验 sql(springboot2.2.x+ mybatis)

### 1.1.1 流程：

配置版mybatis:

1.编写全局配置文件 mybatis-config.xml

2.编写mapper

3.application.properties中编写 数据库连接信息

```
spring.datasource.url=jdbc:mysql://localhost:3306/eesy_mybatis?useUnicode=true&characterEncoding=utf-8&useSSL=false&serverTimezone=UTC
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.username=root
spring.datasource.password=1234


#扫描mapper
mybatis.mapper-locations=classpath:mapper/*Mapper.xml
#别名
mybatis.type-aliases-package=com.study.mybatis.dao
```

编写测试：

```
@SpringBootTest
class MybatisConfigApplicationTests {
    @Autowired
    EmployeeDao employeeDao;
    @Test
    void contextLoads() {
        Employee employee = new Employee();
        employee.setId(null);
        employee.setLastName("aaa");
        employee.setEmail("aaa.@163.com");
        employeeDao.addEmp(employee);
        System.out.println(Arrays.asList(employee));
    }
}
```

### 1.1.2 完整代码

#### 1.1.2.1 目录树：

```
tree /f >list.txt
─mybatis-config-1

    │  pom.xml
    │  

    │          
    ├─src
    │  ├─main
    │  │  ├─java
    │  │  │  └─com
    │  │  │      └─study
    │  │  │          └─mybatis
    │  │  │              │  MybatisConfigApplication.java
    │  │  │              │  
    │  │  │              ├─dao
    │  │  │              │      EmployeeDao.java
    │  │  │              │      
    │  │  │              └─domin
    │  │  │                      Department.java
    │  │  │                      Employee.java
    │  │  │                      
    │  │  └─resources
    │  │      │  application.properties
    │  │      │  
    │  │      ├─mybatis
    │  │      │  │  mybatis-config.xml
    │  │      │  │  
    │  │      │  └─mapper
    │  │      │          EmployeeMapper.xml
    │  │      │          
    │  │      ├─static
    │  │      └─templates
    │  └─test
    │      └─java
    │          └─com
    │              └─study
    │                  └─mybatis
    │                      │  MybatisConfigApplicationTests.java
    │                      │  
    │                      └─dao
    │                              EmpDao.java
    │
```

#### 1.1.2.2 application.properties

```
mybatis.mapper-locations=classpath:mybatis/mapper/*Mapper.xml
mybatis.type-aliases-package=com.study.mybatis.domin


spring.datasource.url=jdbc:mysql://localhost:3306/eesy_mybatis?useUnicode=true&characterEncoding=utf-8&useSSL=false&serverTimezone=UTC
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.username=root
spring.datasource.password=1234
```

#### 1.1.2.3 domin

```
public class Employee {

    private Integer id;
    private String lastName;
    private String email;
    private String gender;

    ... get  and  set
```

#### 1.1.2.4 dao

```
package com.study.mybatis.dao;

import com.study.mybatis.domin.Employee;
import org.apache.ibatis.annotations.MapKey;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Param;

import java.util.List;
import java.util.Map;

/**
 * @Author: xiaobo
 * @Date: 2020/8/25 19:59
 */
@Mapper
public interface EmployeeDao {



    //多条记录封装一个map：Map<Integer,Employee>:键是这条记录的主键，值是记录封装后的javaBean
    //@MapKey:告诉mybatis封装这个map的时候使用哪个属性作为map的key
    @MapKey("lastName")
    public Map<String, Employee> getEmpByLastNameLikeReturnMap(String lastName);

    //返回一条记录的map；key就是列名，值就是对应的值
    public Map<String, Object> getEmpByIdReturnMap(Integer id);

    public List<Employee> getEmpsByLastNameLike(String lastName);

    public Employee getEmpByMap(Map<String, Object> map);

    public Employee getEmpByIdAndLastName(@Param("id")Integer id, @Param("lastName")String lastName);

    public Employee getEmpById(Integer id);

    public Long addEmp(Employee employee);

    public void updateEmp(Employee employee);

    public void deleteEmpById(Integer id);
}
```

#### 1.1.2.5 xxxMapper.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.study.mybatis.dao.EmployeeDao">
    <!--
    namespace:名称空间;指定为接口的全类名
    id：唯一标识
    resultType：返回值类型
    #{id}：从传递过来的参数中取出id值
    public Employee getEmpById(Integer id);
     -->

    <!--public Map<Integer, Employee> getEmpByLastNameLikeReturnMap(String lastName);  -->
    <select id="getEmpByLastNameLikeReturnMap" resultType="com.study.mybatis.domin.Employee">
         select * from  employee where last_name like #{lastName}
     </select>

    <!--  public Long addEmp(Employee employee);  parameterType 全类名或别名-->
<insert id="addEmp"  parameterType="com.study.mybatis.domin.Employee">
    insert  into  Employee(lastName,email,gender)
    value
    (#{lastName},#{email},#{gender})
</insert>

    <!-- public void updateEmp(Employee employee); -->
    <update id="updateEmp" >
        update  Employee set last_name = #{lastName},email=#{email},gender=#{gnder}
        where id=#{id}
    </update>

    <!--  public void deleteEmpById(Integer id);-->
    <delete id="deleteEmpById">
        delete  from  employee where id=#{id}
    </delete>
</mapper>
```

### 1.3 总结

1. mybatis 允许增删改查 直接定义

- Integer , Long , Boolean(影响行数超过0行返回 true), void

**如：**

dao中

```
public  boolean  updateEmp(Emp emp)
```

如果修改超过 返回 true 否则 false

## 1.2 insert —— 获取自增主键的值

### 1.2.1 原理

- 1.原生jdbc
  在原生的jdbc中 我们可以通过 statement 中的 getGenerateKeys() 获取我们的自增主键
- 2.mybatsi
  在mybatis中 也是通过 statement.getGenerateKeys() 获取的

### 1.2.2 使用：

```
    mysql支持自增主键，自增主键值的获取，mybatis也是利用statement.getGenreatedKeys()；
        useGeneratedKeys="true"；使用自增主键获取主键值策略
        keyProperty；指定对应的主键属性，也就是mybatis获取到主键值以后，将这个值封装给javaBean的哪个属性
<insert id="addEmp"  parameterType="com.study.mybatis.domin.Employee"  useGeneratedKeys="true"  keyProperty="id">
    insert  into  Employee(lastName,email,gender)
    value
    (#{lastName},#{email},#{gender})
</insert>
```

## 1.3 oracle 获取 非自增主键值

```
<!-- 
    获取非自增主键的值：
        Oracle不支持自增；Oracle使用序列来模拟自增；
        每次插入的数据的主键是从序列中拿到的值；如何获取到这个值,如下；
     -->
```

## 1.4 获取 非自增主键的值 selectKey

```xmlx
    <!-- 
    获取非自增主键的值：
        Oracle不支持自增；Oracle使用序列来模拟自增；
        每次插入的数据的主键是从序列中拿到的值；如何获取到这个值；
     -->
    <insert id="addEmp" databaseId="oracle">
        <!-- 
        keyProperty:查出的主键值封装给javaBean的哪个属性
        order="BEFORE":当前sql在插入sql之前运行
               AFTER：当前sql在插入sql之后运行
        resultType:查出的数据的返回值类型

        BEFORE运行顺序：
            先运行selectKey查询id的sql；查出id值封装给javaBean的id属性
            在运行插入的sql；就可以取出id属性对应的值
        AFTER运行顺序：
            先运行插入的sql（从序列中取出新值作为id）；
            再运行selectKey查询id的sql；
         -->
        <selectKey keyProperty="id" order="BEFORE" resultType="Integer">
            <!-- 编写查询主键的sql语句 -->
            <!-- BEFORE-->
            select EMPLOYEES_SEQ.nextval from dual 
            <!-- AFTER：
             select EMPLOYEES_SEQ.currval from dual -->
        </selectKey>

        <!-- 插入时的主键是从selectKey序列中拿到的 -->
        <!-- BEFORE:-->
        insert into employees(EMPLOYEE_ID,LAST_NAME,EMAIL) 
        values(#{id},#{lastName},#{email<!-- ,jdbcType=NULL -->}) 
        <!-- AFTER：
        insert into employees(EMPLOYEE_ID,LAST_NAME,EMAIL) 
        values(employees_seq.nextval,#{lastName},#{email}) -->
    </insert>
```

> selectKey 先运行， 然后查出来的结果值封装成id 然后再运行我们的insert ，把 前面值给id

## 1.5 mybatis的参数处理

我们在dao中 定义的 接口方法中 的入参 类型， 在 mybatis 中 我们使用 #{xxx} 就可以取出来；

### 1.5.1 单个参数

单个参数：mybatis不会做特殊处理，
`#{参数名/任意名}`：取出参数值。

**比如**：

下面sql我们只传了 id一个参数 ，我们把id写成其他的 字母， 如 `idaaa` `idbbb`都可以

```
   <update id="updateEmp" >
        update  Employee set last_name = #{lastName},email=#{email},gender=#{gnder}
        where id=#{id}
    </update>
```

### 1.5.2 多个参数

#### 原理

```
多个参数：mybatis会做特殊处理。
    多个参数会被封装成 一个map，
        key：param1...paramN,或者参数的索引也可以
        value：传入的参数值
    #{}就是从map中获取指定的key的值；

    异常：
    org.apache.ibatis.binding.BindingException: 
    Parameter 'id' not found. 
    Available parameters are [1, 0, param1, param2]
    操作：
        方法：public Employee getEmpByIdAndLastName(Integer id,String lastName);
        取值：#{id},#{lastName}
```

#### 异常情况

**会报异常的sql如 ：**

dao:

```
public Emp  getEmpByIdAndListName(Integer id ,String lastName)
```

mapper:

```
 <select id="getEmpByIdAndLastName">
        select * from  employee where id=#{id} and lastName = #{lastName}
    </select>
```

#### 正确情况

dao:

```
public Employee getEmpByIdAndLastName(Integer id, String lastName);
```

mapper:

```
 <select id="getEmpByIdAndLastName" resultType="com.study.mybatis.domin.Employee">
        select * from  employee where id=#{param1} and lastName = #{param2}
    </select>
```

### 1.5.3命名参数

```
【命名参数】：明确指定封装参数时map的key；@Param("id")
    多个参数会被封装成 一个map，
        key：使用@Param注解指定的值
        value：参数值
    #{指定的key}取出对应的参数值
```

解决 不使用 param1 param2 的情况

我们明确指定 参数map的值

#### 案例代码

dao:

```
  public Employee getEmpByIdAndLastName(@Param("id")Integer id, @Param("lastName")String lastName);
```

mapper:

```
<select id="getEmpByIdAndLastName" resultType="com.study.mybatis.domin.Employee">
        select * from  employee where id=#{param1} and lastName = #{param2}
    </select>
```

> 注意： “resultType” 不要写成“resultMap” 会报 `Result Maps collection does not contain value for`

### 1.5.4 POJO & MAP & TO

```
POJO：
如果多个参数正好是我们业务逻辑的数据模型，我们就可以直接传入pojo；
    #{属性名}：取出传入的pojo的属性值    

Map：
如果多个参数不是业务模型中的数据，没有对应的pojo，不经常使用，为了方便，我们也可以传入map

如： public Employee getEmpByMap(Map<String, Object> map);

#{key}：取出map中对应的值



TO：
如果多个参数不是业务模型中的数据，但是经常要使用，推荐来编写一个TO（Transfer Object）数据传输对象
Page{
    int index;
    int size;
}
```

#### MAP

dao:

```
  public Employee getEmpByMap(Map<String, Object> map);
```

mapper:

```
<select id="getEmpByMap" resultType="com.study.mybatis.domin.Employee">
         select * from  employee where id=#{id} and lastName = #{lastName}
    </select>
```

test:

```
   @Test
    void  testB(){
        Map<String,Object> map = new HashMap<>();
        map.put("id",1);
        map.put("lastName","111");
        Employee employee =  employeeDao.getEmpByMap(map);
        System.out.println(employee);
    }
```

## 1.6 参数处理扩展

### 1.6.1 情况1

```
public Employee getEmp(@Param("id")Integer id,String lastName);
    取值：id==>#{id/param1}   lastName==>#{param2}
```

### 1.6.2 情况2

```
public Employee getEmp(Integer id,Employee emp);
    取值：id==>#{param1}    lastName===>#{param2.lastName}

或者

public Employee getEmp(Integer id,@Param("e")Employee emp);
    取值：id==>#{param1}    lastName===>#{e.lastName}
```

### 1.6.3 情况3

```
public Employee getEmpById(List<Integer> ids);
    取值：取出第一个id的值：   #{list[0]}
如果是Collection（List、Set）类型或者是数组，
         也会特殊处理。也是把传入的list或者数组封装在map中。
            key：Collection（collection）,如果是List还可以使用这个key(list)
                数组(array)
```