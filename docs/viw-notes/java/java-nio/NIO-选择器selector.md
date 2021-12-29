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

> 处理read事件，处理读取件

```java
		var selector = Selector.open();
        var ssc = ServerSocketChannel.open();
        ssc.configureBlocking(false);
        ssc.bind(new InetSocketAddress(8080));
        ssc.register(selector, SelectionKey.OP_ACCEPT);
		while(selector.select()>0){
            var it =  selector.selectKeys().iterator();
            while(it.hasNext()){
                var sk = it.next();
                if(判断事件 如果是accept接收连接){
                    // 1.先获取socketChannel
                    // 2.channel.accept()
                    // 3.设置非阻塞
                    // 4.注册到selector上
                }else if(判断事件 如果是可读){
                    // 1.获取触发事件的channel  强转为SocketChannel
                    SocketChannel sc = (SocketChannel)sk.channel();
                    // 2.准备一个缓冲区 buffer ，准备读取这个sc的数据 到缓冲区中
                    // 3.channel.read();
                    // 4.buffer.flip(); // 读模式
                    // 5.出现断开连接需要去处理 cancel
                }
                //清除 事件
                it.remove();
            }
        }
	


```





  💡   :bulb: **事件处理完需要remove**：select 在事件发生后，就会将相关的 key 放入 selectedKeys 集合，但不会在处理完后从 selectedKeys 集合中移除，需要删除。比如channel第一次accept，不删除，下一次read事件 因为accept事件的存在 ,而已经处理过了accept 就会有空指针异常。 **accept在非阻塞下没有连接返回的是null**

:bulb:  处理客户端断开，需要去处理：

```java
//1.try catch  然后 cancel ,cancel是真正的从集合中删除，后续就不监听channel事件
//2.区分正常断开 和异常断开， 可以根据 channel.read的返回结果  正常断开返回的是-1
```



<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/0023.png" style="zoom:67%;" />

:bulb: 处理消息边界：不处理就会导致 因为buffer的容量而接收的message 乱码。如上图 

- 1.固定消息长度。2.按分隔符拆分，效率低，每次都需要去寻找分隔符。3. TLV (http1.1的格式) 类型+长度+数据 或者是  LTV 格式（http2.0）

```java
/**
按照分隔符 都是没有处理超过容量的问题  比如 客户端发送 20个字节 然后 再接分隔符
而buffer只有16，下一次再去读取 又是新的buffer了，不会去 把这两次buffer读到的数据拼接起来

*/
private static void split(ByteBuffer buffer) {
        buffer.flip();
        for (int i = 0; i < buffer.limit(); i++) {
            if (buffer.get(i) == '\n') {
                ByteBufferUtil.debugAll(buffer);
                var len = i + 1 - buffer.position();
                var to = ByteBuffer.allocate(len);
                for (int j = 0; j < len; j++) {
                    to.put(buffer.get());
                }
                System.out.println("<===============================================>");
                ByteBufferUtil.debugAll(to);
            }
        }
        buffer.compact();// 如果这个buffer 没有 分隔符，说明 数据超过了这个buffer，此时 position = limit
    }






	var selector = Selector.open();
        var ssc = ServerSocketChannel.open();
        ssc.configureBlocking(false);
        ssc.bind(new InetSocketAddress(8080));
        ssc.register(selector, SelectionKey.OP_ACCEPT);
		while(selector.select()>0){
            var it =  selector.selectKeys().iterator();
            while(it.hasNext()){
                var sk = it.next();
                if(判断事件 如果是accept接收连接){
                	 ServerSocketChannel channel = (ServerSocketChannel) sk.channel();
                    SockerChannel sc = channel.accept();
                    ByteBuffer buffer = ByteBuffer.allocate(16);
                    // 将buffer 做为附件绑定关联到 这个key上  而这个key也有channel的相关信息
                    SelectionKey key = sc.register(selector,0,buffer);
                    // 给key添加一个 可读事件
                    key.interestOpt(Selectionkey.OP_READ);
                }else if(判断事件 如果是可读){
                  	try{
                        SocketChannel channel = (SocketChannel)sk.channel();//获取触发事件的channel
                        ByteBuffer buffer = (ByteBuffer)key.attachment();
                        int read = channel.read(buffer);
                        if(read == -1){
                            sk.cancel();// 如果是正常断开 返回的就是-1
                        }else {
                            split(buffer);
                            if(buffer.position() == buffer.limit() ){
                                // 扩容
                                ByteBuffer newBuffer = BytrBuffer.allocate(buffer.capacity()*2);
                                // 切换读模式
                                buffer.flip();
                                // 原buffer put到新扩容后的buffer
                                newBuffer.put(buffer);
                                // 扩容后的buffer 替换掉原key上的附件buffer，新的关联buffer
                                key.attach(newBUffer);
                            }
                        }
                        
                    }catch(Exception e){
                        e.printStackTrace();
                        sk.cancel();
                    }
                }
                //清除 事件
                it.remove();
            }
        }

```



:bulb:   ByteBuffer 大小分配

* 每个 channel 都需要记录可能被切分的消息，因为 **ByteBuffer不能被多个channel 共同使用**，bytebuffer不是线程安全的，因此需要为每个 channel 维护一个独立的 ByteBuffer
* **ByteBuffer 不能太大**，比如一个 ByteBuffer 1Mb 的话，要支持百万连接就要 1Tb 内存，因此需要设计大小可变的 ByteBuffer  
  * 一种思路是首先分配一个较小的 buffer，例如 4k，如果发现数据不够，再分配 8k 的 buffer，将 4k buffer 内容拷贝至 8k buffer，优点是消息连续容易处理，缺点是数据拷贝耗费性能，参考实现 [http://tutorials.jenkov.com/java-performance/resizable-array.html](http://tutorials.jenkov.com/java-performance/resizable-array.html)
  * 另一种思路是用多个数组组成 buffer，一个数组不够，把多出来的内容写入新的数组，与前面的区别是消息存储不连续解析复杂，优点是避免了拷贝引起的性能损耗 。netty就有这种思想

 



> 处理write事件



:bulb:  处理写入内容过多。不要一次性把内容全部发送给客户端。

- 非阻塞模式下，无法保证把 buffer 中所有数据都写入 channel，因此需要追踪 write 方法的返回值（代表实际写入字节数）
- 用 selector 监听所有 channel 的可写事件，每个 channel 都需要一个 key 来跟踪 buffer，但这样又会导致占用内存过多，就有两阶段策略
  - 当消息处理器第一次写入消息时，才将 channel 注册到 selector 上
  - selector 检查 channel 上的可写事件，如果所有的数据写完了，就取消 channel 的注册。如果不取消，会每次可写均会触发 write 事件

```java
 ServerSocketChannel ssc = ServerSocketChannel.open();
        ssc.configureBlocking(false);
        ssc.bind(new InetSocketAddress(8080));

        Selector selector = Selector.open();
        ssc.register(selector, SelectionKey.OP_ACCEPT);

        while(true) {
            selector.select();

            Iterator<SelectionKey> iter = selector.selectedKeys().iterator();
            while (iter.hasNext()) {
                SelectionKey key = iter.next();
                iter.remove();
                if (key.isAcceptable()) {
                    SocketChannel sc = ssc.accept();
                    sc.configureBlocking(false);
                    SelectionKey sckey = sc.register(selector, SelectionKey.OP_READ);
                    // 1. 向客户端发送内容
                    StringBuilder sb = new StringBuilder();
                    for (int i = 0; i < 3000000; i++) {
                        sb.append("a");
                    }
                    ByteBuffer buffer = Charset.defaultCharset().encode(sb.toString());
                    int write = sc.write(buffer);
                    // 3. write 表示实际写了多少字节
                    System.out.println("实际写入字节:" + write);
                    // 4. 如果有剩余未读字节，才需要关注写事件
                    if (buffer.hasRemaining()) {
                        // read 1  write 4
                        // 在原有关注事件的基础上，多关注 写事件
                        sckey.interestOps(sckey.interestOps() + SelectionKey.OP_WRITE);
                        // 把 buffer 作为附件加入 sckey
                        sckey.attach(buffer);
                    }
                } else if (key.isWritable()) {  // 如果可写
                    ByteBuffer buffer = (ByteBuffer) key.attachment();
                    SocketChannel sc = (SocketChannel) key.channel();
                    int write = sc.write(buffer);
                    System.out.println("实际写入字节:" + write);
                    if (!buffer.hasRemaining()) { // 写完了
                        key.interestOps(key.interestOps() - SelectionKey.OP_WRITE);
                        key.attach(null);
                    }
                }
            }
        }
    }


```







