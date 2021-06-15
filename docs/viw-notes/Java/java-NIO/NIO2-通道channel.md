[TOC]



# channel



## 介绍



<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210616012930040.png" alt="image-20210616012930040" style="zoom: 80%;" />

​	channel是一个**双向**buffer传输数据的通道，通过channel可以向buffer读写数据，也可以向channel读写数据，channel可以同时进行读写，可以异步读写，channel只能和buffer交互。

