

[TOC]



# 主从和哨兵



## 主从



## sentinel



1.哨兵可以解决集群架构中的单点redis故障问题

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210201012129823.png" alt="image-20210201012129823" style="zoom:67%;" />

哨兵直接互相连接，并连接自己的redis服务，如果mater挂，sentinel投票选举出master，

2.配置文件

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210201012442512.png" alt="image-20210201012442512" style="zoom:50%;" />

```shell

# 端口
port 26379

# 哨兵是否可以守护线程
daemonize no

# 指定master的名称 ip 和端口号 2个从节点
# 主节点中的sentinel配置 sentinel monitor mymaster 127.0.0.1 6379 2
sentinel monitor mymaster [master端口] 6379 2
# 哨兵监听时间  毫秒
sentinel down-after-milliseconds mymaster 30000
```



3.启动



## 3集群



![image-20210318003949274](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210318003949274.png)