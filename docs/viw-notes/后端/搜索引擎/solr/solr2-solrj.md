[TOC]



# solr2



## solrj

### 添加数据

### 删除数据

### 添加自定义字段并添加数据

### 使用注解方式添加字段

1.entity添加 `@Field`

![image-20210116223320801](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210116223320801.png)

2.

![image-20210116223431821](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210116223431821.png)



### 查询功能

1.全查询

2.分页查询



## 数据同步和更新



### 全量



### 增量

1.表里面需要两个关键字段 一个是条目创建时间，一个是条目最后一次修改时间

  

## solrCloud

​	在高并发场景下可以使用solrCloud，就是说搜索量比较大，然后单机solr扛不住。solrCloud是基于solr和ZK的分布式搜索方案。ZK做为集群的配置中心。



需要4台solr机器，两组一主一从。

![image-20210118002907664](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210118002907664.png)

![image-20210118002937044](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210118002937044.png)



**逻辑结构：**

![image-20210118003049773](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210118003049773.png)



==四个配置文件是一致的。比如说IK词库，schema对等配置文件==



1.配置四台solr

2.配置文件相同

3.配置ZK集群，启动ZK

==实际场景==

![image-20210118013843874](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210118013843874.png)

4.启动solr

如果是单机版的  在solr集群目录下 ![image-20210118004946926](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210118004946926.png) 执行 `./solr-1/bin/solr start -p 8984 -force -c  -m 256m -z localhost:2181`

`-c` 表示集群，`-z` 表示ZK集群 后面是ZK地址 `-m` 是分配内存

如果是solr集群

 `./solr-1/bin/solr start -p 8984 -force -c -z localhost:2181,localhost:2182,localhost:2183`

 `./solr-2/bin/solr start -p 8985 -force -c -z localhost:2181,localhost:2182,localhost:2183`

。。。。。。

4台

 5.访问测试

![image-20210118005139657](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210118005139657.png)

6.创建collection

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210118010041770.png" alt="image-20210118010041770" style="zoom:50%;" />![image-20210118010047765](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210118010047765.png)



查看graph

![image-20210118010240136](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210118010240136.png)



7.把配置文件上传到ZK中的script-cloud

![image-20210118011504165](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210118011504165.png)



8.创建新的collection 使用上传的配置文件。



9.boot连接solr集群

```
1.创建 CloudSolrClient 对象
2.配置文件中配置ZK host
3.实例化 CloudSolrClient
```



10.查询时需要指定默认的collection





==总结，搭建solr集群，我们使用ZK做为solr集群入口，ZK使用奇数台，作为配置中心，solr进行分流，主挂了，还可以从继续使用，我们是两组，每一组  一主一从。 每一组我们也叫片==