[TOC]



# mysql集群-主从复制



## 背景



​	使用Mysql集群方案可以为我们在分布式服务架构中提供容错性和高性能的数据存储层。

​	mysql主从复制是构建高可用mysql的基础，而主从复制就是让一台主服务器的数据和其他从服务器上的数据进行同步，主和从有很多组成方式。





## 概念



## 结构





## 流程原理

![image-20210122005948800](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210122005948800.png)

![image-20210121015022385](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210121015022385.png)

两个日志 `relay binlog` 中继日志  `binlogs`  二进制日志

同步的根本原理：==二进制日志==

至少两个线程，一个IO线程来从主库中读取binary log写到 relay log,一个是sql线程 来把从库中的relay log读取出来执行输入到从库中

> 1.master把数据变动记录到二进制binlogs日志文件中，
>
> 2.slave通过io线程去读取master的binlogs到自己的relay log 中继日志中，
>
> 3.slave通过sql线程去读取reloy log日志文件执行到自己的库中

==mysql主从是异步的，串形化的，有延迟的==



## 主从复制问题分析





## 常见架构方案



1.master-slave

2.master-多slave

3.master-master

4.级联同步

​				 slave

​				  /

master-slave

​				\

​				slave

5.环型多主

​			两两双向连接

​				master

master						master





## 主从复制体验(1)-基于docker

linux mysql安装三种方式 tar.gz  yum   rpm

允许远程访问 数据库中执行

```sql
GRANT ALL PRIVILEGES ON *.*TO 'root'@'%' IDENTIFIED BY 'root'
WITH GRANT OPTION;
FLUSH PRIVILEGES;
```

一主多从体验

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210121021057576.png" alt="image-20210121021057576" style="zoom:50%;" />

版本：mysql 5.7 centos 7 docker 1.13 

1.创建msater节点



```shell
docker run --name mysql_master -v /etc/mysql_cluster/master/:/etc/mysql/conf.d/ -e MYSQL_ROOT_PASSWORD=root -p 3301:3306 -d mysql:5.7
```

master文件下的my.conf文件内容

```
log-bin=mysql-bin
server-id=1
binlog-do-db=viw
```

==查看bin-log是否开启==

`show variables like '%log_bin%';`

viw是我们要同步的数据库，相当于指定了我们要同步的数据库

2.创建slave1节点

```shell
docker run --name mysql_slave1 -v /etc/mysql_cluster/slaver1/:/etc/mysql/conf.d/ -e MYSQL_ROOT_PASSWORD=root -p 3302:3306 -d mysql:5.7
```

slaver1文件下创建 `my.cnf`

```
log-bin=mysql-bin
server-id=2
binlog-do-db=viw
```



3.创建slave2节点

```shell
docker run --name mysql_slave2 -v /etc/mysql_cluster/slaver2/:/etc/mysql/conf.d/ -e MYSQL_ROOT_PASSWORD=root -p 3303:3306 -d mysql:5.7
```



```
log-bin=mysql-bin
server-id=3
binlog-do-db=viw
```



4.设置主从连接

设置master信息

查看 slave status  `show slave status;`

进入slave  `mysql -uroot -p` 输入密码

```
change master to master_host = 'xxx.xxx.x.xxx',master_port=3301,
master_user='root',master_password='root';
```

==把slave和master连接。 master_host是ip地址==

启动slave

`start slave;`



把slave2也创建连接，同样是进去slave2

```
change master to master_host = 'xxx.xxx.x.xxx',master_port=3301,
master_user='root',master_password='root';
```



5.测试

在master创建库 和表和插入数据，修改数据，删除数据，看slave1 和slave2是否同步。



6.通过中间代理的方式让写请求到master  查请求到slave

https://github.com/Qihoo360/Atlas



使用的是 `atlas` 构建代理节点

> 主要功能：
>
> * 读写分离
> \* 从库负载均衡
> \* IP过滤
> \* SQL语句黑白名单
> \* 自动分表



```
# 安装
wget https://github.com/Qihoo360/Atlas/releases/download/2.2.1/Atlas-2.2.1.el6.x86_64.rpm

rpm -i Atlas-2.2.1.el6.x86_64.rpm

# 卸载
rpm -e Atlas-2.2.1.el6.x86_64
```



![image-20210122003217992](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210122003217992.png)

修改  

![image-20210122003341630](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210122003341630.png)



![image-20210122004046430](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210122004046430.png)

启动

在mysql_proxy目录下 执行

`./bin/mysql-proxyd test start`

![image-20210122005016412](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210122005016412.png)

连接即可   知道 `ip` +端口号 +你的用户名 `root` +密码 `root`， 

![image-20210122005303953](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210122005303953.png)



![image-20210122005457313](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210122005457313.png)

打开实时日志

重启

![image-20210122005536507](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210122005536507.png)

查看日志

`tail -f log/sql_test.log`

可以看到查询都是分发到slave中，slave的数据不会回流到master中

