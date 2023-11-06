



# 1.mapper

## console打印sql

springboot+ mybatis 打印 sql

```
mybatis.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl
```

## 1.1 参数处理${} 和#{}

### 1.1.1 区别

`#{}`：可以获取map中的值或者pojo对象属性的值；
`${}`：可以获取map中的值或者pojo对象属性的值；

![image-20201225140605273](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201225140605273.png)

区别：

```
        #{}:是以预编译的形式，将参数设置到sql语句中；PreparedStatement；防止sql注入
        ${}:取出的值直接拼装在sql语句中；会有安全问题；
        大多情况下，我们去参数的值都应该去使用#{}；

        原生jdbc不支持占位符的地方我们就可以使用${}进行取值
        比如分表、排序。。。；按照年份分表拆分
            select * from ${year}_salary where xxx; // 比如 select * from 2019_salary wherr xxx   或者是  select * from 2020_salary wherr xxx
            这样就动态的为我们拆表

            select * from tbl_employee order by ${f_name} ${order}
```

如

mapper:

```
 <select id="getEmpByMap" resultType="com.study.mybatis.domin.Employee">
         /*select * from  employee where id=#{id} and lastName = ${lastName}*/
         select * from  ${tableName} where id=#{id} and lastName = ${lastName}
    </select>
```

test:

```
    @Test
    void  testB(){
        Map<String,Object> map = new HashMap<>();
        map.put("id",1);
        map.put("lastName","111");
        map.put("tableName","Employee");
        Employee employee =  employeeDao.getEmpByMap(map);
        System.out.println(employee);
    }
```

### 1.1.2 `#{}`取值的规则

```
规定参数的一些规则：
    javaType、 jdbcType、 mode（存储过程）、 numericScale、
    resultMap、 typeHandler、 jdbcTypeName、 expression（未来准备支持的功能）；

    jdbcType通常需要在某种特定的条件下被设置：
        在我们数据为null的时候，有些数据库可能不能识别mybatis对null的默认处理。比如Oracle（报错）；

        JdbcType OTHER：无效的类型；因为mybatis对所有的null都映射的是原生Jdbc的OTHER类型，oracle不能正确处理;

        由于全局配置中：jdbcTypeForNull=OTHER；oracle不支持；两种办法
        1、#{email,jdbcType=OTHER};
        2、jdbcTypeForNull=NULL
            <setting name="jdbcTypeForNull" value="NULL"/>
```

#### 扩展功能

```
规定参数的一些规则：
    javaType、 jdbcType、 mode（存储过程）、 numericScale、
    resultMap、 typeHandler、 jdbcTypeName、 expression（未来准备支持的功能）；



    jdbcType通常需要在某种特定的条件下被设置：
        在我们数据为null的时候，有些数据库可能不能识别mybatis对null的默认处理。比如Oracle（报错）；

        JdbcType OTHER：无效的类型；因为mybatis对所有的null都映射的是原生Jdbc的OTHER类型，oracle不能正确处理;

        // 标识通用sql类型 在JDBCType.class中
        // public enum JDBCType implements SQLType
```

**如:**

```
{name,javaType=int,jdbcType=NUMERIC}
{money,javaType=double,jdbcType=NUMERIC,numericScale=2}
```

## 1.2 select 标签

### 1.2.1 返回list

select标签 定义查询操作

**标签：**

- id 唯一标识符
  - 引用这一条语句，需要和接口的方法名一致
- parameterType :参数类型
  - mybatis根据TypeHandle对象 自动判断，选择填
- resultType :返回值类型
  - 用别名(Alias)或者 全类名(com.xxx.dao.xxxMapper) ，期望返回的类型是类的完全限定名或者别名，如果返回的是集合，定义集合中元素的类型，不能和 resultMap 同时使用
- resultMap: 自定义结果集

**如返回对象list：**

dao中:

```
    public List<Employee> getEmpsByLastNameLike(String lastName);
```

mapper:

```
  <select id="getEmpsByLastNameLike" resultType="com.study.mybatis.domin.Employee">
        select  * from Employee where lastName like   #{lastName}
    </select>
```

test:

```
@Test
    void testC(){
        List<Employee>  list= employeeDao.getEmpsByLastNameLike("%1%");
        System.out.println(Arrays.asList(list));
    }
```

console:

```
[[Employee [id=1, lastName=111, email=11, gender=11], Employee [id=3, lastName=123, email=1221, gender=null]]]
```

### 1.2.2 返回封装 Map

#### 单条记录 封装map

dao:

```
 //返回一条记录的map；key就是列名，值就是对应的值
    public Map<String, Object> getEmpByIdReturnMap(Integer id);
```

mapper:

```
  <select id="getEmpByIdReturnMap" resultType="map">
        select  * from Employee where id = #{id}
    </select>
```

test:

```
 @Test
    void  testD(){
        Map<String,Object> map = employeeDao.getEmpByIdReturnMap(1);
        System.out.println(map);

    }
```

console:

```
{lastName=111, gender=11, id=1, email=11}
```

#### 多条记录封装 map

dao:

```
    //多条记录封装一个map：Map<Integer,Employee>:键是这条记录的主键，值是记录封装后的javaBean对象
    //@MapKey:告诉mybatis封装这个map的时候使用哪个属性作为map的key
    @MapKey("lastName") // 现在就是 lastName 作为 key
    public Map<String, Employee> getEmpByLastNameLikeReturnMap(String lastName);
```

mapper:

```
   <!--返回类型为 java bean 对象-->
    <select id="getEmpByLastNameLikeReturnMap" resultType="com.study.mybatis.domin.Employee">
        select  * from Employee where lastName like #{lastName}
    </select>
```

测试：

```
 @Test
    void  testD(){
//        Map<String,Object> map = employeeDao.getEmpByIdReturnMap(1);
        Map<String, Employee> map = employeeDao.getEmpByLastNameLikeReturnMap("%1%");
        System.out.println(map);
    }
```

console

```
{111=Employee [id=1, lastName=111, email=11, gender=11], 123=Employee [id=3, lastName=123, email=1221, gender=null]}
```

### 1.2.3 resultMap 自定义结果映射规则（初级）

#### 1.2.3.1 **自动映射：**

1.在全局配置中

`autoMappingBehavior` 的默认属性 是PARTIAL ，需要列名和javaBean 属性名一致

数据库字段命名规范: POJO 属性符合 驼峰 如 last_name -> lastName

2.自定义 resultMap

------

#### 1.2.3.2 使用

dao:
`public Employee getEmpById(Integer id);`

mapper:

```
   <!--自定义某个javaBean的封装规则
    type：自定义规则的Java类型
    id:唯一id方便引用
      -->
    <resultMap type="com.study.mybatis.domin.Employee" id="MyEmp">
        <!--指定主键列的封装规则
        id定义主键会底层有优化；
        column：指定哪一列
        property：指定对应的javaBean属性
          -->
        <id column="id" property="id"/>
        <!-- 定义普通列封装规则 -->
        <result column="lastName" property="lastName"/>
        <!-- 其他不指定的列会自动封装：我们只要写resultMap就把全部的映射规则都写上。 -->
        <result column="email" property="email"/>
        <result column="gender" property="gender"/>
    </resultMap>

    <!-- resultMap:自定义结果集映射规则；  -->
    <!-- public Employee getEmpById(Integer id); -->
    <select id="getEmpById"  resultMap="MyEmp">
        select * from Employee where id=#{id}
    </select>
```

console:

```
Employee [id=1, lastName=111, email=11, gender=11]
```

### 1.2.4 resultMap_关联查询(进阶)

#### 前提

情况1： Emp的属性是另外一个java bean 对象
而我们希望的是 比如说 这个同学是A班的，A班是不是也有 其他同学，我们查询的时候希望把A班的名字查出来

如：

```
//@Alias("emp")
public class Employee {

    private Integer id;
    private String lastName;
    private String email;
    private String gender;
    private Department dept; //Department 对象
```

我们把 Department表 创建出来 然后创建 外键关系

```
ALTER TABLE employee add  CONSTRAINT fk_emp_dept FOREIGN KEY (deptId) REFERENCES Department(id)
```

#### 使用级联属性封装结果

dao:

```
    public Employee getEmpAndDept(Integer id);
```

mapper:

```
  <!--
       方法1. 联合查询：级联属性封装结果集
      -->
    <resultMap id="EmpAndDept" type="com.study.mybatis.domin.Employee">
        <id column="id" property="id"/>
        <result column="lastName" property="lastName"/>
        <result column="email"  property="email"/>
        <result column="gender"  property="gender"/>
        <result column="departmentName"  property="dept.departmentName"/>
    </resultMap>

    <select id="getEmpAndDept" resultMap="EmpAndDept">
        select  e.id ,e.lastName , e.email,e.gender,d.departmentName  from  employee e, department d
where e.deptId = d.id
and e.id = #{id}
    </select>
```

test:

```
 @Test
    void  testB(){
        Employee employee = employeeDaoPlus.getEmpAndDept(1);
        System.out.println(employee);
    }
```

console:

```
==>  Preparing: select e.id ,e.lastName , e.email,e.gender,d.departmentName from employee e, department d where e.deptId = d.id and e.id = ? 
==> Parameters: 1(Integer)
<==    Columns: id, lastName, email, gender, departmentName
<==        Row: 1, 111, 11, 11, 开发
<==      Total: 1
```

> 注意：我们封装结果集是 `property="dept.departmentName"`来取出 emp对象中的dept属性，也就是部门信息，而这个dept 就是我们在Employee 中定义的 department对象

### 1.2.5 resultMap_关联查询—_association定义关系对象封装规则

**相当于前面1.2.4的实现方法2**

只是mapper中写的不同

mapper:

```
   <!--
          方法2. 使用association定义关联的单个对象的封装规则；
         -->
    <resultMap id="EmpAndDept1" type="com.study.mybatis.domin.Employee">
        <id column="id" property="id"/>
        <result column="lastName" property="lastName"/>
        <result column="email"  property="email"/>
        <result column="gender"  property="gender"/>
      <!--  <result column="departmentName"  property="dept.departmentName"/>-->
        <!--  association可以指定联合的javaBean对象
        property="dept"：指定哪个属性是联合的对象
        javaType:指定这个属性对象的类型[不能省略]
        -->
        <association property="dept" javaType="com.study.mybatis.domin.Department">
            <id column="deptId" property="id" />
            <result column="departmentName" property="departmentName"/>
        </association>
    </resultMap>
    <select id="getEmpAndDept1" resultMap="EmpAndDept1">
        select  e.id ,e.lastName , e.email,e.gender,d.departmentName  from  employee e, department d
where e.deptId = d.id
and e.id = #{id}
    </select>
```

> 注意 `<id column="deptId" property="id" />` 这个deptId是Department的id ，`<id column="id" property="id"/>` 这个id是Employee的id 这两个id不能同名

### 1.2.6 resultMap_关联查询_association分步查询

#### 前提

DeptDao 中定义查询dept的方法:

```
@Mapper
public interface DepartmentDao {
    public Department getDeptById(Integer id);
}
```

DeptMapper 中定义sql:

```
 <select id="getDeptById" resultType="com.study.mybatis.domin.Department">
        select  * from  department where id=#{id}
    </select>
```

#### 使用：

EmpDao:中定义接口方法

```
  public Employee getEmpByIdStep(Integer id);
```

mapper:

```
  <!-- 使用association进行分步查询：
        1、先按照员工id查询员工信息
        2、根据查询员工信息中的d_id值去部门表查出部门信息
        3、部门设置到员工中；
     -->

    <resultMap id="EmpAndDep2" type="com.study.mybatis.domin.Employee">
        <id column="id" property="id"/>
        <result column="lastName" property="lastName"/>
        <result column="email" property="email"/>
        <result column="gender" property="gender"/>
        <!-- association定义关联对象的封装规则
         select:表明当前属性是调用select指定的方法查出的结果
         column:指定将哪一列的值传给这个方法

         流程：使用select指定的方法（传入column指定的这列参数的值）查出对象，并封装给property指定的属性
      -->
        <association property="dept" select="com.study.mybatis.dao.DepartmentDao.getDeptById"
    column="id"        >
        </association>
    </resultMap>
    <select id="getEmpByIdStep" resultMap="EmpAndDep2">
        select  * from employee where id= #{id}
    </select>
```

> 注意 `property="dept"`是Emp对象中定义的Department属性 `select="com.study.mybatis.dao.DepartmentDao.getDeptById"`指的是通过这个getDeptById方法当传入`column="id"`的id值 可以查询出 Dept的数据，

### 1.2.7 resultMap_关联查询_association分步查询&&延迟加载

**分步查询下可以使用延迟加载**

```
 <!-- 可以使用延迟加载（懒加载）；(按需加载)
         Employee==>Dept：
             我们每次查询Employee对象的时候，都将一起查询出来。
             部门信息在我们使用的时候再去查询；
             分段查询的基础之上加上两个配置：
      -->
```

#### 好处

延迟加载的好处：
先从单表查询、需要时再从关联表去关联查询，大大提高 数据库性能，因为查询单表要比关联查询多张表速度要快。

lazyLoadingEnabled：全局性设置懒加载。如果设为‘false’，则所有相关联的都会被初始化加载。默认为false
aggressiveLazyLoading：当设置为‘true’的时候，懒加载的对象可能被任何懒属性全部加载。否则，每个属性都按需加载。默认为true

#### 前提

mybatis-config.xml

在`<settings>`中设置 开启 延迟加载

```
 <!--开启延迟加载-->
        <setting name="lazyLoadingEnabled" value="true"/> <!--已默认为true-->
        <!--关闭积极加载-->
        <setting name="aggressiveLazyLoading" value="false"/>
```

### 1.2.8 resultMap_关联查询_collection定义关联集合封装规则

**场景：**

因为一个班级有多个同学，我们希望查询出来这个班级中的所有同学，那么这些同学其实是一个集合，所有我们要对这些查询出来的同学结果集 进行 再封装

#### 前提：

DeptDao:

```
public Department getDeptByIdAll(Integer id);
```

#### 案例代码：

DeptMapper:

```
   <!--嵌套结果集的方式，使用collection标签定义关联的集合类型的属性封装规则  -->
    <resultMap id="Dept01" type="com.study.mybatis.domin.Department">
        <id column="deptId" property="id"/>
        <result column="departmentName" property="departmentName"/>
        <!--
                    collection定义关联集合类型的属性的封装规则
                    ofType:指定集合里面元素的类型
                -->
        <collection property="emps" ofType="com.study.mybatis.domin.Employee">
            <!-- 定义这个集合中元素的封装规则 -->
            <id column="id" property="id"/>
            <result column="lastName" property="lastName"/>
            <result column="email" property="email"/>
            <result column="gender" property="gender"/>
        </collection>
    </resultMap>
    <select id="getDeptByIdAll" resultMap="Dept01">
         select  e.id ,e.lastName , e.email,e.gender,d.id deptId,d.departmentName
from   department d  LEFT JOIN employee e on d.id= e.deptId
where e.deptId =#{id}
    </select>
```

test:

```
 @Test
    void testA(){
       Department  department=   departmentDao.getDeptByIdAll(1);
        System.out.println( department);
        System.out.println(department.getEmps());
    }
```

console:

```
==>  Preparing: select e.id ,e.lastName , e.email,e.gender,d.id deptId,d.departmentName from department d LEFT JOIN employee e on d.id= e.deptId where e.deptId =? 
==> Parameters: 1(Integer)
<==    Columns: id, lastName, email, gender, deptId, departmentName
<==        Row: 1, 111, 11, 11, 1, 开发
<==        Row: 2, aaa, aaa.@163.com, null, 1, 开发
<==      Total: 2
Closing non transactional SqlSession [org.apache.ibatis.session.defaults.DefaultSqlSession@934b52f]
Department [id=1, departmentName=开发]
[Employee [id=1, lastName=111, email=11, gender=11], Employee [id=2, lastName=aaa, email=aaa.@163.com, gender=null]]
```

#### 注意

> 注意 查询出来的id不要重
> 比如说 下面这个 e.id 和d.id 如果我们不起别名 会 导致 默认给我们一个 d.id的别名为 id1，所有可能会报错，

```
   select  e.id ,e.lastName , e.email,e.gender,d.id ,d.departmentName
from   department d  LEFT JOIN employee e on d.id= e.deptId
where e.deptId =#{id}
```

> `<id column="deptId" property="id"/>` 这个deptId 就是我们起的别名,这样就对应起来了
>
> ```
>    select  e.id ,e.lastName , e.email,e.gender,d.id  deptId,d.departmentName
> from   department d  LEFT JOIN employee e on d.id= e.deptId
> where e.deptId =#{id}
> ```

### 1.2.9 resultMap_关联查询_collection分步查询 && 延迟加载

#### 前提：

EmpDao

```
 /**
     * 根据部门id查询该部门下所有人
     * @param deptId
     * @return
     */
    public List<Employee> getEmpsByDeptId(Integer deptId);
```

#### 案例代码

dao:

```
 public Department getDeptByIdStep(Integer id);
```

mapper:

```
   <!-- collection：分段查询 -->
    <!--
        1. 第一步 先查询 select * from Emp  where id =1
        2. 第二步 再根据前面查的id查询 select * from Dept where deptId = 1
    -->
    <resultMap type="com.study.mybatis.domin.Department" id="MyDeptStep">
        <id column="id" property="id"/>
        <id column="departmentName" property="departmentName"/>
        <collection property="emps"
                    select="com.study.mybatis.dao.EmployeeMapperPlus.getEmpsByDeptId"
                    column="deptId" fetchType="lazy"></collection>
    </resultMap>
    <select id="getDeptByIdStep" resultMap="MyDeptStep">
select id ,departmentName from Department where id=#{id}
    </select>
```

#### 注意

```
 <!-- collection：分段查询 -->
    <!--
        1. 第一步 先查询 select * from Emp  where id =1
        2. 第二步 再根据前面查的id查询 select * from Dept where deptId = 1
    -->
```

> 在collention中有一个column ，这个column 意思是 把 上一步 查出来的哪个值传给这个 collection 中的column ,如果我们要传递多列 就 需要如下操作

#### 分步查询传递多列值 && fetchType

```
    <!-- 多列的值传递过去：
            将多列的值封装map传递；
            column="{key1=column1,key2=column2}"

     -->
```

**延迟加载：**

我们在全局配置文件 mybatis-config.xml中 设置了

```
  <!--开启延迟加载-->
        <setting name="lazyLoadingEnabled" value="true"/>
        <!--关闭积极加载-->
        <setting name="aggressiveLazyLoading" value="false"/>
```

在 分步查询中的collection中 有一个属性 `fetchType="lazy"` 也是设置延迟加载

```
    fetchType="lazy"：表示使用延迟加载；
                - lazy：延迟
                - eager：立即
```

### 1.2.10 resultMap_discriminator鉴别器

#### 使用

```
  <!-- =======================鉴别器============================ -->
    <!-- <discriminator javaType=""></discriminator>
        鉴别器：mybatis可以使用discriminator判断某列的值，然后根据某列的值改变封装行为
        封装Employee：
            如果查出的是女生：就把部门信息查询出来，否则不查询；
            如果是男生，把last_name这一列的值赋值给email;
     -->
    <resultMap id="myEmpDis" type="com.study.mybatis.domin.Employee">
        <id column="id" property="id"/>
        <result column="lastName" property="lastName"/>
        <result column="email" property="email"/>
        <result column="gender" property="gender"/>
        <!--
         column：指定判定的列名
         javaType：列值对应的java类型  -->
        <discriminator javaType="string" column="gender">
            <!--女生  resultType:指定封装的结果类型；不能缺少。/resultMap-->
            <case value="0" resultType="com.study.mybatis.domin.Employee">
                <association property="dept"
                             select="com.study.mybatis.dao.DepartmentMapper.getDeptById"
                             column="deptId">
                </association>
            </case>
            <!--男生 ;如果是男生，把lastName这一列的值赋值给email; -->
            <case value="1" resultMap="com.study.mybatis.domin.Employee">
                <id column="id" property="id"/>
                <result column="lastName" property="lastName"/>
                <result column="lastName" property="email"/>
                <result column="gender" property="gender"/>
            </case>
        </discriminator>
    </resultMap>
    <select id="getEmpByIdStep1" resultMap="myEmpDis">
        select  * from  employee where  id=#{id}
    </select>
```