[TOC]



# IO模型

1.阻塞IO  bio

![image-20210507201305104](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210507201305104.png)



2.非阻塞IO  nio

只有等待数据非阻塞，复制数据依然阻塞

![image-20210507203312762](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210507203312762.png)



3.IO多路复用

解决NIO的轮询问题，

![image-20210507204319802](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210507204319802.png)









==阻塞IO的问题==

![image-20210507204749958](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210507204749958.png)





# java-io演变



## bio

> 同步阻塞Io
>
> 一个线程处理一个请求，如果连接不做事情 线程就在浪费，

![img](https://pic3.zhimg.com/80/v2-73aad0f67337a85c287ed77c68d1b6de_720w.jpg)





## nio

> 同步非阻塞
>
> 一个线程处理多个请求，客户端的所有请求被接收并注册到选择器上(多路复用器)，然后去轮询是否有io操作，再去处理

![image-20210614020436425](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210614020436425.png)



## aio(nio-2.0)

> 异步非阻塞，在jdk1.7后出现，用于连接数多并连接时间比较长
>
> 通过future异步思想 去主线程等待时间
>
> 当数据到达内核态，操作系统通知用户线程，用户线程完成内核缓冲区和用户缓冲区数据的复制。



![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20210508102111.png)



## 场景

1.bio 小应用，没有什么并发，连接数小

2.nio 连接多但连接时间短，如 即时通讯，

3.aio  连接数多且长，让os来帮助并发

# nio



## 简单说明





下图片来自 `https://jishuin.proginn.com/p/763bfbd2a16d` 作者链接为https://jishuin.proginn.com/u/466023

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20210614014908.png" style="zoom: 50%;" />



下图来自 `https://www.cnblogs.com/xiangpeng/p/10233108.html` 作者为 [怡安](https://home.cnblogs.com/u/xiangpeng/)

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20210614015041.png" style="zoom:50%;" />





<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20210508015706.png" style="zoom: 33%;" />



> 找了下图片，有了图片还是好理解点，然后是nio一个概念,围绕的也是nio的三大组件，nio也是netty的基础
>
> 三大组件围绕 通道，缓冲区，选择器，有阻塞和非阻塞两种方式
>
> 大概过程就是 一个线程去维护选择器，客户端socket会注册到选择器中，然后选择器会去轮询 哪一个连接有io操作，如果有 就从线程池分配线程去处理，可以写入缓冲区，也可以从缓冲区读，也就是发送数据和接收数据通过buffer中转，通道也是双向的，一个线程就可以去监听多个请求的事件，nio是面向缓冲区的概念的。



​    java nio是非阻塞io，传统io在读写数据是阻塞的，当前线程不能做其他事情，nio 可以在等待数据的过程中去询问是否准备完毕，没有就可以去做其他事情。本质上在用户态和内核态复制数据是同步的。

   和传统bio比较起来，bio是面向流的，是阻塞的，而面向缓冲区的nio 非阻塞，是以块的形式



# 三大组件





## channel（通道）



   channel 是双向数据传输通道，和input 和output 不一样，channel可以把client数据读入buffer，也可以把sever数据写入buffer。channel支持非阻塞的写入和读取，支持写入和读取缓冲区。支持异步读写。



**常见的channel：**

- FileChannel  - 文件
- DatagramChannel -  udp
- SocketChannel - tcp  服务器客户端都可以用
- ServerSocketChannel - tcp  专服务器



![image-20210615011500213](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210615011500213.png)



## buffer



​    buffer 内存缓冲区，用户 和 内核 的之间缓冲区 channel的输入输出数据先放缓冲区。本质是一块可以写入读取的内存，被包装成nio buffer对象。



![image-20210614131924994](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210614131924994.png)



​			buffer是其他缓冲区的顶级父类，其他都是这个抽象类的子类，buffer在nio包下，

![image-20210614132310086](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210614132310086.png)





**常见的buffer子类:**

buffer相当于一个数组一样，根据类型的不同有不同的子类实现。这些子类的api差不多，只是管理数据类型不一样。

- ByteBuffer
  - MappedByteBuffer
  - DirectByteBuffer
  - HeapByteBuffer
- ShortBuffer
- IntBuffer
- LongBuffer
- FloatBuffer
- DoubleBuffer
- CharBuffer



> 
>
> 获取buffer的api基本的是
>
> ```java
> static xxBuffer allocate(int cap) ;//创建一个容量为cap的xxBuffer对象
> ```
>
> 如下图

![image-20210614132736620](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210614132736620.png)











## selector（选择器）

​	selector可以管理多个channel，可以轮询监听 io 事件 哪些channel已经准备好了数据来读写，然后就通知线程来操作。一个线程可以管理多个channel，也就是多个网络连接



大概模型图：

![image-20210614130441717](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210614130441717.png)

- 一个channel对应一个buffer，读写是双向
- 一个线程对应一个selector ，一个selector对应多个 channel
- 程序处理哪个channel是根据监听的事件来的
- seelctor 会根据不同的io事件
- buffer是一个内存块，由数组组成，数据的读写是通过buffer来完成的，既可以读也可以写
- channel传输数据，buffer存取数据







---

1.select的演变

一个thread 维持scoket连接

![image-20210508100709066](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210508100709066.png)

线程池

一个thread也只能同时处理一个scoket的连接操作

![image-20210508100811246](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210508100811246.png)



![image-20210508101529260](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210508101529260.png)











## 零拷贝



1.传统IO

![image-20210508215306823](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210508215306823.png)

用户态和内核态的转换发生了三次。第一次去读数据 从用户态到内核态(操作系统层面去读磁盘数据)，再从内核态转用户态，把内核缓冲区数据复制到用户缓冲区。然后把用户缓冲区数据写入socket缓冲区，因为java无法把数据写入网卡，所有需要切换到内核态去操作。

数据拷贝发生了四次

2.NIO

通过一块共享内存DirectBuffer，java可以去操作，操作系统也可以去操作，

![image-20210508221234440](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210508221234440.png)

数据拷贝少了一次

3.linux 2.1 后

![image-20210508221559816](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210508221559816.png)

只发送了一次用户态到内核态，linux中的sendFile  直接把内核缓冲区数据拷贝到socket缓冲区，对应的是java中的transferTo/transferFrom

4.linux 2.4



![image-20210508221842632](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210508221842632.png)

java中调用transferTo方法，使用DMA把数据从磁盘读入内核缓冲区，一次用户态切换内核态

只会把偏移量和长度等消息放入socket缓冲区，

使用DMA(负责硬件传输，解放CPU)把内核缓冲区数据直接写到网卡

只发生一次 用户和内核的切换，数据拷贝两次，适合小文件传输

数据拷贝只发送在操作系统内核层面上，而不是java层面上拷贝，jvm内存上，

5.netty零拷贝

