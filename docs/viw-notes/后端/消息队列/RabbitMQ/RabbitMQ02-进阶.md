[TOC]



# rabbitmq进阶



## 消息可靠性投递



### confirm

1. 概念

rabbitmq提供了两种方式来控制消息的投递可靠性模型

- confirm 确认模式-producer到exchange 会返回一个confirmCallBack回调函数
- return 退回模式-exchange-queue投递失败会返回一个returnCallBack

指的是==消息生产端生产信息的传递可靠性==保障



2.代码流程



3.总结：

- 1.开启确认模式publisher-confirm=true
- 2.定义一个回调函数 ConfirmCallback,消息发送就回执行这个匿名内部类的confirm方法

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210206091547917.png" alt="image-20210206091547917" style="zoom:50%;" />



### return



1. 回退模式

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210206093349996.png" alt="image-20210206093349996" style="zoom:50%;" />



2. <img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210206093554258.png" alt="image-20210206093554258" style="zoom: 50%;" />



3. 设置处理消息的模式 `xx.setMandatory(true);`



### 小结

![image-20210206093708608](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210206093708608.png)



## consumer ack



1. 概念	

consumer ack 指的是消费端收消息的后的确认模式

 有三种确认模式

- 1.自动确认： acknowledge="none"---消息只要被consumer接收到，就自动确认，并将相应的message从rabbitmq从queue移除，而有些情况消费者收到消息但是业务处理出现了异常 消息可能丢失，
- 2.手动确认：acknowledge="manual"--当消费收到消息不会马上告诉broke 我收消息，等待消息后续业务处理逻辑没问题 我们就调用  `channel.basicAck()` 再手动调用确认签收， 如果出现了exception  就调用 `channel.basicNack()`  broke重发信息 consumer
- 3.根据异常的情况确认，acknowledge="auto"  比较麻烦，

2. 测试

相应在配置文件中指定确认模式 设置 `rabbit:listener-container` 设置确认模式属性

![image-20210206095048383](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210206095048383.png)





## 信息可靠性总结



1. 持久化
   1. exchange要持久化
   2. queue持久化
   3. message持久化
2. 生产者确认confirm
3. 消费者确认ack
4. broke 高可用





## 消费端限流

1. 





## TTL



## 死信队列

