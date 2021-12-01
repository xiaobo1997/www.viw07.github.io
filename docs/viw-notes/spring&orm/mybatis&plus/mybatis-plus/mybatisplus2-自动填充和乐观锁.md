[TOC]



# mybatisplus(2)



## 自动填充

相当于Mp 自动调用set方法

`https://baomidou.com/guide/`

![image-20201226174308936](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201226174308936.png)

步骤：

1.使用注解  `@TableField(xxx)` 

如  `@TableField(fill=FieldFill.INSERT)`

```java
  @TableField(fill = FieldFill.INSERT)
    private Date createTime;
    @TableField(fill = FieldFill.UPDATE)
    private Date updateTime;
```

2.实现接口

```java
package com.viw.mybatispluslab1.handle;

import com.baomidou.mybatisplus.core.handlers.MetaObjectHandler;
import org.apache.ibatis.reflection.MetaObject;
import org.springframework.stereotype.Component;

import java.util.Date;

/**
 * @Author: xhb
 * @Date: 2020/12/26 17:44
 */
@Component
public class MpObjectMethodHandle implements MetaObjectHandler {
    @Override
    public void insertFill(MetaObject metaObject) {
        /**
         * 第一个字段值
         * 第二个 属性值
         * 第三个 元数据对象
         */
        this.setFieldValByName("createTime", new Date(), metaObject);
        this.setFieldValByName("updateTime", new Date(), metaObject);
    }

    @Override
    public void updateFill(MetaObject metaObject) {
        this.setFieldValByName("updateTime", new Date(), metaObject);

    }
}

```



3.测试

```java
@Test
public  void add(){
    People user = new People();
    user.setName("viw");
    user.setAge(77);
    int insert = peopleMapper.insert(user);
    System.out.println(insert);
}

 @Test
    public  void add(){
        People user = new People();
        user.setId(1342770151157678082L);
        user.setAge(88);
        int insert = peopleMapper.updateById(user);
        System.out.println(insert);
    }

```

## 乐观锁



> 使用数据库版本解决 +1



### 实现方式



1. 加字段，version

   <img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201226180918688.png" alt="image-20201226180918688" style="zoom:50%;" />

   

2. 加注解  @Version

   ```
    @Version
       private Integer version;
   ```

   

3. 实现方法插件

   ```java
   package com.viw.mybatispluslab1.config;
   
   import com.baomidou.mybatisplus.extension.plugins.OptimisticLockerInterceptor;
   import org.mybatis.spring.annotation.MapperScan;
   import org.springframework.context.annotation.Bean;
   import org.springframework.context.annotation.Configuration;
   
   /**
    * @Author: xhb
    * @Date: 2020/12/26 18:04
    */
   @Configuration
   @MapperScan("com.viw.mybatispluslab1.mapper")
   public class VersionLock {
   
       /**
        * 乐观锁插件
        */
       @Bean
       public OptimisticLockerInterceptor optimisticLockerInterceptor() {
           return new OptimisticLockerInterceptor();
       }
   }
   
   ```

   

4. 注册Bean到容器中

5. 测试

   ```
   // 1.先查
   // 2.更新
   // 3.版本号 +1
   ```





## 分页

使用pageHelper 自带分页插件

1. 创建配置类

   ```java
   @Configuration
   @MapperScan("com.viw.mybatispluslab1.mapper")
   public class VersionLock {
   
       @Bean
       public PaginationInterceptor paginationInterceptor() {
           return new PaginationInterceptor();
       }
   ```

   

2. 测试

   ```java
    @Test
       public void page(){
           // 当前页   每一页显示的记录数
           Page<People> page = new Page<>(1,5);
           peopleMapper.selectPage(page,null);
           page.getCurrent();//当前页
           page.getRecords();//每页数据的集合
           page.getSize();//每页显示的记录数
           page.getTotal();//总记录数
           page.getPages();//总页数
           page.hasNext();//是否有下一页
           page.hasPrevious();//是否有上页
       }
   ```



## 删除

删除分为逻辑删除（通过标置位让数据查询不到）和物理删除（真实删除）



### 逻辑删除



1. 添加一个删除字段<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201226183554556.png" alt="image-20201226183554556" style="zoom:50%;" />

2. 实体类添加删除字段，加上注解 @TableLogic(fill=FieldFill.INSERT)

   ```java
   @TableLogic
       //@TableField(fill = FieldFill.INSERT)
       private Integer deleted;
   ```

   

3. 元数据添加删除的默认值

4. 加配置插件

   ```java
   @Configuration
   @MapperScan("com.viw.mybatispluslab1.mapper")
   public class VersionLock {
   
       @Bean
       public ISqlInjector sqlInjector(){
           return new LogicSqlInjector();
       }
   ```

   

5. application.properties

   ```properties
   # 删除 变1
   mybatis-plus.global-config.db-config.logic-delete-value=1
   # 不删除 变0  默认
   mybatis-plus.global-config.db-config.logic-not-delete-value=0
   ```

   

6. 注册   

   ```java
   
   ```



7. 测试

   ```java
     @Test
       public  void delete(){
           peopleMapper.deleteById(1342770151157678082L);
       }
   ```

   控制台

   ```
   ==>  Preparing: UPDATE people SET deleted=1 WHERE id=? AND deleted=0 
   ==> Parameters: 1342770151157678082(Long)
   <==    Updates: 1
   ```

   再查询所有

   ![image-20201226184616066](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201226184616066.png)



## 性能分析

参数：maxTime： SQL 执行最大时长，超过自动停止运行，有助于发现问题。

参数：format： SQL是否格式化，默认false。

 步骤：

1.在配置类中添加 插件

```java
  /**
     * SQL 执行性能分析插件
     * 开发环境使用，线上不推荐。 maxTime 指的是 sql 最大执行时长
     */
    @Bean
    @Profile({"dev", "test"})// 设置 dev test 环境开启  生成环境是prod
    public PerformanceInterceptor performanceInterceptor() {
        PerformanceInterceptor performanceInterceptor = new PerformanceInterceptor();
        performanceInterceptor.setMaxTime(100);//ms，超过此处设置的ms则sql不执行
        performanceInterceptor.setFormat(true);
        return performanceInterceptor;
    }
```

2.设置spring boot环境

```properties
spring.profiles.active=dev
```



## 复杂查询



### 类

Wrapper ： 条件构造抽象类，最顶端父类

  AbstractWrapper ： 用于查询条件封装，生成 sql 的 where 条件

​    QueryWrapper ： Entity 对象封装操作类，不是用lambda语法

​    UpdateWrapper ： Update 条件封装，用于Entity对象更新操作

  AbstractLambdaWrapper ： Lambda 语法使用 Wrapper统一处理解析 lambda 获取 column。

​    LambdaQueryWrapper ：看名称也能明白就是用于Lambda语法使用的查询Wrapper

​    LambdaUpdateWrapper ： Lambda 更新封装Wrapper



### 操作

一般是使用 QueryWrapper构建条件

`https://baomidou.com/guide/wrapper.html#eq`