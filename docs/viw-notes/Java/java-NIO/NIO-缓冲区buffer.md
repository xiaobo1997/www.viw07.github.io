

# buffer基本概念



- **容量(capacity)**：buffer内存块的大小，Buffer具有一定的固定大小，也称为"容量"，缓冲区容量不能为负，并且创建后不能更改。
- **限制limit**:限制可以操作数据的大小，limit后的数据不能读写，不能超过容量。**写入模式，限制等于buffer的容量。读取模式下，limit等于写入的数据量**。
- **位置position:** 下一个要读取或写入的数组的索引。缓冲区的位置不能为负.小于容量。
- **标记 (mark)与重置 (reset)**：标记是一个索引，通过 Buffer 中的 mark() 方法 指定 Buffer 中一个特定的 position(到了指定位置标记)，之后可以通过调用 reset() 方法恢复到这 个 position.(回到之前标记的地方)
- **标记、位置、限制、容量遵守以下不变式： 0 <= mark <= position <= limit <= capacity**





![image-20200619172434538](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200619172434538.png)



---



实例化一个Buffer

![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/0021.png)

写模式下，position 是写入位置，limit 等于容量，下图表示写入了 4 个字节后的状态

![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/0018.png)

flip 动作发生后，position 切换为读取位置，limit 切换为读取限制

![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/0019.png)

读取 4 个字节后，状态

![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/0020.png)

clear 动作发生后，状态

![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/0021.png)

compact 方法，是把未读完的部分向前压缩，然后切换至写模式

![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/0022.png)



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



## ByteBuffer



> 读写数据
>
> **写数据：**
>
> - 调用channel的read方法  `channel.read(buffer)`
> - 调用buffer的put方法，  `buffer.put("xxx".getBytes)`
>
> **写数据：**
>
> - 调用channel 的 write方法  `channel.write(buffer)`
> - 调用buffer的 get方法  ` byte b = buffer.get()`

get 方法会让 position 读指针向后走，如果想重复读取数据

* 可以调用 rewind 方法将 position 重新置为 0
* 或者调用 get(int i) 方法获取索引 i 的内容，它不会移动读指针



> mark和reset
>
> mark标记索引   reset回到标记位置
>
> **rewind 和 flip 都会清除 mark 位置**



> 字符串和ByteBuffer互转

```java
  /**
     * string => buffer
     */
    @Test
    public void test1() {
        var buffer = ByteBuffer.allocate(16);
        var s = "xiaobo".getBytes();
        buffer.put(s);
        byteBufferUtil.debugAll(buffer);
    }
// 还是写模式
+--------+-------------------- all ------------------------+----------------+
position: [6], limit: [16]
         +-------------------------------------------------+
         |  0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f |
+--------+-------------------------------------------------+----------------+
|00000000| 78 69 61 6f 62 6f 00 00 00 00 00 00 00 00 00 00 |xiaobo..........|
+--------+-------------------------------------------------+----------------+
```



```java

    // charset
    @Test
    public void test2() {
        var buffer = StandardCharsets.UTF_8.encode("xiaobo");
        byteBufferUtil.debugAll(buffer);
    }
// 切换为 读模式
position: [0], limit: [6]
         +-------------------------------------------------+
         |  0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f |
+--------+-------------------------------------------------+----------------+
|00000000| 78 69 61 6f 62 6f                               |xiaobo          |
+--------+-------------------------------------------------+----------------+
```



```java
  @Test
    public void test3(){
        var buffer = ByteBuffer.wrap("xiaobo".getBytes());
        byteBufferUtil.debugAll(buffer);
    }
    position: [0], limit: [6]
         +-------------------------------------------------+
         |  0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f |
+--------+-------------------------------------------------+----------------+
|00000000| 78 69 61 6f 62 6f                               |xiaobo          |
+--------+-------------------------------------------------+----------------+
```



```java

@Test
    public void test4(){
        var buffer = ByteBuffer.wrap("xiaobo".getBytes());
        byteBufferUtil.debugAll(buffer);
        System.out.println("buffer===> string");
        System.out.println(StandardCharsets.UTF_8.decode(buffer));
    }
position: [0], limit: [6]
         +-------------------------------------------------+
         |  0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f |
+--------+-------------------------------------------------+----------------+
|00000000| 78 69 61 6f 62 6f                               |xiaobo          |
+--------+-------------------------------------------------+----------------+
buffer===> string
xiaobo
```





## Buffern内部结构工具类

> 查看buffer内部工具类

```xml
<!--pom.xml-->
 <dependency>
            <groupId>io.netty</groupId>
            <artifactId>netty-all</artifactId>
            <version>4.1.39.Final</version>
</dependency>
```



```java
import io.netty.util.internal.StringUtil;

import java.nio.ByteBuffer;
import static io.netty.util.internal.StringUtil.NEWLINE;
import static io.netty.util.internal.MathUtil.isOutOfBounds;

/**
 * @Author: xhb
 * @email xiaobo97@163.com
 * gitee: https://gitee.com/xiaobo97
 * @Date: 2021/6/21 1:24
 * @description:
 */
public class ByteBufferUtil {
    private static final char[] BYTE2CHAR = new char[256];
    private static final char[] HEXDUMP_TABLE = new char[256 * 4];
    private static final String[] HEXPADDING = new String[16];
    private static final String[] HEXDUMP_ROWPREFIXES = new String[65536 >>> 4];
    private static final String[] BYTE2HEX = new String[256];
    private static final String[] BYTEPADDING = new String[16];

    static {
        final char[] DIGITS = "0123456789abcdef".toCharArray();
        for (int i = 0; i < 256; i++) {
            HEXDUMP_TABLE[i << 1] = DIGITS[i >>> 4 & 0x0F];
            HEXDUMP_TABLE[(i << 1) + 1] = DIGITS[i & 0x0F];
        }

        int i;

        // Generate the lookup table for hex dump paddings
        for (i = 0; i < HEXPADDING.length; i++) {
            int padding = HEXPADDING.length - i;
            StringBuilder buf = new StringBuilder(padding * 3);
            for (int j = 0; j < padding; j++) {
                buf.append("   ");
            }
            HEXPADDING[i] = buf.toString();
        }

        // Generate the lookup table for the start-offset header in each row (up to 64KiB).
        for (i = 0; i < HEXDUMP_ROWPREFIXES.length; i++) {
            StringBuilder buf = new StringBuilder(12);
            buf.append(NEWLINE);
            buf.append(Long.toHexString(i << 4 & 0xFFFFFFFFL | 0x100000000L));
            buf.setCharAt(buf.length() - 9, '|');
            buf.append('|');
            HEXDUMP_ROWPREFIXES[i] = buf.toString();
        }

        // Generate the lookup table for byte-to-hex-dump conversion
        for (i = 0; i < BYTE2HEX.length; i++) {
            BYTE2HEX[i] = ' ' + StringUtil.byteToHexStringPadded(i);
        }

        // Generate the lookup table for byte dump paddings
        for (i = 0; i < BYTEPADDING.length; i++) {
            int padding = BYTEPADDING.length - i;
            StringBuilder buf = new StringBuilder(padding);
            for (int j = 0; j < padding; j++) {
                buf.append(' ');
            }
            BYTEPADDING[i] = buf.toString();
        }

        // Generate the lookup table for byte-to-char conversion
        for (i = 0; i < BYTE2CHAR.length; i++) {
            if (i <= 0x1f || i >= 0x7f) {
                BYTE2CHAR[i] = '.';
            } else {
                BYTE2CHAR[i] = (char) i;
            }
        }
    }

    /**
     * 打印所有内容
     * @param buffer
     */
    public static void debugAll(ByteBuffer buffer) {
        int oldlimit = buffer.limit();
        buffer.limit(buffer.capacity());
        StringBuilder origin = new StringBuilder(256);
        appendPrettyHexDump(origin, buffer, 0, buffer.capacity());
        System.out.println("+--------+-------------------- all ------------------------+----------------+");
        System.out.printf("position: [%d], limit: [%d]\n", buffer.position(), oldlimit);
        System.out.println(origin);
        buffer.limit(oldlimit);
    }

    /**
     * 打印可读取内容
     * @param buffer
     */
    public static void debugRead(ByteBuffer buffer) {
        StringBuilder builder = new StringBuilder(256);
        appendPrettyHexDump(builder, buffer, buffer.position(), buffer.limit() - buffer.position());
        System.out.println("+--------+-------------------- read -----------------------+----------------+");
        System.out.printf("position: [%d], limit: [%d]\n", buffer.position(), buffer.limit());
        System.out.println(builder);
    }

    private static void appendPrettyHexDump(StringBuilder dump, ByteBuffer buf, int offset, int length) {
        if (isOutOfBounds(offset, length, buf.capacity())) {
            throw new IndexOutOfBoundsException(
                    "expected: " + "0 <= offset(" + offset + ") <= offset + length(" + length
                            + ") <= " + "buf.capacity(" + buf.capacity() + ')');
        }
        if (length == 0) {
            return;
        }
        dump.append(
                "         +-------------------------------------------------+" +
                        NEWLINE + "         |  0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f |" +
                        NEWLINE + "+--------+-------------------------------------------------+----------------+");

        final int startIndex = offset;
        final int fullRows = length >>> 4;
        final int remainder = length & 0xF;

        // Dump the rows which have 16 bytes.
        for (int row = 0; row < fullRows; row++) {
            int rowStartIndex = (row << 4) + startIndex;

            // Per-row prefix.
            appendHexDumpRowPrefix(dump, row, rowStartIndex);

            // Hex dump
            int rowEndIndex = rowStartIndex + 16;
            for (int j = rowStartIndex; j < rowEndIndex; j++) {
                dump.append(BYTE2HEX[getUnsignedByte(buf, j)]);
            }
            dump.append(" |");

            // ASCII dump
            for (int j = rowStartIndex; j < rowEndIndex; j++) {
                dump.append(BYTE2CHAR[getUnsignedByte(buf, j)]);
            }
            dump.append('|');
        }

        // Dump the last row which has less than 16 bytes.
        if (remainder != 0) {
            int rowStartIndex = (fullRows << 4) + startIndex;
            appendHexDumpRowPrefix(dump, fullRows, rowStartIndex);

            // Hex dump
            int rowEndIndex = rowStartIndex + remainder;
            for (int j = rowStartIndex; j < rowEndIndex; j++) {
                dump.append(BYTE2HEX[getUnsignedByte(buf, j)]);
            }
            dump.append(HEXPADDING[remainder]);
            dump.append(" |");

            // Ascii dump
            for (int j = rowStartIndex; j < rowEndIndex; j++) {
                dump.append(BYTE2CHAR[getUnsignedByte(buf, j)]);
            }
            dump.append(BYTEPADDING[remainder]);
            dump.append('|');
        }

        dump.append(NEWLINE +
                "+--------+-------------------------------------------------+----------------+");
    }

    private static void appendHexDumpRowPrefix(StringBuilder dump, int row, int rowStartIndex) {
        if (row < HEXDUMP_ROWPREFIXES.length) {
            dump.append(HEXDUMP_ROWPREFIXES[row]);
        } else {
            dump.append(NEWLINE);
            dump.append(Long.toHexString(rowStartIndex & 0xFFFFFFFFL | 0x100000000L));
            dump.setCharAt(dump.length() - 9, '|');
            dump.append('|');
        }
    }

    public static short getUnsignedByte(ByteBuffer buffer, int index) {
        return (short) (buffer.get(index) & 0xFF);
    }
}
```

效果：

```java
+--------+-------------------- all ------------------------+----------------+
position: [6], limit: [16]
         +-------------------------------------------------+
         |  0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f |
+--------+-------------------------------------------------+----------------+
|00000000| 78 69 61 6f 62 6f 00 00 00 00 00 00 00 00 00 00 |xiaobo..........|
+--------+-------------------------------------------------+----------------+
```

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210621015032719.png" alt="image-20210621015032719" style="zoom:50%;" />

## byteBuffer+fileChannel



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





## 分散读集中写

> 分散读

```java
 @Test
    public  void test1() throws  Exception{
        var channel = new RandomAccessFile("viw.txt","r").getChannel();
        var b1 = ByteBuffer.allocate(6);
        var b2 = ByteBuffer.allocate(6);
        channel.read(new ByteBuffer[]{b1,b2});
        b1.flip();b2.flip();
        ByteBufferUtil.debugAll(b1);
        ByteBufferUtil.debugAll(b2);
    }
// print out
position: [0], limit: [6]
         +-------------------------------------------------+
         |  0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f |
+--------+-------------------------------------------------+----------------+
|00000000| 78 69 61 6f 62 6f                               |xiaobo          |
+--------+-------------------------------------------------+----------------+
+--------+-------------------- all ------------------------+----------------+
position: [0], limit: [6]
         +-------------------------------------------------+
         |  0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f |
+--------+-------------------------------------------------+----------------+
|00000000| 78 69 61 6f 62 6f                               |xiaobo          |
+--------+-------------------------------------------------+----------------+
```



> 集中写

```java
public void test2() throws FileNotFoundException {
        try (var channel = new RandomAccessFile("viw.txt", "rw").getChannel()) {
            var b1 = ByteBuffer.allocate(2);
            var b2 = ByteBuffer.allocate(3);
            b1.put("he".getBytes());b2.put("llo".getBytes());
            b1.flip();b2.flip();
            channel.write(new ByteBuffer[]{b1,b2});
            ByteBufferUtil.debugAll(b1);
            ByteBufferUtil.debugAll(b2);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
```



## 黏包半包拆包

​	一般网络编程中传输数据会用标识符标识一行 `\n` ，可能被重新组合。

如  

> ```
> viw\n
> xiaobo\n
> hello\n
> ===> 服务端接收时 buffer有限 等原因 变成了
> viw\nxiao
> bo\nhello\n
> ```



```java

public class ByteBufferExam02 {

    public static void main(String[] args) {
        ByteBuffer source = ByteBuffer.allocate(32);
        //                     11            24
        source.put("Hello,world\nI'm zhangsan\nHo".getBytes());
        split(source);

        source.put("w are you?\nhaha!\n".getBytes());
        split(source);
    }

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
        buffer.compact();
    }
position: [0], limit: [27]
|00000000| 48 65 6c 6c 6f 2c 77 6f 72 6c 64 0a 49 27 6d 20 |Hello,world.I'm |
|00000010| 7a 68 61 6e 67 73 61 6e 0a 48 6f 00 00 00 00 00 |zhangsan.Ho.....|
position: [12], limit: [12]
|00000000| 48 65 6c 6c 6f 2c 77 6f 72 6c 64 0a             |Hello,world.    |
position: [12], limit: [27]
|00000000| 48 65 6c 6c 6f 2c 77 6f 72 6c 64 0a 49 27 6d 20 |Hello,world.I'm |
|00000010| 7a 68 61 6e 67 73 61 6e 0a 48 6f 00 00 00 00 00 |zhangsan.Ho.....|
position: [13], limit: [13]
|00000000| 49 27 6d 20 7a 68 61 6e 67 73 61 6e 0a          |I'm zhangsan.   |
position: [0], limit: [19]
|00000000| 48 6f 77 20 61 72 65 20 79 6f 75 3f 0a 68 61 68 |How are you?.hah|
|00000010| 61 21 0a 6e 67 73 61 6e 0a 48 6f 00 00 00 00 00 |a!.ngsan.Ho.....|
position: [13], limit: [13]
|00000000| 48 6f 77 20 61 72 65 20 79 6f 75 3f 0a          |How are you?.   |
position: [13], limit: [19]
|00000000| 48 6f 77 20 61 72 65 20 79 6f 75 3f 0a 68 61 68 |How are you?.hah|
|00000010| 61 21 0a 6e 67 73 61 6e 0a 48 6f 00 00 00 00 00 |a!.ngsan.Ho.....|
position: [6], limit: [6]
|00000000| 68 61 68 61 21 0a                               |haha!.          |
```



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





