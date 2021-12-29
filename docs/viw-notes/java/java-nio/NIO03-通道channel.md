[TOC]





# 介绍



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





# FileChannel的介绍

> 文件channel一般是通过调用支持channel的方法 的 getChannel() 方法来获取的。
>
> FileChannel只能工作在**阻塞模式**下。也就是和配合selector是阻塞
>
> 和socketChannel配合才可以在非阻塞模式下
>
> FileChannel是不能直接获取。



> 如:
>
> * FileInputStream---  **获取的channel只能读**
> * FileOutputStream--  **获取的channel只能写**
> * RandomAccessFile--   **根据参数来决定 读写模式**
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



## 读写和常见方法



> 写文件
>
> 1. 存buffer
>
> 2. flip切换读模式
>
> 3. 循环去写，buffer有限，一般在while中 去  调用 channel.write(buffer); 如
>
>    ```java
>    while(buffer.hasRemaining()){
>    	channel.write(buffer);
>    }
>    ```
>
>    

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
>
> `int readBytes = channel.read(buffer)` 返回值表示读到了多少字节， -1 表示文件的末尾

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



> 关闭
>
> **channel需要关闭**

```
channel.close();
```



> 位置和大小

```java
// 获取当前的位置
var pos = channel.position();
// 设置当前位置

var newPosition = xxx;
channel.position(newPosition);
/**
设置当前位置时，如果设置为文件的末尾
这时读取会返回 -1 
这时写入，会追加内容，但要注意如果 position 超过了文件末尾，再写入时在新内容和原末尾之间会有空洞（00）
*/
// 大小
channel.size();
```



> 强制写入
>
> 操作系统出于性能的考虑，会将数据缓存，不是立刻写入磁盘。可以调用 force(true)  方法将文件内容和元数据（文件的权限等信息）立刻写入磁盘

```java
/**
强制将此通道文件的任何更新写入包含它的存储设备。
*/
public abstract void force(boolean metaData) throws IOException;
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
        FileChannel channel = rw.getChannel();//获取文件channel
        ByteBuffer buffer1 = ByteBuffer.allocate(5);// 实例化一个容量为5的 buffer
        ByteBuffer buffer2 = ByteBuffer.allocate(10);//
        ByteBuffer[] buffers = {buffer1,buffer2};//组成一个buffer 数组
        channel.read(buffers);
        for(ByteBuffer byteBuffer : buffers){
            byteBuffer.flip();//读模式
        }
        System.out.println(new String(buffers[0].array(),0,buffers[0].limit()));
        System.out.println(new String(buffers[1].array(),0,buffers[1].limit()));
        //
        RandomAccessFile rw1 = new RandomAccessFile("viw2.txt", "rw");
        FileChannel channel1 = rw1.getChannel();// 获取rw1的文件channel
        channel1.write(buffers);//写入channel
    }
// print out 
hello
 world
// viw2.txt
hello world
```





>  transferFrom(ReadableByteChannel src,long position,long count)
>
>  从src复制count字节到当前通道，
>
>  点击跳转[零拷贝](https://xiaobo1997.github.io/#/./viw-notes/Java/java-NIO/NIO1-IO%E6%A8%A1%E5%9E%8B%E7%BB%93%E6%9E%84%E5%92%8C%E5%9F%BA%E7%A1%80?id=%e9%9b%b6%e6%8b%b7%e8%b4%9d)

```java

 public void test5() throws Exception {
        RandomAccessFile rw = new RandomAccessFile("viw.txt", "rw");
        FileChannel resource = rw.getChannel();
        RandomAccessFile rw1 = new RandomAccessFile("viw2.txt", "rw");
        FileChannel toResource = rw1.getChannel();
        //从源通道读取并写入该通道的简单循环更有效。少了复制(对比零拷贝的用户态和内核态切换,)
//        toResource.transferFrom(resource,resource.position(),resource.size());
        //从该通道读取并写入目标通道的简单循环更有效
        resource.transferTo(toResource.position(),toResource.size(),toResource);
        resource.close();
        toResource.close();
    }

```



>  transferTo(long position,long count,WritableByteChannel target)
>
>  从当前通道中把数据写入到target通道中
>
>  如上方法。效率是比较高的。参考linux 底层零拷贝。
>
>  传输数据有上限  **2G**

 

```java

public class FileChannel01 {

    public static void main(String[] args) {
        try (
                FileChannel from = new FileInputStream("bigdata.txt").getChannel();
                FileChannel to = new FileOutputStream("to.txt").getChannel();
        ) {
            // transferTo效率高，底层会利用操作系统的零拷贝进行优化
            long size = from.size();
            // left 变量代表还剩余多少字节 循环写
            for (long left = size; left > 0; ) {
                System.out.println("position:" + (size - left) + " left:" + left);
                left -= from.transferTo((size - left), left, to);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```



## Path



> jdk7中引入的 path(表示文件路径)  和paths(工具类，获取path实例)
>
> 

```java
Path source = Paths.get("1.txt"); // 相对路径 使用 user.dir 环境变量来定位 1.txt

Path source = Paths.get("d:\\1.txt"); // 绝对路径 代表了  d:\1.txt

Path source = Paths.get("d:/1.txt"); // 绝对路径 同样代表了  d:\1.txt

Path projects = Paths.get("d:\\data", "projects"); // 代表了  d:\data\projects
```



>  `.` 和 `..` 
>
> * `.` 代表了当前路径
> * `..` 代表了上一级路径

如目录结构如下

```
d:
	|- data
		|- projects
			|- a
			|- b
```

代码

```java
Path path = Paths.get("d:\\data\\projects\\a\\..\\b");
System.out.println(path);
System.out.println(path.normalize()); // 正常化路径
```

会输出

```
d:\data\projects\a\..\b
d:\data\projects\b
```



## Files

> jdk7出的文件

```java
//检查文件是否存在
var v = Paths.get("xxx/data.txt");
Files.exists(v);
//创建一级目录
var p  =  Paths.get("bb");// 不能一次性创建aaa 和bb 两个目录.NoSuchFileException: aaa\bb ，目录已存在抛 FileAlreadyExistsException
Files.createDirectory(p);//

//创建多级目录用
Path path = Paths.get("helloword/d1/d2");
Files.createDirectories(path);

// 拷贝文件
Path source = Paths.get("helloword/data.txt");
Path target = Paths.get("helloword/target.txt");
Files.copy(source, target);
//如果文件已存在，会抛异常 FileAlreadyExistsException
//如果希望用 source 覆盖掉 target，需要用 StandardCopyOption 来控制
Files.copy(source, target, StandardCopyOption.REPLACE_EXISTING);

//移动文件
Path source = Paths.get("helloword/data.txt");
Path target = Paths.get("helloword/data.txt");
Files.move(source, target, StandardCopyOption.ATOMIC_MOVE);//StandardCopyOption.ATOMIC_MOVE 保证文件移动的原子性


//删除文件  不存在抛异常
Path target = Paths.get("helloword/target.txt");
Files.delete(target);


//删除目录 不存在抛异常
Path target = Paths.get("helloword/d1");
Files.delete(target);


//遍历目录文件
	Path path = Paths.get("C:\\Program Files\\Java\\jdk1.8.0_91");
    AtomicInteger dirCount = new AtomicInteger();
    AtomicInteger fileCount = new AtomicInteger();
    Files.walkFileTree(path, new SimpleFileVisitor<Path>(){
        @Override
        public FileVisitResult preVisitDirectory(Path dir, BasicFileAttributes attrs) 
            throws IOException {
            System.out.println(dir);
            dirCount.incrementAndGet();
            return super.preVisitDirectory(dir, attrs);
        }

        @Override
        public FileVisitResult visitFile(Path file, BasicFileAttributes attrs) 
            throws IOException {
            System.out.println(file);
            fileCount.incrementAndGet();
            return super.visitFile(file, attrs);
        }
    });
    System.out.println(dirCount); // 133
    System.out.println(fileCount); // 1479



// 统计 jar 的数目
Path path = Paths.get("C:\\Program Files\\Java\\jdk1.8.0_91");
AtomicInteger fileCount = new AtomicInteger();
Files.walkFileTree(path, new SimpleFileVisitor<Path>(){
    @Override
    public FileVisitResult visitFile(Path file, BasicFileAttributes attrs) 
        throws IOException {
        if (file.toFile().getName().endsWith(".jar")) {
            fileCount.incrementAndGet();
        }
        return super.visitFile(file, attrs);
    }
});
System.out.println(fileCount); // 724


//删除多级目录
Path path = Paths.get("d:\\a");
Files.walkFileTree(path, new SimpleFileVisitor<Path>(){
    @Override
    public FileVisitResult visitFile(Path file, BasicFileAttributes attrs) 
        throws IOException {
        Files.delete(file);
        return super.visitFile(file, attrs);
    }

    @Override
    public FileVisitResult postVisitDirectory(Path dir, IOException exc) 
        throws IOException {
        Files.delete(dir);
        return super.postVisitDirectory(dir, exc);
    }
});

//拷贝多级目录
long start = System.currentTimeMillis();
String source = "D:\\Snipaste-1.16.2-x64";
String target = "D:\\Snipaste-1.16.2-x64aaa";

Files.walk(Paths.get(source)).forEach(path -> {
    try {
        String targetName = path.toString().replace(source, target);
        // 是目录
        if (Files.isDirectory(path)) {
            Files.createDirectory(Paths.get(targetName));
        }
        // 是普通文件
        else if (Files.isRegularFile(path)) {
            Files.copy(path, Paths.get(targetName));
        }
    } catch (IOException e) {
        e.printStackTrace();
    }
});




```

