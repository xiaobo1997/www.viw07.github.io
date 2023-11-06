[TOC]





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