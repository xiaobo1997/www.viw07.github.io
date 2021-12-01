[TOC]

# 本地缓存失效和事务的关系

## 问题



在测试 mybatis一级缓存是否失效时，缓存失效，代码如下

```java
 @Autowired
   EmployeeDao employeeDao  ;
    @Test
   // @Transactional(rollbackFor = Throwable.class)
    public void TestA (){

            Employee empById = employeeDao.getEmpById(1);
            Employee empById2 = employeeDao.getEmpById(1);
            System.out.println(empById == empById2);
        }

```



## 原因



可能是sqlsession不一致，



![image-20200829224424945](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200829224424945.png)



![image-20200829225437084](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200829225437084.png)

可以看到创建了新的sqlsession，不是同一个sqlsesion

所以缓存是不可能生效的

也就是请求连接了两次数据库，

因为使用了默认的数据库连接池，查询完一次就commit，所以要让缓存生效，需要开启事务，让都查询完再commit，保证在同一个sqlsession中

## 开启事务

![image-20200829230248952](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200829230248952.png)

同一个sqlsession中

![image-20200829230417444](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200829230417444.png)