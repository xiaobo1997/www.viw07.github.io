# buffer



## 基本概念



- **容量(capacity)**：buffer内存块的大小，Buffer具有一定的固定大小，也称为"容量"，缓冲区容量不能为负，并且创建后不能更改。
- **限制limit**:限制可以操作数据的大小，limit后的数据不能读写，不能超过容量。**写入模式，限制等于buffer的容量。读取模式下，limit等于写入的数据量**。
- **位置position:** 下一个要读取或写入的数组的索引。缓冲区的位置不能为负





![image-20200619172434538](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200619172434538.png)



