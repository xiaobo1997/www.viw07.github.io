[TOC]

# java连接redis基本操作



> 有两个客户端可以提供选择
>
> - 1.Jedis
> - 2.Lettuce

## Jedis连接操作redis



### 1.创建工程引入依赖

> 创建maven项目
>
> 引入依赖

```xml
  <dependencies>
        <!-- https://mvnrepository.com/artifact/redis.clients/jedis -->
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>2.9.0</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.16.20</version>
        </dependency>
    </dependencies>
```

### 2.连接并操作



#### 字节数组形式

> 简单测试连接并存储string

```java
@Test
    public void testA(){
        //1.连接redis
        Jedis jedis = new Jedis("121.40.178.114",6379);
        //2.操作
        jedis.set("name","小波01");
        // 3.释放资源
        jedis.close();
    }
```

![image-20200917225029704](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200917225029704.png)

> 以字节数组存储一个对象

```java
/**
 * @Author: xiaobo
 * @Date: 2020/9/17 22:58
 */
@Data
@NoArgsConstructor // 无参构造
@AllArgsConstructor // 有参构造
public class User implements Serializable {
    private Integer id;
    private String lastName;
    private Date birthday;
}

```

```java
/**
 * @Author: xiaobo
 * @Date: 2020/9/17 22:59
 */
public class SaveBean {
    Jedis jedis = new Jedis("121.40.178.114", 6379);
    // 存储对象  以 字节数组存储 redis
    @Test
    public void setByteArray() {
        //创建 key(String)-value(Object)
        String key = "user";
        User value = new User(1,"xiaobo01",new Date());
        //key-value转 byte[]
        byte[] byteKey = SerializationUtils.serialize(key);
        byte[] byteValue = SerializationUtils.serialize(value);
        // 存储
        jedis.set(byteKey,byteValue);
        //关闭连接
        jedis.close();
    }
}

```

> 获取字节数组对象

```java
 @Test
    public void getByteArray(){
        String key = "user";
        byte[] byteKey = SerializationUtils.serialize(key);
        byte[] value = jedis.get(byteKey);
        User user = (User) SerializationUtils.deserialize(value);
        System.out.println(user);
        jedis.close();
    }
```



#### String形式

> 通过string存储数据

```java
  @Test
    public void saveBean(){

        // 实例化key(Srting)-value(User)
        String strKey = "stringuser";
        User user = new User(2,"xiaobo02",new Date());
        // 将value转json 导入 fastjson
         String strValue = JSON.toJSONString(user);
        // 存储
        jedis.set(strKey,strValue);
        jedis.close();
    }

```

> 获取数据
>
> 如果前端需要json格式直接返回json ，如果需要成实体类 就转成实体类

```java
  /**
     * 以json形式获取
     */
    @Test
    public   void getBean(){
        // 设置key
        String string = "stringuser";
        // 查询value
//       String value =  jedis.get(string);
        // 将value反序列化 user
        User user = JSON.parseObject(jedis.get(string), User.class);
        System.out.println(user);
        jedis.close();
    }
```

### 3.jedis连接池的操作

> 简单使用

```java
/**
     * 简单使用
     */
    @Test
    public  void pool(){
        // 创建连接池
        JedisPool pool  = new JedisPool("121.40.178.114",6379);
        // 通过连接池获取jedis对象
        Jedis jedis = pool.getResource();
        // 操作
        String value = jedis.get("stringuser");
        System.out.println(value);
        // 释放资源
        jedis.close();
    }
```



> 设置参数获取连接

```java
 @Test
    public void poolArgs() {
        //JedisPool pool = new JedisPool("121.40.178.114", 6379);
        // 创建连接池配置信息
        GenericObjectPoolConfig genericObjectPoolConfig = new GenericObjectPoolConfig();
        genericObjectPoolConfig.setMaxTotal(100); // 连接池的最大活跃数
        genericObjectPoolConfig.setMaxIdle(10); // 最大空闲数
        genericObjectPoolConfig.setMinIdle(5); // 最小空闲数
        genericObjectPoolConfig.setMaxWaitMillis(5); // 连接池空后等待多久超时异常
        //创建连接池
        JedisPool pool = new JedisPool(genericObjectPoolConfig,"121.40.178.114", 6379);
        // 获取jedis
        Jedis jedis = pool.getResource();
        // 操作
        String value = jedis.get("stringuser");
        System.out.println(value);
        // 释放资源
        jedis.close();
    }
```

### 4.redis的管道操作

>操作redis的时候，执行一个命令过程是 发送请求到reids服务器， 这个过程还会有比如网络的影响，redis再返回给客户端响应
>
>如果一次性执行多个命令 效率就很低，所以先将管道放到pipeline，然后再一次性的将全部命令都发送到redis服务器，redis一次性把响应结果给客户端，提高并发

> 客户端发送请求 得到 redis服务响应
>
> 如果执行三次操作，效率低

![image-20200918000419929](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200918000419929.png)

> 使用管道
>
> 三条命令一起发送 再等待 reids服务响应，而不需要一次请求等待一个响应
>
> 提高了性能

![image-20200918000548958](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200918000548958.png)

> 不使用pip

```java
        // 1.创建连接池
        long currentTimeMillis = System.currentTimeMillis();
        JedisPool pool = new JedisPool("121.40.178.114", 6379);

        // 不所有管道 55472ms
        // 获取对象
//        Jedis jedis = pool.getResource();
        // 执行操作
//        for (int i = 0; i < 1000; i++) {
//            jedis.incr("pipUse");
//        }
        //释放资源
    //    jedis.close();
```

>使用pip

```java
 //======使用管道
        // 304ms

        // 获取对象
        Jedis jedis = pool.getResource();
        // 创建管道
        Pipeline pipeline = jedis.pipelined();
        // 执行操作
        for (int i = 0; i < 1000; i++) {
            pipeline.incr("pipUse");
        }
        List<Object> list = pipeline.syncAndReturnAll();
        //释放资源
        jedis.close();
        System.out.println(System.currentTimeMillis() - currentTimeMillis + "使用pip时间");
```

---

# redis其他配置文件内容和集群配置文件



## 修改redis配置文件 映射Docker宿主机配置文件



修改docker-compose.yml

```yml
version= '3.1'
services:
        redis:
                image: daocloud.io/library/redis:5.0.9
                restart: always
                container_name: redis2
                environment:
                        - TZ=Asia/Shanghai
                ports:
                        - 6000:6379
                volumes:
                			 - ./conf/redis.conf:/usr/local/redis/redis.conf
                command: ["redis-server","/usr/local/redis/redis.conf"]
```

==volumes：关联配置文件。command：容器启动执行命令==

![image-20200921230942337](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200921230942337.png)

创建配置文件 让宿主机和容器中的redis配置文件关联

![image-20210130115710583](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210130115710583.png)



## redis的AUTH

1.` vi redis.conf`

```
requirepass  [密码]
```

2.之后操作就需要先使用密码，否则权限不够

![image-20210201001231472](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210201001231472.png)

还可以通过输入命令时指定密码,就可以不修改密码的前提下修改

![image-20210201001501366](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210201001501366.png)



3.使用jedis连接操作redis也需要使用带有密码参数的构造器

```
# 1. 不推荐
jedis.auth("xxx");
# 2. jedis连接池
使用jedis连接池带密码的构造函数
```



