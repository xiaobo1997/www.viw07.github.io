

# buffer基本概念



- **容量(capacity)**：buffer内存块的大小，Buffer具有一定的固定大小，也称为"容量"，缓冲区容量不能为负，并且创建后不能更改。
- **限制limit**:限制可以操作数据的大小，limit后的数据不能读写，不能超过容量。**写入模式，限制等于buffer的容量。读取模式下，limit等于写入的数据量**。
- **位置position:** 下一个要读取或写入的数组的索引。缓冲区的位置不能为负.小于容量。
- **标记 (mark)与重置 (reset)**：标记是一个索引，通过 Buffer 中的 mark() 方法 指定 Buffer 中一个特定的 position(到了指定位置标记)，之后可以通过调用 reset() 方法恢复到这 个 position.(回到之前标记的地方)
- **标记、位置、限制、容量遵守以下不变式： 0 <= mark <= position <= limit <= capacity**





![image-20200619172434538](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200619172434538.png)



# 使用





> 一般步骤
>
> **1.向buffer写数据 。 channel.read(buffer)**
>
> **2.调用flip()方法，转换读模式。**
>
> **3.从buffer中读取模式。如调用buffer.get();**
>
> **4.调用buffer.clear() 或者 biffer.compact()方法清除缓冲区。切换写模式。**



> 简单使用

```java

/**
 * @Author: xhb
 * @email xiaobo97@163.com
 * gitee: https://gitee.com/xiaobo97
 * @Date: 2021/6/16 0:02
 * @description: buffer api
 * <p>
 * <p>
 * buffer的操作
 * Buffer clear() 清空缓冲区并返回对缓冲区的引用
 * Buffer flip() 为 将缓冲区的界限设置为当前位置，并将当前位置置为 0
 * int capacity() 返回 Buffer 的 capacity 大小
 * boolean hasRemaining() 判断缓冲区中是否还有元素
 * int limit() 返回 Buffer 的界限(limit) 的位置
 * Buffer limit(int n) 将设置缓冲区界限为 n, 并返回一个具有新 limit 的缓冲区对象
 * Buffer mark() 对缓冲区设置标记
 * int position() 返回缓冲区的当前位置 position
 * Buffer position(int n) 将设置缓冲区的当前位置为 n , 并返回修改后的 Buffer 对象
 * int remaining() 返回 position 和 limit 之间的元素个数
 * Buffer reset() 将位置 position 转到以前设置的 mark 所在的位置
 * Buffer rewind() 将位置设为为 0， 取消设置的 mark
 * 
 * 
 * 数据的操作
 * Buffer 所有子类提供了两个用于数据操作的方法：get()put() 方法
 * 取获取 Buffer中的数据
 * get() ：读取单个字节
 * get(byte[] dst)：批量读取多个字节到 dst 中
 * get(int index)：读取指定索引位置的字节(不会移动 position)
 *
 * 放到 入数据到 Buffer 中 中
 * put(byte b)：将给定单个字节写入缓冲区的当前位置
 * put(byte[] src)：将 src 中的字节写入缓冲区的当前位置
 * put(int index, byte b)：将指定字节写入缓冲区的索引位置(不会移动 position)
 */
public class BufferTest01 {


    @Test
    public void test1() {
        // 分配一个容量为10的缓冲区
        ByteBuffer buffer = ByteBuffer.allocate(10);
        System.out.println("缓冲区的当前位置：" + buffer.position());// 0
        System.out.println("当前limit位置" + buffer.limit());// 10
        System.out.println("当前buffer容量" + buffer.capacity());// 10
        // 添加元素
        buffer.put("xiaobo".getBytes());
        System.out.println("缓冲区的当前位置：" + buffer.position());// 6
        System.out.println("当前limit位置" + buffer.limit());// 10
        System.out.println("当前buffer容量" + buffer.capacity());// 10
        //调用flip方法  limit设置为当前位置，当前位置设为0   可读模式
        buffer.flip();
        System.out.println("缓冲区的当前位置：" + buffer.position());// 0
        System.out.println("当前limit位置" + buffer.limit());// 6
        System.out.println("当前buffer容量" + buffer.capacity());// 10
        //获取
        System.out.println((char) buffer.get());// x
        System.out.println("缓冲区的当前位置：" + buffer.position());// 1
        System.out.println("当前limit位置" + buffer.limit());// 6
        System.out.println("当前buffer容量" + buffer.capacity());// 10
    }


    @Test
    public void test2() {
        // 分配一个容量为10的缓冲区
        ByteBuffer buffer = ByteBuffer.allocate(10);
        System.out.println("缓冲区的当前位置：" + buffer.position());// 0
        System.out.println("当前limit位置" + buffer.limit());// 10
        System.out.println("当前buffer容量" + buffer.capacity());// 10
        // 添加元素
        buffer.put("xiaobo".getBytes());
        System.out.println("缓冲区的当前位置：" + buffer.position());// 6
        System.out.println("当前limit位置" + buffer.limit());// 10
        System.out.println("当前buffer容量" + buffer.capacity());// 10
        // clear 不清除数据，恢复位置
        buffer.clear();
        System.out.println("缓冲区的当前位置：" + buffer.position());// 0
        System.out.println("当前limit位置" + buffer.limit());// 10
        System.out.println("当前buffer容量" + buffer.capacity());// 10

        System.out.println((char) buffer.get());// x

        System.out.println("==============");
        ByteBuffer buffer1 = ByteBuffer.allocate(10);
        buffer1.put("viwviw".getBytes());

        buffer1.flip();

        byte[] bytes = new byte[4];
        buffer1.get(bytes);
        System.out.println(new String(bytes));//viwv


        System.out.println(buffer1.toString());
        System.out.println("缓冲区的当前位置：" + buffer1.position());// 4
        System.out.println("当前limit位置" + buffer1.limit());// 6 前6个可以读取
        System.out.println("当前buffer容量" + buffer1.capacity());// 10
        System.out.println("======================");
        buffer1.mark(); // 标记当前4的位置
        byte[] bytes1 = new byte[2];
        buffer1.get(bytes1);
        System.out.println(new String(bytes1));//iw
        System.out.println(buffer1.toString());// 当前位置6  limit 6  容量10

        buffer1.reset();//回到标记位置  也就是上面的4
        if(buffer1.hasRemaining()){//告诉当前位置和限制之间是否有任何元素
            System.out.println("有剩余元素");
            System.out.println(buffer1.remaining());// 此缓冲区中剩余的元素数  2
        }

    }

}

```



### ByteBuffer



### byteBuffer+fileChannel



> fileChanel读写文件

```java
  public static void main(String[] args) {
        // filechannel
        try (FileChannel channel = new FileInputStream("viw.txt").getChannel()) { // 文件channel
            ByteBuffer buffer = ByteBuffer.allocate(10);// 准备一个缓冲区
            while (true) {
                var i = channel.read(buffer);// 从channel读 数据 写入buffer
                log.debug("字节数{}",i);
                if(i==-1) break;
                buffer.flip();// 读模式
                while (buffer.hasRemaining()) { // 是否有剩余数据
                    byte b = buffer.get();
                    log.debug("数据{}",(char)b);
                }
                buffer.clear();// 写模式
            }
        } catch (IOException e) {
        }
    }

// viw.txt
xiaoboxiaobo
    
// print out
23:52:48.394 [main] DEBUG com.viw.nioviw.c1.ByteBuffer01 - 字节数5
23:52:48.404 [main] DEBUG com.viw.nioviw.c1.ByteBuffer01 - 数据x
23:52:48.405 [main] DEBUG com.viw.nioviw.c1.ByteBuffer01 - 数据i
23:52:48.405 [main] DEBUG com.viw.nioviw.c1.ByteBuffer01 - 数据a
23:52:48.405 [main] DEBUG com.viw.nioviw.c1.ByteBuffer01 - 数据o
23:52:48.405 [main] DEBUG com.viw.nioviw.c1.ByteBuffer01 - 数据b
23:52:48.405 [main] DEBUG com.viw.nioviw.c1.ByteBuffer01 - 字节数5
23:52:48.405 [main] DEBUG com.viw.nioviw.c1.ByteBuffer01 - 数据o
23:52:48.405 [main] DEBUG com.viw.nioviw.c1.ByteBuffer01 - 数据x
23:52:48.405 [main] DEBUG com.viw.nioviw.c1.ByteBuffer01 - 数据i
23:52:48.405 [main] DEBUG com.viw.nioviw.c1.ByteBuffer01 - 数据a
23:52:48.405 [main] DEBUG com.viw.nioviw.c1.ByteBuffer01 - 数据o
23:52:48.405 [main] DEBUG com.viw.nioviw.c1.ByteBuffer01 - 字节数2
23:52:48.405 [main] DEBUG com.viw.nioviw.c1.ByteBuffer01 - 数据b
23:52:48.405 [main] DEBUG com.viw.nioviw.c1.ByteBuffer01 - 数据o
23:52:48.406 [main] DEBUG com.viw.nioviw.c1.ByteBuffer01 - 字节数-1
```





### 分散读集中写





### 黏包拆包





# 直接和非直接缓冲区



**非直接内存和直接内存**

> byte buffer 基于两种内存形式。堆和非堆
>
> 非直接内存是堆内存(jvm应用层面)，而直接内存是存储相同层面的内存，如jdk8中的堆外内存。
>
> java无法直接调用操作系统层面的东西，需要读写数据都是  先调io 读写到本地内存，再到应用内存处理，再写回本地内存，io再去调用处理
>
> 而直接内存是可以  直接io处理，读写直接在本地内存上，



**直接内存可以使用 `allocateDirect(int capacity)` 创建 一个直接字节缓冲区，内部调用的是 `new DirectByteBuffer`。`isDirect()为true说明是buffer是直接内存` **

```java
 public void test(){
        ByteBuffer allocate = ByteBuffer.allocate(10);
        System.out.println(allocate.isDirect());//false
        ByteBuffer byteBuffer = ByteBuffer.allocateDirect(10);
        System.out.println(byteBuffer.isDirect());// true
    }
```

直接内存使用场景：

- 1 有很大的数据需要存储，它的生命周期又很长
- 2 适合频繁的IO操作，比如网络并发场景





