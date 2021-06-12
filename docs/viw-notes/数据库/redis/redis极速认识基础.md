[TOC]



# redis的基本概念

##引言和基本概念

> redis是基于内存存储数据和读取数据的
>
> redis是独立于tomcat外的缓存中间件
>
> 可以将存储在session中的共享数据统一存储在redis中

![image-20200911002342525](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200911002342525.png)

> redis和其他缓存中间件以及其他数据库的比较
>
> redis经常和memcached 比较，都可以存储键值映射，性能差不多，memached只能存储普通的字符串键
>
> redis还很方便的支持衡向扩展和垂直扩展

![image-20200911003142633](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200911003142633.png)

> 常见NoSQL

![image-20200911003455845](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200911003455845.png)

## 安装和连接

> redis有四种安装方式
>
> 1.docker安装（使用这种）
>
> 2.github源代码编译安装
>
> 3.直接安装  apt-get install(Ubuntu), yum install (RedHat) , brew install(Mac)
>
> 4.网页版redis体验

```shell
# 快速安装上手，可以看我的springboot+redis 那一篇，和springboot-docker
# 拉取 redis 镜像
> docker pull redis
# 运行 redis 容器
> docker run --name myredis -d -p6379:6379 redis
# 执行容器中的 redis-cli，可以直接使用命令行操作 redis
> docker exec -it myredis redis-cli 
```

> 我是已经下好了
>
> 你也可以去 `https://hub.daocloud.io/repos/beb958f9-ffb6-4f68-817b-c17e1ff476c3` 查找你需要的版本pull  

![image-20200911004605668](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200911004605668.png) 

> 或者编写docker-compose.yml
>
> opt项下 `vi docker-compose.yml`
>
> 然后执行  `docker-compose up -d`

```sh
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
```

> 或者是

```shell
[root@iZbp1h57xdtf34nsd47m65Z /]# docker run -d -p 6379:6379 --name redis1 f60d84d4d72c
```

> 进入容器

```shell
docker exec -it [id] bash
```

> redis内部命令行连接
>
> 或者 客户端连接
>
> 或者 java 连接
>
> 下面是 进入容器内部连接

![image-20200911012509202](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200911012509202.png)

> 简单测试

![image-20200911012553140](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200911012553140.png)

> RedisDesktopManager图形化界面连接redis

![image-20200911013230534](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200911013230534.png)



# redis常用命令

## redis的数据结构

基础数据结构：

> redis 有5种基础数据结构，分别是
>
> - 1.key-string: 字符串，一个key对应一个value
> - 2.key-hash:哈希，一个key对应一个map，常存储一个对象数据
> - 3.key-list: 列表，一个key对应一个列表，常存储使用list结构实现栈和队列结构
> - 4.key-set:集合，一个key对应一个集合，常用于交集，并集，差集的操作
> - 5.key-zset:  一个key对应一个有序集合，如 排行榜(分数存储)

![image-20200914213155465](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200914213155465.png)

另外三种数据结构：

> HyperLogLog:计算近似值
>
> GEO:地理位置
>
> BIT:一般存储的是一个字符串，存储的是 byte[]

redis所有的数据结构都是通过唯一的key来获取对应的value，不同类型在于 value的结构不一样

###  string（字符串）

> 简单图示

![image-20200914212922234](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200914212922234.png)

> 字符串结构常用存储一个值，比如缓存用户信息，将用户信息使用json序列化成字符串，然后将序列化后的字符串放进redis来缓存，
>
> 取出用户信息时也会经过一次反序列化



>  redis的字符串是简单动态字符串(SDS),是可以修改的字符串，内部结构类似于java的ArrayList,采用预分配冗余空间的方式减少频繁的内存分配，
>
> capacity是当前字符串实际分配的空间capacity，一般是要高于实际字符串长度len，当字符串小于1M时，扩容是加倍，当大于1M时，扩容每次只会多扩1M的空间，最大长度为512M

![image-20200914213958143](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200914213958143.png)



### list(列表)

> redis列表底层是链表(所以有些也称列表为链表)，相当于java中的 LinkedList,所有插入和删除操作非常快，时间复杂度O(1)，查找 索引到位就非常慢 时间复杂度O(n)
>
> 常用于来做异步队列，将需要延迟处理的任务结构体序列化字符串放入redis的列表，其他一个线程从这个列表中轮询数据处理

![image-20200914221519189](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200914221519189.png)



### hash(散列或称字典)

> 是一种用来保存键值对的抽象数据结构，比如存储对象user

![image-20200914221708126](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200914221708126.png)

> redis的散列相当于java中的HashMap,它是无序的。底层是哈希表， 数组+链表二维结构，
>
> 一个哈希表有多个节点，每一个节点保存一个键值对

![image-20200914221828261](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200914221828261.png)

### set(集合)

> redis的集合相当于java中的HashSet, 内部的键值对是无序唯一的(也就是无序不重复的)，它的内部实现相当于一个 value都是null的hash
>
> 比如set结构可以与来做存储活动中奖用户的id，因为有去重的效果，保证同一个用户不会中两次奖

![image-20200914222451942](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200914222451942.png)



### zset(有序列表)



> zset是redis特色的数据结构， 类似于java中的SortedSet 和HashMap的结合体，
>
> zset的键称之为 成员(number)，是唯一的
>
> zset的值称之为为 分数（score），为浮点数，做为value的排序权重
>
> 所以zset也就是 有序不重复的
>
> zset是唯一可以根据key来访问元素 也可以根据value来访问元素的结构
>
> ---
>
> 
>
> 它的内部实现用的是一种 跳跃列表的数据结构
>
> 比如用来 存储粉丝列表，value是粉丝的id，score是关注时间，
>
> 或者是 存储学生成绩



![image-20200914222831686](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200914222831686.png)



![image-20200914222904006](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200914222904006.png)



## 基本数据的常用命令



###  string的常用命令

```sh
# 1.设置一个key的value
set key value

# 2.取值
get key

# 3.设置多个key，批量操作
mset key value [key1 value1 key2 value2 ..]

# 4.获取所以key的值
mget key[key1 key2 ...]

```

![image-20200914233409861](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200914233409861.png)![image-20200914233604387](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200914233604387.png)



> 自增自减命令

```sh
# 5.自增命令（自增1） 
incr key 
# 6.自减命令（自减1） 
decr key 
# 7.自增指定数量
incrby key increment
# 8.自减指定数量
decyby key increment
```

![image-20200914233717675](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200914233717675.png)

![image-20200914233834100](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200914233834100.png)

```sh
# 9.设置值的同时指定生存时间，(单位秒)
setex key seconds value
# 10.设置值，如果当前key不存在，和set一样，放入redis，如果存在不执行任何操作（redis分布式锁时）
setnx key value
```

![image-20200914234959754](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200914234959754.png)![image-20200914235157874](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200914235157874.png)

```sh
# 11.追加操作（ key 已经存在，并且值为字符串，那么这个命令会把 value 追加到原来值（value）的结尾。 如果 key 不存在，那么它将首先创建一个空字符串的key，再执行追加操作，这种情况 APPEND 将类似于 SET 操作。）
append key value

# 12.获取指定key的长度
strlen key
```

![image-20200914235302334](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200914235302334.png)



### hash的常用命令

```sh
# 1.存储数据，设置hash里面一个字段的值（key是键，value是一个属性的值）
hset key field value

# 2.获取数据 获取hash中的field属性的值
hget key field

# 3.批量设置操作
hmset key field value [field1 value1 field2 value2 ...]

# 4.批量获取操作
hmget key field [field1 field2 ..]
```

![image-20200915004154864](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200915004154864.png)![image-20200915004201985](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200915004201985.png)



```sh
# 5.自增（指定自增值 可以为正也可以为负）注意field类型
hincrby key fiekd increment
```

![image-20200915004439065](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200915004439065.png)

```sh
# 6.设置值（如key-value不存在就添加，存在就不执行操作）
hsetnx key field value 

# 7.检查field 是否存在
hexists key field

# 8.删除key对应的每一个 field
hdel key field[...]
```

![image-20200915004912082](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200915004912082.png)

```sh
# 9获取当前hash结构中的全部的field和value
hgetall key
# 10.获取 当前hash结构中的全部field
hkeys key
# 11.获取当前hash结构中全部value
hvals key
# 12.获取当前hash结构中的field的数量
hlen key
```

![image-20200915005333386](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200915005333386.png)0

### list的常用命令

```sh
# 存储数据的命令
# 1. 从 左侧 插入数据
lpush key value[value ..]
# 2.从 右侧 插入数据
rpush key value[value ..]

# 3.存储数据，如果key不存在，不执行，key存在 但不是list结构，不执行任何操作。无法执行批量操作
lpushx key value    #当 队列存在 左边插入入队插入一个元素
rpushx key value	# 当队列存在  右  入队 插入元素

# 4. 插入数据(存储数据时，指定索引，覆盖当前索引的数据，index超过列表长度 也会失败)
lset key index value 

```



```sh
# 5.获取数据（弹栈方式获取数据，左边弹出数据）
lpop key
# 6. 获取数据（弹栈方式获取数据，右边弹出数据）
rpop key


# 7.获取指定索引范围的数据（start从0开始，stop输入-1，代表最后一个，-2代表倒数第二个）当下标超过list范围的时候不会产生error。 如果start比list的尾部下标大的时候，会返回一个空列表。 如果stop比list的实际尾部大的时候，Redis会当它是最后一个元素的下标。
lrange key start stop 

# 8.获取指定索引位置的数据
lindex key  index

# 9.获取整个list的长度
llen key
```

![image-20200916223432781](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200916223432781.png)



```sh
# 10.删除列表中的数据，返回值是被删除元素的个数
# count > 0: 从头往尾移除值为 value 的元素。左向右删
# count < 0: 从尾往头移除值为 value 的元素。右向左删
# count = 0: 移除所有值为 value 的元素。
lrem key  count value 

# 11. 保留列表中的数据（保留指定索引范围的数据，超过的会被删除），这里的 start stop和 上面 #7 命令的 start stop一样 
ltrim key start stop

# 12.将一个列表中最后一个数据，插入到另外一个列表的头位置(原子性)
rpoplpush list1 list2
```

![image-20200916224352876](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200916224352876.png)![image-20200916224456344](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200916224456344.png)![image-20200916224649962](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200916224649962.png)



### set的常用命令

```sh
# 1.存储数据
sadd key member [member01 member02 ..]

# 2.获取全部数据
smembers key

# 3.随机获取一个元素（获取的同时删除数据）count可以 指定删除数量
spop key count
```

![image-20200916225628281](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200916225628281.png)![image-20200916225635537](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200916225635537.png)



```sh
# 4.交集 （取多个set集合的交集）
sinter set1 set2 ...

# 5.并集(获取全部集合中的数据)
sunion set1 set2 ...

# 6.差集(获取多个集合中不一样的数据),顺序不一致结果不一致
sdiff set1 set2 ..
```

![image-20200917194657699](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200917194657699.png)![image-20200917194712070](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200917194712070.png)

![image-20200917194726295](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200917194726295.png)![image-20200917194753158](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200917194753158.png)



```sh
# 删除数据
srem key member[ member ..]

# 查看当前set集合中是否包含这个值
sismember key member
```

![image-20200917195029406](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200917195029406.png)![image-20200917195211543](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200917195211543.png)



### zset的常命令



```sh
# 1.添加数据
zadd key score member [score1 member1 score2 member2 ..]

# 2.修改 member score(若member存在key中，正常添加分数，若member不存在，这个命令就相当于zadd)
# zincrby salary 1 xiaobo25  结果是 xiaobo25的分数加1
zincrby key increment member 


```

![image-20200917202245495](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200917202245495.png)



```sh
# 3. 查看指定member 的score
zscore key member

# 4.获取zset 中的数据的数量
zcard key

# 5.查询score范围内的 member的数量
zcount key min max

# 6.删除zset中的 member
zrem key member [member1 member2 ..] 
```

![image-20200917202553479](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200917202553479.png)![image-20200917202618181](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200917202618181.png)

![image-20200917202653912](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200917202653912.png)![image-20200917202702709](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200917202702709.png)![image-20200917202744221](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200917202744221.png)![image-20200917202807205](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200917202807205.png)



```sh
# 7.根据score从小到大排序，获取指定范围内的数据(加withscores，会返回member的分数，不加withscores 只返回member)得分相同，将按字典排序。
zrange key start stop [withscores]

# 8.根据分数 从大到小的排序，获取指定范围内的数据
zrevrange key start stop [withscores]

# 9 根据score的范围返回数据（limit 相当于MySQL中的limit一样 再次筛选）
# 如ZRANGEBYSCORE zset (1 5  返回所有符合条件1 < score <= 5的成员；
zrangebyscore key min max [withscores] [limit offset count]

# 10.根据score的范围返回数据（limit 相当于MySQL中的limit一样 再次筛选）
zrangebyscore key max min [withscores] [limit offset count]


```

![image-20200917203814343](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200917203814343.png)![image-20200917203828533](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200917203828533.png)

![image-20200917203958088](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200917203958088.png)

![image-20200917204042901](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200917204042901.png)![image-20200917204136836](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200917204136836.png)

![image-20200917204230476](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200917204230476.png)

> `+inf` 最大，不管多大开始，`-inf` 最小 不管多小

---

##  key的常用命令



```sh
# 1.查看redis 全部的key
# pattern: *   通配符 全部
# pattern： xxx*  匹配前缀
#  pattern : *xxx  匹配后缀
keys pattern

# 2.查看某一个key是否存在（1 存在， 0 key 不存在）
exists key

# 删除key
del key [key1 key2 ...]


```

![image-20200917211959039](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200917211959039.png)

---

```sh
# 4.设置 key的生存时间  还能生存多久
expire key second   # 秒
pexpire key milliseconds  # 毫秒

# 5.指定生存到多少时间点， 
expireat key  timestamp  #  timestamp单位是秒
pexpireat key milliseconds

# 6.查看key的剩余生存时间(返回-2 当前key不存在， -1 当前key没有设置生存时间，)
ttl key
pttl key

# 7，删除key的生存时间(返回1 删除成功， 0 不存在生存时间，永远存在服务中， key不存在也会返回0)
persist key
```

![image-20200917213900486](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200917213900486.png)![image-20200917213915443](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200917213915443.png)![image-20200917214356403](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200917214356403.png)





##  对于库的简单操作

```sh
#  1.选择操作的库  redis 默认的有 16个库 , 0-15个库之间选择
select 0-15

# 2.移动key到另外一个库中
move key db

```

![image-20200917214709684](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200917214709684.png)![image-20200917214719956](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200917214719956.png)

![image-20200917214752410](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200917214752410.png)



```sh
# 3.清空当前所在库
flushdb

# 4,清空所有库
flushall

# 5.查看当前数据库有多个key
dbsize

# 6.查看最后一次操作的时间,保存到磁盘的时间（时间戳的形式）
lastsave

#7.实时监控redis服务接收到的目录
monitor
```

![image-20200917215045907](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200917215045907.png)





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





# 事务



## 介绍

1.redis的事务：会把命令放到一个queue中，提交时会把queue中的命令都提交 `exec`，取消就都取消  `discard` ，如果提交时中间有错误的，那这一次提交就有的成功，有的失败。

2.redis的事务命令行开启   ，`multi`

3.增加监听机制功能： 命令行 `match`

> 在开启事务之前，可以通过 `watch` 命令去监听多个命令，在开启事务后，有其他客户端修改了就直接取消再一次事务，watch监听器也自动消除， 就不需要 手动执行 `unwatch`



# 持久化





## AOF



1.aof记录的是每一次写操作的命令，前期rdb大，后期aof大。aof默认是关闭的。可以同时开启aof和rdb，在redis宕机重启时默认优先aof。先开启rdb后开启aof ，aof会覆盖掉rdb的内容，如果需要这种应重启时就开启



2.核心配置文件 `vi redis.conf`

```shell
# 开启redis aof持久化策略
appendonly no

# The name of the append only file (default: "appendonly.aof")
# aof 持久化的名称
appendfilename "appendonly.aof"

# 持久化时机
# appendfsync always 每执行一个写操作立即执行，安全但效率不行
appendfsync everysec # 每一秒，默认的
# appendfsync no  # 会根据操作系统环境等其他因素 执行持久化


```



## RDB

不保证实时持久化，会丢失，效率高。是默认的持久化，存储的是二进制文件。传输方便。可以互相通过rdb传输数据

1.rdb持久化的配置

2.打开配置文件 `vi redis.conf`

3.rdb核心配置

```shell
# rdb的核心配置

save 900 1  # 900秒内有1个key变化 就执行rdb
save 300 10 # 300...10......
save 60 10000 # 60....10000......

rdbcompression yes  # 开启rdb持久化的压缩

dbfilename dump.rdb # rdb持久化文件名称
```

4.如果是docker中的话，需要修改一下配置文件，在

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210201003801548.png" alt="image-20210201003801548" style="zoom:67%;" />





# 集群



## 主从



1.主从

​		单机版redis存在 性能瓶颈的问题

​		主负责写，从负责读，从 需要**主动**找主



2.配置文件

修改 `docker-compose.yml`

```shell
version= '3.1'
services:
        redis1:
                image: daocloud.io/library/redis:5.0.9
                restart: always
                container_name: redis1
                environment:
                        - TZ=Asia/Shanghai
                ports:
                        - 6001:6379
                volumes:
                			 - ./conf/redis1.conf:/usr/local/redis/redis.conf
                command: ["redis-server","/usr/local/redis/redis.conf"]
         redis2:
                image: daocloud.io/library/redis:5.0.9
                restart: always
                container_name: redis2
                environment:
                        - TZ=Asia/Shanghai
                ports:
                        - 6002:6379
                volumes:
                			 - ./conf/redis2.conf:/usr/local/redis/redis.conf
                links:
                			 - redis1.master;
                command: ["redis-server","/usr/local/redis/redis.conf"]
         redis3:
                image: daocloud.io/library/redis:5.0.9
                restart: always
                container_name: redis3
                environment:
                        - TZ=Asia/Shanghai
                ports:
                        - 6003:6379
                volumes:
                			 - ./conf/redis3.conf:/usr/local/redis/redis.conf
                links:
                			 - redis1.master;
                command: ["redis-server","/usr/local/redis/redis.conf"]
                   
                
                
```

从需要连接主，

 `links:

                			 - redis1.master;` 为了让从找到主



修改 `redis.conf`

```xml
# 从节点的配置
replicaof <masterip> <masterport>
```



3.启动

通过redis cli客户端输入命令行   `info` 查看信息  

![image-20210201220415500](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210201220415500.png)



## sentinel



1.哨兵可以解决集群架构中的单点redis故障问题

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210201012129823.png" alt="image-20210201012129823" style="zoom:67%;" />

哨兵直接互相连接，并连接自己的redis服务，如果mater挂，sentinel投票选举出master，

2.配置文件

修改docker-compose.yml文件配置内容

```yml
version= '3.1'
services:
        redis1:
                image: daocloud.io/library/redis:5.0.9
                restart: always
                container_name: redis1
                environment:
                        - TZ=Asia/Shanghai
                ports:
                        - 6001:6379
                volumes:
                			 - ./conf/redis1.conf:/usr/local/redis/redis.conf
                			 - ./conf/sentinel1.conf:/data/sentinel.conf
                command: ["redis-server","/usr/local/redis/redis.conf"]
         redis2:
                image: daocloud.io/library/redis:5.0.9
                restart: always
                container_name: redis2
                environment:
                        - TZ=Asia/Shanghai
                ports:
                        - 6002:6379
                volumes:
                			 - ./conf/redis2.conf:/usr/local/redis/redis.conf
                			 - ./conf/sentinel2.conf:/data/sentinel.conf
                links:
                			 - redis1.master;
                command: ["redis-server","/usr/local/redis/redis.conf"]
         redis3:
                image: daocloud.io/library/redis:5.0.9
                restart: always
                container_name: redis3
                environment:
                        - TZ=Asia/Shanghai
                ports:
                        - 6003:6379
                volumes:
                			 - ./conf/redis3.conf:/usr/local/redis/redis.conf
                			 - ./conf/sentinel3.conf:/data/sentinel.conf #映射哨兵配置文件
                links:
                			 - redis1.master;
                command: ["redis-server","/usr/local/redis/redis.conf"]
                   
                
                
```

然后目录下执行  `docker-compose down`  然后 `docker-compose up -d`

然后 data目录 执行 `redis-sentinel sentinel.conf` 启动 哨兵

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210201012442512.png" alt="image-20210201012442512" style="zoom:50%;" />

```shell
# 端口
port 26379

# 哨兵是否可以守护线程
daemonize no

# 指定master的名称 ip 和端口号 2个从节点
# 主节点中的sentinel配置 sentinel monitor mymaster 127.0.0.1 6379 2
sentinel monitor mymaster [master端口] 6379 2
# 哨兵监听时间,每隔多久监听一次redis架构  毫秒
sentinel down-after-milliseconds mymaster 30000
```





3.启动





## 集群



1.redis集群

 	redis集群可以保证sentinel的基本功能 还可以提升redis存储数据的能力

在redis集群中是无中心的

![image-20210201221745174](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210201221745174.png)



2.搭建redis集群

​	至少需要6个redis节点

修改 `docker-compose.yml`

```yaml
version: "3.1"
services:
	redis1:
		image: daocloud.io/library/redis:5.0.7
		restart: always
    container_name: redis1
    environment:
   			- TZ: Asia/Shanghai
   	port:
   			- 7001:7001
   			- 17001:17001  # 集群需要互相通信
   	volumes:
   			- ./conf/redis1.conf:/usr/local/redis/redis.conf
   	 command: ["redis-server","/usr/local/redis/redis.conf"]
	redis2:
		image: daocloud.io/library/redis:5.0.7
		restart: always
    container_name: redis2
    environment:
   			- TZ: Asia/Shanghai
   	port:
   			- 7002:7002
   			- 17002:17002  # 集群需要互相通信
   	volumes:
   			- ./conf/redis2.conf:/usr/local/redis/redis.conf
   	command: ["redis-server","/usr/local/redis/redis.conf"]
	redis3:
		image: daocloud.io/library/redis:5.0.7
		restart: always
    container_name: redis3
    environment:
   			- TZ: Asia/Shanghai
   	port:
   			- 7003:7003
   			- 17003:17003  # 集群需要互相通信
   	volumes:
   			- ./conf/redis3.conf:/usr/local/redis/redis.conf
   	command: ["redis-server","/usr/local/redis/redis.conf"]
	redis4:
		image: daocloud.io/library/redis:5.0.7
		restart: always
    container_name: redis4
    environment:
   			- TZ: Asia/Shanghai
   	port:
   			- 7004:7004
   			- 17004:17004  # 集群需要互相通信
   	volumes:
   			- ./conf/redis4.conf:/usr/local/redis/redis.conf
   	command: ["redis-server","/usr/local/redis/redis.conf"]
	redis5:
		image: daocloud.io/library/redis:5.0.7
		restart: always
    container_name: redis5
    environment:
   			- TZ: Asia/Shanghai
   	port:
   			- 7005:7005
   			- 17005:17005  # 集群需要互相通信
   	volumes:
   			- ./conf/redis5.conf:/usr/local/redis/redis.conf
   	command: ["redis-server","/usr/local/redis/redis.conf"]
	redis6:
		image: daocloud.io/library/redis:5.0.7
		restart: always
    container_name: redis6
    environment:
   			- TZ: Asia/Shanghai
   	port:
   			- 7006:7006
   			- 17006:17006  # 集群需要互相通信
   	volumes:
   			- ./conf/redis6.conf:/usr/local/redis/redis.conf
   	command: ["redis-server","/usr/local/redis/redis.conf"]
```

修改 `redis.conf`

从1-6都需要修改 redis.conf

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210201223031406.png" alt="image-20210201223031406" style="zoom:67%;" />

启动 docker,需要在 conf的上一级目录

相当于

```
docker_cluster
		conf
				redis1.conf
				redis2.conf
				redis3.conf
				redis4.conf
				redis5.conf
				redis6.conf
docker-compose.yml

```

执行 `docker-compose up -d`

让6个节点创建关联，需要如下，redis 版本需要>5.0，任意进入一个redis

![image-20210201223458181](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210201223458181.png)



### java连接 redis集群



1.使用 jedis 连接

![image-20210201224044431](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210201224044431.png)

![image-20210201224050783](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210201224050783.png)



# 常见问题



## 删除策略



1.key生存时间到了，redis不会立即删除

- 定期删除：
  - 每隔一段时间查看redis设置过期时间的key,会再100ms默认查看3个key
- 惰性删除：
  - 查询时redis看这个key是否过了生存时间，过了就删除这个key，返回用户空值



## 淘汰策略

1. 当添加时 redis内存满了，执行淘汰策略

```
# volatile-lru -> Evict using approximated LRU among the keys with an expire set.
# allkeys-lru -> Evict any key using approximated LRU.
# volatile-lfu -> Evict using approximated LFU among the keys with an expire set.
# allkeys-lfu -> Evict any key using approximated LFU.
# volatile-random -> Remove a random key among the ones with an expire set.
# allkeys-random -> Remove a random key, any key.
# volatile-ttl -> Remove the key with the nearest expire time (minor TTL)
# noeviction -> Don't evict anything, just return an error on write operations.

```

![image-20210201224750748](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210201224750748.png)



指定淘汰策略

在 `redis.conf` 配置文件中

下面是设置具体策略

![image-20210201224833740](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210201224833740.png)

设置最大内存

![image-20210201224940117](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210201224940117.png)



## 缓存问题

### 缓存穿透

![image-20210201232206070](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210201232206070.png)

### 缓存击穿

![image-20210201232345338](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210201232345338.png)

### 缓存雪崩

![image-20210201232550455](C:\Users\Pactera\AppData\Roaming\Typora\typora-user-images\image-20210201232550455.png)