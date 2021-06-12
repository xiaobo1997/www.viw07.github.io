[TOC]





# 1.工具使用

>使用工具查看java进程中的线程堆栈的信息



| `jps`    | JVM 进程状态工具。显示系统内的所有 JVM 进程。参考虚拟机进程状态 |
| -------- | ------------------------------------------------------------ |
| `jstat`  | JVM 统计监控工具。监控虚拟机运行时状态信息，它可以显示出 JVM 进程中的类装载、内存、GC、JIT 编译等运行数据。 |
| `jmap`   | JVM 堆内存分析工具。用于打印 JVM 进程对象直方图、类加载统计。并且可以生成堆转储快照（一般称为 heapdump 或 dump 文件）。 |
| `jstack` | JVM 栈查看工具。用于打印 JVM 进程的线程和锁的情况。并且可以生成线程快照（一般称为 threaddump 或 javacore 文件）。 |
| `jhat`   | 用来分析 jmap 生成的 dump 文件。                             |
| `jinfo`  | JVM 信息查看工具。用于实时查看和调整 JVM 进程参数。          |
| `jcmd`   | JVM 命令行调试 工具。用于向 JVM 进程发送调试命令。           |



## jps



### 用法和参数

```
jps [参数1] [默认当前主机名]
jps [-help]
```



### 例子

> 列出  本地java进程
>
> ```dart
> $ jps
> 18027 Java2Demo.JAR
> 18032 jps
> 18005 jstat
> ```



> 列出本地 Java 进程 ID，并输出主类的全名
>
> ```
> jps -l [主机名]
> 1111  /xxx/xxx/xxx
> ```
>
> 





## jinfo

jinfo 用于实时查看和调整虚拟机运行参数



### 参数用法

```
jinfo [参数1] pid

查看2788的MaxPerm大小可以用
xxxx   #jinfo -flag MaxPermSize 2788
-XX:MaxPermSize=134217728
```



## jstat

jstat 用于监视虚拟机运行时状态信息，它可以显示出虚拟机进程中的类装载、内存、垃圾收集、JIT 编译等运行数据。



### 用法

```
jstat [参数1 指定需要查询的虚拟机信息] VMID [参数2 查询间隔] [参数3 查询次数]
```



option 

\- 选项参数，用于指定用户需要查询的虚拟机信息

- `-class` - 监视类装载、卸载数量、总空间以及类装载所耗费的时间
- `-compiler`：显示 JIT 编译的相关信息；
- `-gc`：监视 Java 堆状况，包括 Eden 区、两个 survivor 区、老年代、永久代等区的容量、已用空间、GC 时间合计等信息。
- `-gccapacity`：显示各个代的容量以及使用情况；
- `-gcmetacapacity`：显示 Metaspace 的大小；
- `-gcnew`：显示新生代信息；
- `-gcnewcapacity`：显示新生代大小和使用情况；
- `-gcold`：显示老年代和永久代的信息；
- `-gcoldcapacity`：显示老年代的大小；
- `-gcutil`：显示垃圾回收统计信息；
- `-gccause`：显示垃圾回收的相关信息（通 -gcutil），同时显示最后一次或当前正在发生的垃圾回收的诱因；
- `-printcompilation`：输出 JIT 编译的方法信息。

- `VMID` - 如果是本地虚拟机进程，则 VMID 与 LVMID 是一致的；如果是远程虚拟机进程，那 VMID 的格式应当是：`[protocol:][//]lvmid[@hostname[:port]/servername]`
- `interval` - 查询间隔
- `count` - 查询次数

### 例

使用 `jstat -class pid` 命令查看编译统计信息

```
jstat  -class xxx
Loaded  Bytes  Unloaded  Bytes     Time
 26749 50405.3      873  1216.8      19.75
```

- Loaded - 加载 class 的数量
- Bytes - 所占用空间大小
- Unloaded - 未加载数量
- Bytes - 未加载占用空间
- Time - 时间

使用 `jstat  -compiler pid` 查看编译统计信息

```
jstat  -compiler  xxx
Compiled Failed Invalid   Time   FailedType FailedMethod
   42030      2       0   302.53          1 org/apache/felix/framework/BundleWiringImpl$BundleClassLoader findClass
```

- Compiled - 编译数量
- Failed - 失败数量
- Invalid - 不可用数量
- Time - 时间
- FailedType - 失败类型
- FailedMethod - 失败的方法



使用  `jstat -gc pid time`查看 GC统计信息

以 250 毫秒的间隔进行 7 个采样，并显示-gcutil 选项指定的输出。

```
$ jstat -gcutil 21891 250 7
  S0     S1     E      O      M     CCS    YGC     YGCT    FGC    FGCT     GCT
  0.00  97.02  70.31  66.80  95.52  89.14      7    0.300     0    0.000    0.300
  0.00  97.02  86.23  66.80  95.52  89.14      7    0.300     0    0.000    0.300
  0.00  97.02  96.53  66.80  95.52  89.14      7    0.300     0    0.000    0.300
 91.03   0.00   1.98  68.19  95.89  91.24      8    0.378     0    0.000    0.378
 91.03   0.00  15.82  68.19  95.89  91.24      8    0.378     0    0.000    0.378
 91.03   0.00  17.80  68.19  95.89  91.24      8    0.378     0    0.000    0.378
 91.03   0.00  17.80  68.19  95.89  91.24      8    0.378     0    0.000    0.378
```

- `S0C`：年轻代中 To Survivor 的容量（单位 KB）；
- `S1C`：年轻代中 From Survivor 的容量（单位 KB）；
- `S0U`：年轻代中 To Survivor 目前已使用空间（单位 KB）；
- `S1U`：年轻代中 From Survivor 目前已使用空间（单位 KB）；
- `EC`：年轻代中 Eden 的容量（单位 KB）；
- `EU`：年轻代中 Eden 目前已使用空间（单位 KB）；
- `OC`：Old 代的容量（单位 KB）；
- `OU`：Old 代目前已使用空间（单位 KB）；
- `MC`：Metaspace 的容量（单位 KB）；
- `MU`：Metaspace 目前已使用空间（单位 KB）；
- `YGC`：从应用程序启动到采样时年轻代中 gc 次数；
- `YGCT`：从应用程序启动到采样时年轻代中 gc 所用时间 (s)；
- `FGC`：从应用程序启动到采样时 old 代（全 gc）gc 次数；
- `FGCT`：从应用程序启动到采样时 old 代（全 gc）gc 所用时间 (s)；
- `GCT`：从应用程序启动到采样时 gc 用的总时间 (s)。









## jmap

jmap 用于生成堆转储快照（一般称为 heapdump 或 dump 文件）。jmap 不仅能生成 dump 文件，还可以查询 `finalize` 执行队列、Java 堆和永久代的详细信息，如当前使用率、当前使用的是哪种收集器等。

也可以让系统OOM自动生成dump文件，



### 用法：

```
jmap [参数1] pid
```

参数1选项：

-  `-dump` 生成 堆快照
- `-finalizerinfo` 显示在F-Queue等待队列中等待执行 finalizer 对象
-  `-heap`  显示 java堆的详细信息
- `-histo` 显示堆中对象的统计信息
- `-F` - 当-dump 没有响应时，强制生成 dump 快照



## jhat

`jhat`（`JVM Heap Analysis Tool`）命令通常与 `jmap` 搭配使用，用来分析 `jmap` 生成的 `dump`。`jhat` 内置了一个微型的 `HTTP/HTML` **服务器**，生成 `dump` 的分析结果后，可以在浏览器中查看。

例：

```
$ jhat -port 7000 dump.dat
Reading from dump.dat...
Dump file created Sun Aug 12 12:15:02 CST 2018
Snapshot read, resolving...
Resolving 1788693 objects...
Chasing references, expect 357 dots.....................................................................................................................................................................................................................................................................................................................................................................
Eliminating duplicate references.....................................................................................................................................................................................................................................................................................................................................................................
Snapshot resolved.
Started HTTP server on port 7000
Server is ready.

```



![image-20201219211712805](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201219211712805.png)

![image-20201219211728317](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201219211728317.png)





## jstack



jstack 用来打印目标 Java 进程中各个线程的栈轨迹，以及这些线程所持有的锁，并可以生成 java 虚拟机当前时刻的线程快照（一般称为 threaddump 或 javacore 文件）。



用法：

```
jstack [option] pid
```



`jstack` 通常会结合 `top -Hp pid` 或 `pidstat -p pid -t` 一起查看具体线程的状态，也经常用来排查一些死锁的异常。



### thread dump

thread  dump文件主要是五个部分

- 1.Full thread dump identifier：展示了快照文件的生成时间，JVM版本信息
- 2.Java EE middleware, third party & custom application Threads:展示了整个文件的核心部分，展示javaEE容器，自己的程序的线程信息
- 3.HotSpot VM Thread：展示了JVM内部线程的信息，

## 系统线程状态

在dump文件中，我们需要关注的线程状态为：

- 死锁
- 执行中
- 等待资源
- 等待获取监听器
- 暂停
- 阻赛
- 通知



如两个线程发生死锁，可以先  用 jps 查看jvm进程pid， 然后 jstack 查看 堆栈信息，