[TOC]





# selector选择器介绍



## 入门概念

> selector在nio模型中的位置 是nio的 核心，一个selector监听多个channel事件。

![image-20210614130441717](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210614130441717.png)



* Java 的 NIO，用非阻塞的 IO 方式。可以用一个线程，处理多个的客户端连接，就会使用到 Selector(选择器)
* Selector 能够检测多个注册的通道上是否有事件发生(注意:多个 Channel 以事件的方式可以注册到同一个
  Selector)，如果有事件发生，便获取事件然后针对每个事件进行相应的处理。这样就可以只用一个单线程去管
  理多个通道，也就是管理多个连接和请求。
* 只有在 连接/通道 真正有读写事件发生时，才会进行读写，减少了系统开销，并且不必为每个连接都
  创建一个线程，不用去维护多个线程
* 避免了多线程之间的上下文切换导致的开销





> 使用

```java
//新打开一个选择器
// Selector se  =  Selector.open;
public static Selector open() throws IOException {
        return SelectorProvider.provider().openSelector();
    }
```

> 流程
>
> 可以绑定的事件
>
> * 读 : SelectionKey.OP_READ （1） **数据可读入时触发.有因为接收能力弱,数据暂不能读入的情况**
> * 写 : SelectionKey.OP_WRITE （4） **数据可写出时触发,有因为发送能力弱,数据暂不能写出的情况**
> * 连接 : SelectionKey.OP_CONNECT （8）**客户端连接成功时触发**
> * 接收 : SelectionKey.OP_ACCEPT （16） **服务器端成功接受连接时触发**
>
> 多个事件可以组合如：  `int i =SelectionKey.OP_READ|SelectionKey.OP_WRITE  `

```java
/**
*/


//1. 获取服务器scoket通道
ServerSocketChannel ssChannel = ServerSocketChannel.open();
//2. 切换非阻塞模式
ssChannel.configureBlocking(false);
//3. 绑定一个socket连接
ssChannel.bind(new InetSocketAddress(8080));
//4. 新打开一个选择器
Selector selector = Selector.open();
//5. 将通道注册到选择器上, 并且指定“监听接收事件”
ssChannel.register(selector, SelectionKey.OP_ACCEPT);
```



## 流程



> 创建和绑定

```java
/**
创建
*/
Selector selector = Selector.open();

/**
channel需要工作在非阻塞模式下，而FileChannel没有非阻塞模式 不能和Selector一起使用
*/
channel.configureBlocking(false);
SelectionKey key = channel.register(selector, 绑定事件);

/**
监听
*/
// 1.阻塞，直到绑定的事件发生
int count = selector.select();
// 2.同上，不过有超时时间
int count = selector.select(long timeout);
// 3. 不阻塞，不管有没有事件，立刻返回，自己根据返回值检查是否有事件
int count = selector.selectnow();
```



select不阻塞的时机:

- 事件发生时：
  - 客户端发起请求，触发accept事件
  - 客户端发送数据，触发read，或者 发送的数据超过buffer容量，就会触发read事件
  - channel可写，触发Write
  - linux  nio下的 发送bug
- 调用selector.wakeup();
- 调用selector.close();
- selector所在线程中断、



> 处理accept事件

```java
		// 5步流程
 		var selector = Selector.open();
        var ssc = ServerSocketChannel.open();
        ssc.configureBlocking(false);
        ssc.bind(new InetSocketAddress(8080));
        ssc.register(selector, SelectionKey.OP_ACCEPT);
		while(1.如果迭代器的元素>0){ // selector.select()>0 没有事件发送，阻塞，有事件线程才运行
            2.获取key的迭代器
            while(3.判断迭代器是否有事件){
                4.获取事件key，里面包含了channel的信息等
                if(5.判断事件，做不同的判断){
                   // do something
                }eles {
                    // do something
                }
                6.删除事件 或者cannel()
            }
        }




```

**事件发送后，要么处理，要么取消`cancel`，不能什么都不做，否则下一次这个事件仍然会触发，因为认为你没有触发过，nio==底层水平触发==**

> 处理read事件





> 处理write事件







