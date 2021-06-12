[TOC]



# 1.参数和命令配置



## JVM参数



| `-Xss`               | 虚拟机栈大小。                                               |
| -------------------- | ------------------------------------------------------------ |
| `-Xms`               | 堆空间初始值。                                               |
| `-Xmx`               | 堆空间最大值。                                               |
| `-Xmn`               | 新生代空间大小。                                             |
| `-XX:NewSize`        | 新生代空间初始值。                                           |
| `-XX:MaxNewSize`     | 新生代空间最大值。                                           |
| `-XX:NewRatio`       | 新生代与年老代的比例。默认为 2，意味着老年代是新生代的 2 倍。 |
| `-XX:SurvivorRatio`  | 新生代中调整 eden 区与 survivor 区的比例，默认为 8。即 `eden` 区为 80% 的大小，两个 `survivor` 分别为 10% 的大小。 |
| `-XX:PermSize`       | 永久代空间的初始值。（JDK8 没有这个概念了）                  |
| `-XX:MaxPermSize`    | 永久代空间的最大值。（JDK8 没有这个概念）                    |
| `-XX:MetaspaceSize`  | 元空间大小                                                   |
| -XX:MaxMetaspaceSize | 最大元空间                                                   |





## JVM收集器的参数



```
开启选项：-XX:+UseSerialGC

打开此开关后，使用 Serial + Serial Old 收集器组合来进行内存回收。
```



> 开启选项：-XX:+UseParallelGC
>
> 打开此开关后，使用 Parallel Scavenge + Serial Old 收集器组合来进行内存回收。
>
> 开启选项：-XX:+UseParallelOldGC
>
> 打开此开关后，使用 Parallel Scavenge + Parallel Old 收集器组合来进行内存回收。



> Parallel Scavenge 收集器提供了两个参数用于精确控制吞吐量，分别是：
>
> -XX:MaxGCPauseMillis - 控制最大垃圾收集停顿时间，收集器将尽可能保证内存回收时间不超过设定值。
> -XX:GCTimeRatio - 直接设置吞吐量大小的（值为大于 0 且小于 100 的整数）。
>
> Parallel Scavenge 收集器还提供了一个参数 -XX:+UseAdaptiveSizePolicy，这是一个开关参数，打开参数后，就不需要手工指定年轻代的大小（-Xmn）、Eden 和 Survivor 区的比例（-XX:SurvivorRatio）、
> 晋升老年代对象年龄（-XX:PretenureSizeThreshold）等细节参数了，虚拟机会根据当前系统的运行情况收集性能监控信息，动态调整这些参数以提供最合适的停顿时间或者最大的吞吐量，这种方式称为 GC 自适应的调节策略（GC Ergonomics）。



> 开启选项：`-XX:+UseConcMarkSweepGC`
>
> 打开此开关后，使用 **CMS** + **ParNew** + **Serial Old** 收集器组合来进行内存回收。



> #### ParNew 收集器
>
> > 开启选项：`-XX:+UseParNewGC`



> 开启选项：`-XX:+UseG1GC`

![image-20201219153116663](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201219153116663.png)

![image-20201219153128163](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201219153128163.png)

![image-20201219153134906](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201219153134906.png)