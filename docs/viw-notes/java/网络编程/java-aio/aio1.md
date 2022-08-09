

# aio



> 简单介绍
>
> **AIO**提供了从建立连接到读、写的全异步操作。可用于异步的**文件读写**和**网络通信**。
>
> 一般网络通信不会直接使用nio  aio 而是 netty这样的网络通信框架

Java AIO(NIO.2) ： 异步非阻塞，服务器实现模式为一个有效请求一个线程，客户端的I/O请求都是由OS先完成了再通知服务器应用去启动线程进行处理。



```java
AIO异步非阻塞，基于NIO的，可以称之为NIO2.0
    BIO                   NIO                              AIO        
Socket                SocketChannel                    AsynchronousSocketChannel
ServerSocket          ServerSocketChannel	       AsynchronousServerSocketChannel
```

与NIO不同，当进行读写操作时，只须直接调用API的read或write方法即可, 这两种方法均为异步的，对于读操作而言，当有流可读取时，操作系统会将可读的流传入read方法的缓冲区,对于写操作而言，当操作系统将write方法传递的流写入完毕时，操作系统主动通知应用程序

即可以理解为，read/write方法都是异步的，完成后会主动调用回调函数。在JDK1.7中，这部分内容被称作NIO.2，主要在Java.nio.channels包下增加了下面四个异步通道：

```java
	AsynchronousSocketChannel
	AsynchronousServerSocketChannel
	AsynchronousFileChannel
	AsynchronousDatagramChannel
```



