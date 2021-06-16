[TOC]



# channel



## 介绍



<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210616012930040.png" alt="image-20210616012930040" style="zoom: 80%;" />

> channel是一个**双向**buffer传输数据的通道，通过channel可以向buffer读写数据，也可以向channel读写数据，
>
> channel可以同时进行读写，可以异步读写。
>
> channel只能和buffer交互。

​	channel在nio继承树中是一个接口。



**常见的channel的实现类**

* FileChannel：用于读取、写入、映射和操作文件的通道。
* DatagramChannel：通过 UDP 读写网络中的数据通道。
* SocketChannel：通过 TCP 读写网络中的数据。
* ServerSocketChannel：可以监听新进来的 TCP 连接，对每一个新进来的连接都会创建一个 SocketChannel。 【ServerSocketChanne 类似 ServerSocket , SocketChannel 类似 Socket】





## FileChannel

> 文件channel一般是通过调用支持channel的方法 的 getChannel() 方法来获取的。
>
> FileChannel只能工作在阻塞模式下。



> 如:
>
> * FileInputStream
> * FileOutputStream
> * RandomAccessFile
> * DatagramSocket
> * Socket
> * ServerSocket
>   获取通道的其他方式是使用 Files 类的静态方法 newByteChannel() 获取字节通道。或者通过通道的静态方法 open() 打开并返回指定通道
>
> 常见方法：
>
> ```java
> int read(ByteBuffer dst) 从 从  Channel 到 中读取数据到  ByteBuffer
> long  read(ByteBuffer[] dsts) 将 将  Channel 到 中的数据“分散”到  ByteBuffer[]
> int  write(ByteBuffer src) 将 将  ByteBuffer 到 中的数据写入到  Channel
> long write(ByteBuffer[] srcs) 将 将  ByteBuffer[] 到 中的数据“聚集”到  Channel
> long position() 返回此通道的文件位置
> FileChannel position(long p) 设置此通道的文件位置
> long size() 返回此通道的文件的当前大小
> FileChannel truncate(long s) 将此通道的文件截取为给定大小
> void force(boolean metaData) 强制将所有对此通道的文件更新写入到存储设备中
> ```



> 写文件

```java
 /**
     * 写文件
     * @throws Exception
     */
    @Test
    public void test1() throws Exception {
        FileOutputStream stream = new FileOutputStream("viw.txt");
        FileChannel channel = stream.getChannel();
        ByteBuffer buffer = ByteBuffer.allocate(1024);
        buffer.put("hello world".getBytes());//准备缓冲区 写缓冲区
        buffer.flip();
        channel.write(buffer);
        channel.close();
    }

```



> 读文件

```java
  /**
     * 读文件
     */
    @Test
    public void test2 () throws  Exception{
        FileChannel channel = new FileInputStream("viw.txt").getChannel();
        while (true) {
            ByteBuffer buffer = ByteBuffer.allocate(1024);
            var i = channel.read(buffer);
            buffer.flip();
            if (i==-1) break;
//            while (buffer.hasRemaining()){
//                log.debug("{}",(char)buffer.get());
//            }
            System.out.println(new String(buffer.array(),0, buffer.remaining()));
            buffer.clear();
        }
        channel.close();
    }
```



> 文件复制

![image-20210617013520465](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210617013520465.png)

```java

  /**
     * 复制文件
     */
    @Test
    public void test3() throws  Exception{
        var file = new File("E:\\workspace2\\gitee\\viw-simple-lab\\Netty\\nio\\viw_buffer\\src\\main\\resources\\application.properties");
        var toFile = new File("E:\\workspace2\\gitee\\viw-simple-lab\\Netty\\nio\\viw_buffer\\src\\main\\resources\\application111.properties");
        FileChannel fileChannel = new FileInputStream(file).getChannel();
        FileChannel toChannel = new FileOutputStream(toFile).getChannel();
        ByteBuffer buffer = ByteBuffer.allocate(10);
        while (true) {
            buffer.clear();
            var read = fileChannel.read(buffer);
            if(read==-1) break;
            buffer.flip();
            toChannel.write(buffer);
        }
        fileChannel.close();
        toChannel.close();
    }

```



> 分散和聚集
>
> 分散读取(scatter)：把channel通道的数据读入到多个缓冲区中去
>
> 聚集写入(gathering)：把多个buffer中的数据聚集到channel

```java
 /**
     * 
     * @throws Exception
     */
    @Test
    public void test4() throws Exception{
        //"rw" 开放阅读和写作。 如果该文件尚不存在，则会尝试创建它
        RandomAccessFile rw = new RandomAccessFile("viw.txt", "rw");
        FileChannel channel = rw.getChannel();
        ByteBuffer buffer1 = ByteBuffer.allocate(5);
        ByteBuffer buffer2 = ByteBuffer.allocate(10);
        ByteBuffer[] buffers = {buffer1,buffer2};
        channel.read(buffers);
        for(ByteBuffer byteBuffer : buffers){
            byteBuffer.flip();
        }
        System.out.println(new String(buffers[0].array(),0,buffers[0].limit()));
        System.out.println(new String(buffers[1].array(),0,buffers[1].limit()));
        //
        RandomAccessFile rw1 = new RandomAccessFile("viw2.txt", "rw");
        FileChannel channel1 = rw1.getChannel();
        channel1.write(buffers);
    }
// print out 
hello
 world
// viw2.txt
hello world
```

