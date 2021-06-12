[TOC]



# spring事务



## 事务的隔离级别

1. 丢失修改
2. 脏读
3. 不可重复读
4. 幻读





## spring 事务接口



![image-20201223002125486](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201223002125486.png)

![](https://user-gold-cdn.xitu.io/2019/12/27/16f47e323cae7287?imageslim)



## 注解 @Transaction



```java
public interface PlatformTransactionManager()...{  
    // 由TransactionDefinition得到TransactionStatus对象
    TransactionStatus getTransaction(TransactionDefinition definition) throws TransactionException; 
    // 提交
    void commit(TransactionStatus status) throws TransactionException;  
    // 回滚
    void rollback(TransactionStatus status) throws TransactionException;  
    } 

```





## 事务的实现方式



主要是编程式事务，和声明式事务



1. 基于编程式事务，需要在代码中调用beginTransaction(),commit(),rollback()等事务管理方法
2. 基于TransactionProxyFactoryBean的声明式事务管理，
3. 基于@Transactionl的声明式事务管理
4. 基于Aspectj aop的配置事务



### 编程式事务

> 编程式，可以使用`TransactionTemplate`或者直接使用底层的`PlatformTransactionManager`。对于编程式事务管理，Spring推荐使用`TransactionTemplate`



### 声明式



> 声明式事务本质是建立在AOP基础之上的，在代理类实例里拦截目标方法，在方法执行前，开启或者加入一个事务，执行完毕或者发生异常后，根据具体情况进行提交或者回滚。
> 相比较编程式事务，其最大的特点就是对业务代码没有侵入性，只需在配置文件中对事务进行配置管理（或者基于`@Transactional`注解的方式）即可，这样就可以将业务代码和事务管理隔离开。



1. `@Transactional`注解事务管理
   基于注解的事务管理，在需要开启事务的类或方法上添加`@Transactional`，这种方式比较灵活，随用随加，但是容易到处乱用，凌乱不堪。更偏重灵活性。![image-20201223003349223](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201223003349223.png)

   ```xml
   <!--  开启注解管理事务 -->
   <tx:annotation-driven transaction-manager="transactionManager"/>
   ```

   

2. `Aspectj AOP`配置事务管理

   ```xml
   <!-- 事务AOP --><aop:config>   
     <!-- pointcut:切入点 aop:advisor 适配器，是要注入的方法和pointcut连接的桥梁 -->    
     <aop:advisor pointcut="execution(* cn.x.x..*.service..*.*(..))" 
                  advice-ref="txAdvice" />
   </aop:config>
   <!-- 需要注入的方法，可以使用通配符进行匹配 -->
   <tx:advice id="txAdvice" transaction-manager="transactionManager">  
     <tx:attributes>      
       <tx:method name="get*" isolation="REPEATABLE_READ" propagation="REQUIRED" read-only="true" />  
       <tx:method name="insert*" isolation="REPEATABLE_READ" propagation="REQUIRED" rollback-for="Exception"/>     
       <tx:method name="update" isolation="REPEATABLE_READ" propagation="REQUIRED" rollback-for="Exception"/>     
       <tx:method name="delete*" isolation="REPEATABLE_READ" propagation="REQUIRED" rollback-for="Exception"/>  
     </tx:attributes>
   </tx:advice>
   <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager"> 
     <property name="dataSource" ref="dataSource" />
   </bean>
   
   ```





## 事务的传播行为



> 指的是一个事务被另外一个事务调用时，这个事务应该怎么做
>
> 如事务A中调用事务B， B继续在A中运行 还是再开启一个事务



![image-20201223003537842](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201223003537842.png)



## 事务的属性设置

![image-20201223003909928](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201223003909928.png)

1. 和事务属性关联的方法名

2. 隔离级别 isolation

3. 事务传播行为 propagation

4. 超时 timeout

5. 只读 read-only

6. rollback-for 触发回滚的异常

   ```
   如果是检查时异常 事务不rollback ， 运行时exception  事务 rollback
   ```

7. no-rollback-for 不触发 回滚的异常





## 参考



https://juejin.cn/post/6844904033191198733



https://juejin.cn/post/6875120614319849479



