[TOC]





# 基础



## 消息队列

1.MQ(Message Queue) 用来在信息传输过程中保存消息的容器。多用于分布式系统中的通信

在分布式系统中服务和服务通信一般是  rpc 和第三方(如mq)，也就是同步调用和异步调用

服务通过读写出入队的消息来通信，不需要专门连接来链接。

队列的使用除去了接收和发送应用程序同时执行的要求。

2.生产者和消费者

生产者：消息的发送方

消费者：消息的消费方

3.MQ的优点和缺点

> 优点：
>
> 1.应用解耦合，提升容错性和可维护性
>
> 2.异步提速，提升用户体验，和系统吞吐量
>
> 3.削峰填谷，高峰期产生的数据会积压在MQ，直到消费完MQ积压消息
>
> <img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210202215703738.png" alt="image-20210202215703738" style="zoom:50%;" />
>
> 缺点：
>
> 1.系统可用性降低，外部依赖增多，需要保证MQ的高可用
>
> 2.系统复杂度增加，需要保证信息不被重复消费，保证信息不丢失，保证信息传递的有序
>
> 3.消息处理一致性问题
>
> ==需要考虑系统是否真的需要MQ，在解耦合 提速  削峰的效果比较好==
>
> ==需要考虑生产者不需要从消费者处获取反馈，才能达到异步的效果==
>
> ==容许短暂的不一致性==



## AMQP和JMS

1.AMQP

AMQP是一种协议，定义的是数据交互的格式，不定义具体的API，不定义具体的语言实现

AMQP的消息模式更加丰富

2.JMS

JMS是一种接口规范，是JAVA面向消息中间件的API，对消息统一管理，指定了JAVA语言实现，类似于JDBC

JMS的消息模式定义了两种



<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210202220700262.png" alt="image-20210202220700262" style="zoom:50%;" />

## 相同产品比较

![image-20210202220922549](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210202220922549.png)



## RabbitMQ



### 相关概念介绍

1. 基础架构图

![image-20210202221456052](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210202221456052.png)

2. RabbitMQ的相关概念

`Broker`:接收消息和分发消息的应用，RabbitServer = BrokerServer

`Virtual host`:  虚拟主机 相当于MySQL的每一个库，互相不干扰。多个用户使用同一个RabbitMQ可以创建多个虚拟主机，每一个用户在自己的虚拟主机下创建交换机 和队列

`connection`: publisher /consumer 和 broker之间的tcp连接

`channel:` 连接通道，减少tcp连接的开销， AMQP method 包含channel id帮助客户端 和message broker 识别 channel

![image-20210202224451762](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210202224451762.png)



### 安装

docker会比较方便，

1.安装编译环境

```
yum install gcc
yum install socat 
```

2.安装erlang 和rabbitmq

使用npm安装包安装，上传到服务器

![image-20210202211258904](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210202211258904.png)

```shell
#安装erlang
rpm -ivh erlang-22.0.7-1.el7.x86_64.rpm
#查看状态
erl

#安装rabbitmq
rpm -ivh rabbitmq-server-3.7.17-1.el7.noarch.rpm
#查看状态
systemctl status rabbitmq-server
```

3.开启管理界面

```shell
# 开启管理界面
rabbitmq-plugins enable rabbitmq_management
# 修改配置文件，放开用户
cd /usr/share/doc/rabbitmq-server-3.7.17
cp rabbitmq.config.example /etc/rabbitmq/rabbitmq.config
vi /etc/rabbitmq/rabbitmq.config
```

![image-20210202213226116](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210202213226116.png)

4.启动

```shell
systemctl start rabbitmq-server #启动
systemctl stop rabbitmq-server #停止
systemctl restart rabbitmq-server
systemctl status rabbitmq-server #状态
```



rabbit.app是rabbitmq的核心配置文件，如里面有端口什么的

![image-20210202212820633](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210202212820633.png)

5.开启安全组端口号  rabbitmq 15672

如果不知道 可以根据 pid查询端口   `netstat -antup|grep [pid]`

6.访问

![image-20210202214506487](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210202214506487.png)

用户名和密码是 guest

![image-20210202214552904](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210202214552904.png)



7.创建用户

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210202214644514.png" alt="image-20210202214644514" style="zoom:50%;" />

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210202224812269.png" alt="image-20210202224812269" style="zoom:50%;" />



角色说明：

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210202214739286.png" alt="image-20210202214739286" style="zoom:50%;" />

8.虚拟主机说明

像mysql拥有数据库的概念并且可以指定用户对库和表等操作的权限。RabbitMQ也有类似的权限管理；在RabbitMQ中可以虚拟消息服务器Virtual Host，每个Virtual Hosts相当于一个相对独立的RabbitMQ服务器，每个VirtualHost之间是相互隔离的。exchange、queue、message不能互通。 相当于mysql的db。Virtual Name一般以/开头。

![image-20210202214917121](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210202214917121.png)

添加虚拟机

![image-20210202224912883](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210202224912883.png)

然后设置权限就可以

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210202224950688.png" alt="image-20210202224950688" style="zoom:50%;" />

![image-20210202225059386](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210202225059386.png)

9.管控台

查看配置文件路径等信息， 点击node

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210202225419128.png" alt="image-20210202225419128" style="zoom:50%;" />

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210202225551137.png" alt="image-20210202225551137" style="zoom:50%;" />

集群端口是 25672 管理控制台是15672



## RabbitMQ相关流程



### rabbitmq大概运转流程

生产者：

1. 生产者创建连接，开启一个通道，连接到rabbitmq server
2. 声明队列并设置相关属性，如是否持久化 是否自动删除
3. 将路由键与队列binding
4. 发送消息到 rabbitmq broker
5. 关闭信道
6. 关闭连接

消费者：

1. 消费者创建连接，开启一个channel，连接 rabbitmq server
2. 向broker请求消费相应队列中的消息，设置相应的回调函数
3. 等待broker回应关闭传递到队列中的消息，消费者接收消息
4. 确认(ack，自动确认) 接收到的消息
5. rabbitmq 从队列中删除相应已经被确认的消息
6. 关闭信道
7. 关闭连接



流程图

![image-20210202230204141](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210202230204141.png)



### 生产者流转过程



### 消费者流转过程



## RabbitMQ入门



### 生产者和消费者-简单模式

1.生产者

```java

  /**
     * 1.创建连接工厂
     * 2.设置参数
     * 3.创建连接connection
     * 4.创建channel
     * 5.创建队列
     * 6.发送消息
     * 7.释放资源
     */
    @Test
    public  void testProducerHello() throws IOException, TimeoutException {
        ConnectionFactory connectionFactory  = new    ConnectionFactory();
        connectionFactory.setHost("121.40.xxx.xxx");
        connectionFactory.setPort(5672);
        connectionFactory.setVirtualHost("/viw");
        connectionFactory.setUsername("xiaobo");
        connectionFactory.setPassword("xiaobo");
        Connection connection = connectionFactory.newConnection();
        Channel channel = connection.createChannel();
        // String queue-队列的名称, boolean durable-是否持久化, boolean exclusive-是否排它 是否
        // connection关闭时关闭队列, boolean autoDelete-是否自动删除  没有 consumer时是否自动删除, Map<String, Object> arguments-其他参数信息
        channel.queueDeclare("myqueue1",true,false,false,null);
        //void basicPublish(String exchange, String routingKey, BasicProperties props, byte[] body)
        //简单模式的exchange 默认时  routingKey相应和queuename一样
        channel.basicPublish("","myqueue1",null,"Hello".getBytes());
        channel.close();
        connection.close();
    }


```



2.消费者

```java
public static void main(String[] args) throws Exception {
        ConnectionFactory connectionFactory = new ConnectionFactory();
        connectionFactory.setHost("121.40.xxx.xxx");
        connectionFactory.setPort(5672);
        connectionFactory.setVirtualHost("/viw");
        connectionFactory.setUsername("xiaobo");
        connectionFactory.setPassword("xiaobo");
        Connection connection = connectionFactory.newConnection();
        Channel channel = connection.createChannel();
        // String queue-队列的名称, boolean durable-是否持久化, boolean exclusive-是否排它 是否
        // connection关闭时关闭队列, boolean autoDelete-是否自动删除  没有 consumer时是否自动删除, Map<String, Object> arguments-其他参数信息
        channel.queueDeclare("myqueue1", true, false, false, null);
        //String queue-队列名称, boolean autoAck-自动确认, Consumer callback-回调函数
        Consumer consumer = new DefaultConsumer(channel) {
            /**
             * 回调方法，当收到消息 自动执行此方法
             * @param consumerTag  标识
             * @param envelope  获取exchange等信息
             * @param properties    配置信息
             * @param body 数据
             * @throws IOException
             */
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                System.out.println("consumerTag:" + consumerTag + "getExchange:" + envelope.getExchange() + "getRoutingKey:" + envelope.getRoutingKey() + ":" + properties + "==body:" + new String(body));
            }
        };
        channel.basicConsume("myqueue1", true, consumer);
    }
```



3.简单模式模型

![image-20210205223850711](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210205223850711.png)

- p:生产者produce
- c:消费者consumer
- 中间的是消息队列

==消费者保持queue的监听，生产者一发送消息，消费者就可以取得消息==

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210205225004536.png" alt="image-20210205225004536" style="zoom:67%;" />

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210205225057396.png" alt="image-20210205225057396" style="zoom:67%;" />









## RabbitMQ的工作模式

> 一共有6种消息分发模式分别是：
>
> 简单模式，WORK模式，Pub/Sub发布订阅模式，Routing模式，Topics主题模式，RPC远程调用模式（严格意义不算）



### work模式



1.模式概念图

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210205224502284.png" alt="image-20210205224502284" style="zoom:50%;" />

c1和c2是竞争的关系，只有一个可以取得队列中的消息，比简单模式多了一个消费端

**应用场景**:可以提高任务处理的速度，如短信服务，随便哪个消费者取得，发送就可以了。

2.编写生产者

==和简单模式的生产者一样==

3.编写消费者1

==和简单模式的消费者一样==

4.编写消费者2

==和上面的消费者1是一样==

5.同时启动两个消费者

![image-20210205230327453](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210205230327453.png)

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210205230338888.png" alt="image-20210205230338888" style="zoom:50%;" />

6.启动生产者

==可以肯定消费者取得消息了，轮询取==

多个消费者监听同一个队列，

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210205230420978.png" alt="image-20210205230420978" style="zoom:50%;" />

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210205230441583.png" alt="image-20210205230441583" style="zoom:50%;" />









### 发布订阅模式



1.模式模型图

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210205230658846.png" alt="image-20210205230658846" style="zoom:50%;" />

- x是交换机，前面的工作模式和简单描述是默认的交换机

![image-20210205230753512](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210205230753512.png)



2.生产者

```java
 @Test
    public void testProducerHello() throws IOException, TimeoutException {
        ConnectionFactory connectionFactory = new ConnectionFactory();
        connectionFactory.setHost("121.40.xxx.xxx");
        connectionFactory.setPort(5672);
        connectionFactory.setVirtualHost("/viw");
        connectionFactory.setUsername("xiaobo");
        connectionFactory.setPassword("xiaobo");
        Connection connection = connectionFactory.newConnection();
        // 创建channe
        Channel channel = connection.createChannel();
        //创建交换机
        /**
         String exchange, -- 交换机名称
         String type, -- 交换机类型（需要手写类型，不推荐）
         BuiltinExchangeType type, 推荐这种enum
                DIRECT("direct")定向, FANOUT("fanout")广播，发送消息到每一个与之绑定的队列
                TOPIC("topic")通配符, HEADERS("headers")参数匹配;
         boolean durable, 是否持久化
         boolean autoDelete, 是否自动删除
         boolean internal, 是否内部使用 一般false
         Map<String, Object> arguments  参数列表
         */
        String exchangeName = "test_exchanege";
        channel.exchangeDeclare(exchangeName, BuiltinExchangeType.FANOUT,true,false,false,null);
        //创建队列
        String queueName1 = "fanout_queue1";
        channel.queueDeclare(queueName1,true,false,false,null);
        String queueName2 = "fanout_queue2";
        channel.queueDeclare(queueName2,true,false,false,null);
        //绑定队列 和交换机
        /**
         * (String queue,-需要绑定的当前队列名称
         *                             String exchange,  交换机名称
         *                             String routingKey,  路由键-
         *                                      如果 exchange = fanout  默认为 “” 空字符串
         *                             Map<String, Object> arguments) 参数
         */
        channel.queueBind(queueName1,exchangeName,"");
        channel.queueBind(queueName2,exchangeName,"");
        // 发送消息
        String body = "日志信息，调用了xxx的接口a";
        channel.basicPublish(exchangeName,"",null,body.getBytes());
        //释放资源
        channel.close();
        connection.close();
    }

```



3.消费者1

```java

{
        ConnectionFactory connectionFactory = new ConnectionFactory();
        connectionFactory.setHost("121.40.xxx.xxx");
        connectionFactory.setPort(5672);
        connectionFactory.setVirtualHost("/viw");
        connectionFactory.setUsername("xiaobo");
        connectionFactory.setPassword("xiaobo");
        Connection connection = connectionFactory.newConnection();
        // 创建队列
        Channel channel = connection.createChannel();
        String queueName1 = "fanout_queue1";
        String queueName2 = "fanout_queue2";
        Consumer consumer = new DefaultConsumer(channel) {
            /**
             * 回调方法，当收到消息 自动执行此方法
             * @param consumerTag  标识
             * @param envelope  获取exchange等信息
             * @param properties    配置信息
             * @param body 数据
             * @throws IOException
             */
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                /**
                 * consumerTag:amq.ctag-qN4O2pE1Y3HgqXhEX2istAgetExchange:getRoutingKey:myqueue1:#contentHeader<basic>(content-type=null, content-encoding=null, headers=null, delivery-mode=null,
                 * priority=null, correlation-id=null, reply-to=null, expiration=null, message-id=null, timestamp=null, type=null, user-id=null, app-id=null, cluster-id=null)==body:Hello
                 */
                System.out.println(new String(body));
                System.out.println("队列1的消息要做的事情。。。");
            }
        };
        channel.basicConsume(queueName1, true, consumer);
    }
```

4.消费者2



```java


{
        ConnectionFactory connectionFactory = new ConnectionFactory();
        connectionFactory.setHost("121.40.xxx.xxx");
        connectionFactory.setPort(5672);
        connectionFactory.setVirtualHost("/viw");
        connectionFactory.setUsername("xiaobo");
        connectionFactory.setPassword("xiaobo");
        Connection connection = connectionFactory.newConnection();
        // 创建队列
        Channel channel = connection.createChannel();
        String queueName1 = "fanout_queue1";
        String queueName2 = "fanout_queue2";
        Consumer consumer = new DefaultConsumer(channel) {
            /**
             * 回调方法，当收到消息 自动执行此方法
             * @param consumerTag  标识
             * @param envelope  获取exchange等信息
             * @param properties    配置信息
             * @param body 数据
             * @throws IOException
             */
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                /**
                 * consumerTag:amq.ctag-qN4O2pE1Y3HgqXhEX2istAgetExchange:getRoutingKey:myqueue1:#contentHeader<basic>(content-type=null, content-encoding=null, headers=null, delivery-mode=null,
                 * priority=null, correlation-id=null, reply-to=null, expiration=null, message-id=null, timestamp=null, type=null, user-id=null, app-id=null, cluster-id=null)==body:Hello
                 */
                System.out.println(new String(body));
                System.out.println("队列2的消息要做的事情。。。");
            }
        };
        channel.basicConsume(queueName2, true, consumer);
    }
```



5.测试

​		和工作模式的区别是通过交换机指定的规则，再通过把队列绑定到交换机上，不同的消费者可以监听不同的队列(自己的队列) 处理不同的业务逻辑

​		这里测试的是广播规则，这种规则不需要指定路由规则，空字符串即可

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210206002447016.png" alt="image-20210206002447016" style="zoom:50%;" />

​		消费者1和消费者2可以根据我们指定的规则做不同的事情，进行消息的分发。

​		![image-20210206002636536](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210206002636536.png)







### Routing模式

​		简单一句话就是 生产者发送的同一个消息到了不同的消费者哪里，做不同的业务处理。

1.模型概念图和介绍



<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210206003745405.png" alt="image-20210206003745405" style="zoom:50%;" />

![image-20210206003712299](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210206003712299.png)

- P:生产者
- C1 C2 不同的消费者
- X：交换机
- 队列
- 路由规则：根据Routing Key 来 发送消息到不同的队列
  - 这种==Direct==交换机类型指定 发送消息的routing key 和 后面队列绑定的routing key保持一致





2.生产者

```java
{
        ConnectionFactory connectionFactory = new ConnectionFactory();
        connectionFactory.setHost("121.40.xxx.xxx");
        connectionFactory.setPort(5672);
        connectionFactory.setVirtualHost("/viw");
        connectionFactory.setUsername("xiaobo");
        connectionFactory.setPassword("xiaobo");
        Connection connection = connectionFactory.newConnection();
        // 创建channe
        Channel channel = connection.createChannel();
        //创建交换机
        /**
         String exchange, -- 交换机名称
         String type, -- 交换机类型（需要手写类型，不推荐）
         BuiltinExchangeType type, 推荐这种enum
                DIRECT("direct")定向, FANOUT("fanout")广播，发送消息到每一个与之绑定的队列
                TOPIC("topic")通配符, HEADERS("headers")参数匹配;
         boolean durable, 是否持久化
         boolean autoDelete, 是否自动删除
         boolean internal, 是否内部使用 一般false
         Map<String, Object> arguments  参数列表
         */
        String exchangeName = "test_direct";
        // 这种交换机类型指定 发送消息的routing key 和 后面队列绑定的routing key保持一致
        channel.exchangeDeclare(exchangeName, BuiltinExchangeType.DIRECT,true,false,false,null);
        //创建队列
        String queueName1 = "direct_queue1";
        channel.queueDeclare(queueName1,true,false,false,null);
        String queueName2 = "direct_queue2";
        channel.queueDeclare(queueName2,true,false,false,null);
        //绑定队列 和交换机
        /**
         * (String queue,-需要绑定的当前队列名称
         *                             String exchange,  交换机名称
         *                             String routingKey,  路由键-
         *                                      如果 exchange = fanout  默认为 “” 空字符串
         *                             Map<String, Object> arguments) 参数
         */
        // 这里就需要指定绑定队列的routing key 在测试中 队列1 绑定的routing key 为 error
        channel.queueBind(queueName1,exchangeName,"error");

        //队列2 绑定的routing key 为  info  error  warning
        channel.queueBind(queueName2,exchangeName,"info");
        channel.queueBind(queueName2,exchangeName,"error");
        channel.queueBind(queueName2,exchangeName,"warning");
        // 发送消息
        String body = "日志信息，调用了xxx的接口a 我需要走 info 的routing key";
        channel.basicPublish(exchangeName,"info",null,body.getBytes());
        //释放资源
        channel.close();
        connection.close();
    }
```

==在这里我们指定了队列1 和队列2 的routing key ，并且发送了一条routing key 为 info 的消息 ，交换机会把 这一天消息发送到与之绑定的routing key 为info D 队列==

​		下面是交换机中的队列

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210206005714896.png" alt="image-20210206005714896" style="zoom:50%;" />

​		执行了上面的代码后，队列2中多了一条消息

![image-20210206005816699](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210206005816699.png)







3.消费者



​	启动了两个消费者，两个除了队列名其他都一样。

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210206083147028.png" alt="image-20210206083147028" style="zoom: 50%;" />

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210206083216125.png" alt="image-20210206083216125" style="zoom:50%;" />

​		可以看到生产者发送的这一天路由键为 ==info==的消息， 只有队列2可以取到。

```java
// consumer1
public static void main(String[] args) throws Exception {
        ConnectionFactory connectionFactory = new ConnectionFactory();
        connectionFactory.setHost("121.40. ");
        connectionFactory.setPort(5672);
        connectionFactory.setVirtualHost("/viw");
        connectionFactory.setUsername("xiaobo");
        connectionFactory.setPassword("xiaobo");
        Connection connection = connectionFactory.newConnection();
        // 创建队列
        Channel channel = connection.createChannel();
        String queueName1 = "direct_queue1";
        String queueName2 = "direct_queue2";
        Consumer consumer = new DefaultConsumer(channel) {
            /**
             * 回调方法，当收到消息 自动执行此方法
             * @param consumerTag  标识
             * @param envelope  获取exchange等信息
             * @param properties    配置信息
             * @param body 数据
             * @throws IOException
             */
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                System.out.println(new String(body));
                System.out.println("队列1的消息要做的事情。。。");
            }
        };
        channel.basicConsume(queueName1, true, consumer);
    }
```



```java
// consumer2
public static void main(String[] args) throws Exception {
        ConnectionFactory connectionFactory = new ConnectionFactory();
        connectionFactory.setHost("121.40.");
        connectionFactory.setPort(5672);
        connectionFactory.setVirtualHost("/viw");
        connectionFactory.setUsername("xiaobo");
        connectionFactory.setPassword("xiaobo");
        Connection connection = connectionFactory.newConnection();
        // 创建队列
        Channel channel = connection.createChannel();
        String queueName1 = "direct_queue1";
        String queueName2 = "direct_queue2";
        Consumer consumer = new DefaultConsumer(channel) {
            /**
             * 回调方法，当收到消息 自动执行此方法
             * @param consumerTag  标识
             * @param envelope  获取exchange等信息
             * @param properties    配置信息
             * @param body 数据
             * @throws IOException
             */
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                /**
                 * consumerTag:amq.ctag-qN4O2pE1Y3HgqXhEX2istAgetExchange:getRoutingKey:myqueue1:#contentHeader<basic>(content-type=null, content-encoding=null, headers=null, delivery-mode=null,
                 * priority=null, correlation-id=null, reply-to=null, expiration=null, message-id=null, timestamp=null, type=null, user-id=null, app-id=null, cluster-id=null)==body:Hello
                 */
                System.out.println(new String(body));
                System.out.println("队列2的消息要做的事情。。。");
            }
        };
        channel.basicConsume(queueName2, true, consumer);
    }
```





### Topics通配符模式



1.模型图和概念

http://www.xiaobo.life/archives/767/#1.3.5%20%E8%BF%90%E8%A1%8C%E6%9C%BA%E5%88%B6

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210206083548029.png" alt="image-20210206083548029" style="zoom:50%;" />

![img](http://www.xiaobo.life/usr/uploads/2020/08/2078137544.png)

==符号`"#"`和符号
`"*"`。#匹配0个或多个单词，
*匹配一个单词）==



2.生产者



```java
@Test
    public void testProducerHello() throws IOException, TimeoutException {
        ConnectionFactory connectionFactory = new ConnectionFactory();
        connectionFactory.setHost("121.40. ");
        connectionFactory.setPort(5672);
        connectionFactory.setVirtualHost("/viw");
        connectionFactory.setUsername("xiaobo");
        connectionFactory.setPassword("xiaobo");
        Connection connection = connectionFactory.newConnection();
        // 创建channe
        Channel channel = connection.createChannel();
        //创建交换机
        /**
         String exchange, -- 交换机名称
         String type, -- 交换机类型（需要手写类型，不推荐）
         BuiltinExchangeType type, 推荐这种enum
                DIRECT("direct")定向, FANOUT("fanout")广播，发送消息到每一个与之绑定的队列
                TOPIC("topic")通配符, HEADERS("headers")参数匹配;
         boolean durable, 是否持久化
         boolean autoDelete, 是否自动删除
         boolean internal, 是否内部使用 一般false
         Map<String, Object> arguments  参数列表
         */
        String exchangeName = "test_topic";
        channel.exchangeDeclare(exchangeName, BuiltinExchangeType.TOPIC,true,false,false,null);
        //创建队列
        String queueName1 = "topic_queue1";
        channel.queueDeclare(queueName1,true,false,false,null);
        String queueName2 = "topic_queue2";
        channel.queueDeclare(queueName2,true,false,false,null);
        //绑定队列 和交换机
        /**
         * (String queue,-需要绑定的当前队列名称
         *                             String exchange,  交换机名称
         *                             String routingKey,  路由键-
         *                                      如果 exchange = fanout  默认为 “” 空字符串
         *                             Map<String, Object> arguments) 参数
         */
        // routing key 队列1 匹配 所有  error  队列2 匹配
        channel.queueBind(queueName1,exchangeName,"#.error");
        channel.queueBind(queueName1,exchangeName,"order.*");
        channel.queueBind(queueName2,exchangeName,"*.*");
        // 发送消息
        String body = "日志信息,发送了一条  order.info 通配符的信息 ";
        channel.basicPublish(exchangeName,"order.info",null,body.getBytes());
        //释放资源
        channel.close();
        connection.close();
    }
```



队列1 和队列2 都可以收到信息

![image-20210206084956551](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210206084956551.png)



3.消费者

```java
public static void main(String[] args) throws Exception {
        ConnectionFactory connectionFactory = new ConnectionFactory();
        connectionFactory.setHost("121.40.xxx");
        connectionFactory.setPort(5672);
        connectionFactory.setVirtualHost("/viw");
        connectionFactory.setUsername("xiaobo");
        connectionFactory.setPassword("xiaobo");
        Connection connection = connectionFactory.newConnection();
        // 创建队列
        Channel channel = connection.createChannel();
        String queueName1 = "topic_queue1";
        String queueName2 = "topic_queue2";
        Consumer consumer = new DefaultConsumer(channel) {
            /**
             * 回调方法，当收到消息 自动执行此方法
             * @param consumerTag  标识
             * @param envelope  获取exchange等信息
             * @param properties    配置信息
             * @param body 数据
             * @throws IOException
             */
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                System.out.println(new String(body));
                System.out.println("队列1的消息要做的事情。。。通配符是\"#.error\" 和\"order.*\" ");
            }
        };
        channel.basicConsume(queueName1, true, consumer);
    }
```

==消费者1和消费者2是一样的，除了 channel.basicConsume里面的参数不一样==

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210206085538559.png" alt="image-20210206085538559" style="zoom:50%;" />

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210206085547307.png" alt="image-20210206085547307" style="zoom:50%;" />



4.总结



==Topic主题模式可以实现Pub/Sub发布和订阅模式  也可以实现Routing 路由模式。只需要设置通配符即可==









## springboot+rabbitmq



1.

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210206090129206.png" alt="image-20210206090129206" style="zoom:50%;" />

2.

https://gitee.com/xiaobo97/viw-simple-lab/tree/master/springboot/springboot-13-amqp

