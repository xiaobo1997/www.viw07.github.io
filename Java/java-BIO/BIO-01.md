[TOC]

# IO



##  继承树和基本概念

**java世界中的IO大体分为：**

- 1.磁盘文件操作：File
- 2.字节操作：InputStream , OutputStream
- 3.字符操作：Reader ，Writer
- 4.对象操作： Serializable
- 5.网络操作：Socker
- 6.新IO(`Java 新I/O 库是在 1.4 版本引入到 java.nio.* 包中的，旨在更快速。`)：NIO



**说一下字节和字符：**



Bit是最小的二进制单元，是计算机的操作单元。取值 0 和1

- 字节(Byte)：

Byte是计算机操作数据的最小单元，8位bit 组成， -128 到127

- 字符 char

是用户可读写的最小单元，在Java里面由16位bit组成 取值（0-65535）

- 字节流：

操作byte类型数据，父类是 `OutputStream`  `InputStream` 不用缓冲区，不close关闭资源也可以输出，是直接对文件操作

- 字符流：

操作字符数据类型，父类是 `Write`  `Reader`   使用了缓冲区，如果不close关闭资源就不会输出然后内容，并且可以使用flush 强制刷新。这样就可以强制刷新不关闭输出



**说一下字符和字节的转换**

​		在整个IO中，大概分为的是字节和字符，还可以转换

- 1.OutputStreamWriter

OutputStreamWriter是一个类，用于将==字符流转换为字节流==，使用指定的字符集将字符编码为字节。 write（）方法调用编码转换器，该转换器将字符转换为字节。 然后将生成的字节存储在缓冲区中，然后再写入基础输出流。 传递给write（）方法的字符不被缓冲。 我们通过在BufferedWriter中使用OutputStreamWriter来优化其性能，以避免频繁的转换器调用。

- 2.InputStreamWriter

InputStreamReader是从==字节流到字符流的==桥梁：它读取字节，并使用指定的字符集将它们解码为字符。 它使用的字符集可以通过名称指定，也可以明确指定，或者可以接受平台的默认字符集





**先说一下 新IO：**



新 I/O 使用 **NIO**（同步非阻塞）的方式重写了老的 I/O 了，因此它获得了 **NIO** 的种种优点。即使我们不显式地使用 **NIO** 方式来编写代码，也能带来性能和速度的提高。这种提升不仅仅体现在文件读写（File I/O），同时也体现在网络读写（Network I/O）中。



在新IO中 使用了更接近操作系统 I/O 执行方式的结构：**Channel**（通道） 和 **Buffer**（缓冲区）。



1. InputSreeam的继承树

抽象类、基于字节的**输入**操作、是所有输入流的父类、定义了所有输入流都具有的共同特征。

<img src="https://static.javatpoint.com/java/javaio/images/java-inputstream.png" alt="Java input stream hierarchy" style="zoom:67%;" />

2.OutputSreeam的继承树

抽象类、基于字节的**输出**操作、是所有输出流的父类、定义了所有输出流都具有的共同特征。

<img src="https://static.javatpoint.com/java/javaio/images/java-outputstream.png" alt="Java output stream hierarchy" style="zoom:67%;" />



3.Java Writer

它是用于写入字符流的抽象类。 子类必须实现的方法是write（char []，int，int），flush（）和close（）。 大多数子类将覆盖此处定义的某些方法，以提供更高的效率和/或功能。





4.Java Reader

Java Reader是用于读取字符流的抽象类。 子类必须实现的唯一方法是read（char []，int，int）和close（）。 但是，大多数子类将覆盖某些方法以提供更高的效率和/或附加功能，或者一些实现类是BufferedReader，CharArrayReader，FilterReader，InputStreamReader，PipedReader，StringReader





5.   RandomAccessFile

   功能丰富，可以从文件的任意位置进行存取（输入输出）操作；用于读取和写入随机访问文件。 随机访问文件的行为类似于大字节数组。 有一个指向该数组的游标，称为文件指针，通过移动游标我们可以执行读写操作。 如果在读取所需的字节数之前到达文件末尾，则抛出EOFException。 它是IOException的一种。



6. 继承树

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201110012101811.png" alt="image-20201110012101811" style="zoom:67%;" />



7. 概述图

![image-20201111004629688](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201111004629688.png)

8. 用途特征

- 文件访问
- 网络访问
- 内存缓存访问
- 线程内部通信(管道)
- 缓冲
- 过滤
- 解析
- 读写文本 (Readers / Writers)
- 读写基本类型数据 (long, int etc.)
- 读写对象



9.说一下关联

InputStream和Reader与数据源相关联，OutputStream和writer与目标媒介相关联。

![image-20201111004555677](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201111004555677.png)

## 2.磁盘文件操作 File IO



### File

> file类可以表示一个文件，还可以表示一个目录的路径，是一个抽象类，他的几个方法可以对文件属性操作



> 构造方法

```java
//
File(File parent,String child)
//
File(String pathname)
//
File(String parent,String child)
//
File(URL uri)
```



> useful method

| static File | createTempFile(String prefix, String suffix) | It creates an empty file in the default temporary-file directory, using the given prefix and suffix to generate its name. |
| ----------- | -------------------------------------------- | ------------------------------------------------------------ |
| boolean     | createNewFile()                              | 当且仅当具有该抽象路径名的文件尚不存在时，它原子地创建一个由该抽象路径名命名的新的空文件。 |
| boolean     | canWrite()                                   | 它测试应用程序是否可以修改此抽象pathname.String []路径表示的文件。 |
| boolean     | canExecute()                                 | 测试应用程序是否可以执行此抽象路径名表示的文件               |
| boolean     | canRead()                                    | It tests whether the application can read the file denoted by this abstract pathname. |
| boolean     | isAbsolute()                                 | It tests whether this abstract pathname is absolute.         |
| boolean     | isDirectory()                                | It tests whether the file denoted by this abstract pathname is a directory. |
| boolean     | isFile()                                     | It tests whether the file denoted by this abstract pathname is a normal file. |
| String      | getName()                                    | It returns the name of the file or directory denoted by this abstract pathname. |
| String      | getParent()                                  | It returns the pathname string of this abstract pathname's parent, or null if this pathname does not name a parent directory. |
| Path        | toPath()                                     | 它返回从此抽象路径构造的java.nio.file.Path对象。             |
| URI         | toURI()                                      | It constructs a file: URI that represents this abstract pathname. |
| File[]      | listFiles()                                  | It returns an [array](https://www.javatpoint.com/array-in-java) of abstract pathnames denoting the files in the directory denoted by this abstract pathname |
| long        | getFreeSpace()                               | It returns the number of unallocated bytes in the partition named by this abstract path name. |
| String[]    | list(FilenameFilter filter)                  | It returns an array of strings naming the files and directories in the directory denoted by this abstract pathname that satisfy the specified filter. |
| boolean     | mkdir()                                      | 创建以此抽象路径名命名的目录。                               |

> example--1

```java
package java8.io;

import java.io.File;
import java.io.IOException;

/**
 * @Author: xiaobo
 * @Date: 2020/11/10 12:35
 */
public class File01 {
    public static void main(String[] args) throws IOException {
        File file = new File("D://xiaobo//a.txt");
        if (file.exists()) {
            System.out.println("存在");
        }else {
            file.createNewFile();
            System.out.println("创建成功");
        }
        System.out.println("存在吗"+file.exists()
        +"| "+file.isFile()+"|"+file.isDirectory()+"|"
                +file.getAbsolutePath()+"|" +file.getCanonicalPath()+"|"
                +file.getPath()+"|"
                +file.getParent()+"|"
                +file.getName()+"|"
                +file.listFiles()+"|"
                +file.length()
        );

    }
}

//console
创建成功
存在吗true| true|false|D:\xiaobo\a.txt|D:\xiaobo\a.txt|D:\xiaobo\a.txt|D:\xiaobo|a.txt|null|0

```



> exampl --2

```java
package java8.io;

import java.io.File;
import java.io.IOException;

/**
 * @Author: xiaobo
 * @Date: 2020/11/10 12:35
 */
public class File01 {
    public static void main(String[] args) throws IOException {
        String path = "D://xiaobo";
        File file = new File("D://xiaobo//a.txt");

        File filedir = new File(path);
        File[] files = filedir.listFiles();
        if (file.exists()) {
            System.out.println("存在");
        }else {
            file.createNewFile();
            System.out.println("创建成功");
        }
        System.out.println("存在吗"+file.exists()
        +"| "+file.isFile()+"|"+file.isDirectory()+"|"
                +file.getAbsolutePath()+"|" +file.getCanonicalPath()+"|"
                +file.getPath()+"|"
                +file.getParent()+"|"
                +file.getName()+"|"
                +file.listFiles()+"|"
                +file.length()
        );
        for (File file1 : files){

            System.out.println(file1.getName()+"|"+file1.canWrite()
                    +"|"+file1.length()
                    +"|"+file1.isHidden()
            );
        }
    }
}
// console
存在
存在吗true| true|false|D:\xiaobo\a.txt|D:\xiaobo\a.txt|D:\xiaobo\a.txt|D:\xiaobo|a.txt|null|0
a.properties|true|0|false
a.txt|true|0|false
b.txt|true|0|false

```



> example-3 输出路径下面所有文件夹

```java
package io;

import java.io.File;
import java.util.ArrayList;

/**
 * @Author: xhb
 * @email xiaobo97@163.com
 * gitee: https://gitee.com/xiaobo97
 * @Date: 2021/4/26 0:37
 * @description:
 */
public class File02 {

    /**
     * Output all folders under the specified path
     *
     * @param path
     */
    static void printAllFileName(String path) {
        ArrayList<File> files = new ArrayList<>();
        File file = new File(path);
        File[] listFiles = file.listFiles();
        if (!file.exists()) return;
        for (File f : listFiles) {
            if (f.isFile()) {
                files.add(f);
            } else if (f.isDirectory()) {
                System.out.println(f.getAbsolutePath());
                printAllFileName(f.getAbsolutePath());
            }
        }
    }

    public static void main(String[] args) {
        printAllFileName("E:\\Users");
        /**
         * E:\Users\sczyh30
         * E:\Users\sczyh30\logs
         * E:\Users\sczyh30\logs\csp
         */
    }
}

```





### FileInputStream



> 从文件中读取数据，字节流

**总结：**

Java FileInputStream类从文件获取输入字节。 它用于读取面向字节的数据（原始字节流），例如图像数据，音频，视频等。您还可以读取字符流数据。 但是，为了读取字符流，建议使用FileReader类。

将文件中的数据输入到内存中，用来读文件操作，无法读取中文字符



> FileInputStream  class declaration
>
> 继承了 `InputSteam`, 

```java
public class FileInputStream extends InputStream
```

> methods

| int available()                      | 返回下一次对此输入流调用的方法可以不受阻塞地从此输入流读取的字节数。返回一个整数值 |
| ------------------------------------ | ------------------------------------------------------------ |
| int read()                           | 从输入流中读取数据字节                                       |
| int read(byte[] b)                   | 最多从输入流中读取 b.length 个数据字节                       |
| int read(byte[] b, int off, int len) | 从输入流中获取 len个字节， off是数组的起始偏移量             |
| long skip(long x)                    | 跳过并丢弃输入流中的x字节数据                                |
| FileChannel getChannel()             | 它用于返回与文件输入流关联的唯一FileChannel对象。            |
| FileDescriptor getFD()               | 它用于返回FileDescriptor对象。                               |
| protected void finalize()            | 在没有更多对文件输入流的引用时调用close方法。                |
| void close()                         | 关闭流                                                       |

>exampl--01

```java
package java8.io;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;

/**
 * @Author: xiaobo
 * @Date: 2020/11/10 17:27
 */
public class FileInput01 {
    public static void main(String[] args) {
        File file = new File("D://xiaobo//a.txt");
        try {
            FileInputStream fi = new FileInputStream(file);
            for (int a=0;a<file.length();a++){
                int i = fi.read();
                System.out.print((char)i);

            }
            fi.close();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }

    }
}

// a.txt
abc
// console
abc
```







### FileOutputStream

> 向文件中写数据，字节流

**总结：**

Java FileOutputStream是用于将数据写入文件的输出流。如果必须将原始值写入文件，请使用FileOutputStream类。 您可以通过FileOutputStream类编写面向字节和面向字符的数据。 但是，对于面向字符的数据，首选使用FileWriter而不是FileOutputStream。



> 类的声明

```java
public class FileOutputStream extends OutputStream
```



> method

| protected void finalize()                | 清理文件输出流的数据连接                              |
| ---------------------------------------- | ----------------------------------------------------- |
| void write(byte[] ary)                   | 把ary.legnth个字节从字节数组写入文件输出流            |
| void write(byte[] ary, int off, int len) | 把len个长度的 字节数组写入文件输出流，off是初始偏移量 |
| void write(int b)                        | 将指定的字节写入输出流中                              |
| FileChannel getChannel()                 | 返回与文件输出流关联的文件通道对象。                  |
| FileDescriptor getFD()                   | 返回与流关联的文件描述符。                            |
| void close()                             | 关闭输出流                                            |

> exampl -- 01

```java
package java8.io;

import java.io.*;

/**
 * @Author: xiaobo
 * @Date: 2020/11/10 19:13
 */
public class FileOutpot01 {
    public static void main(String[] args) {
        File file = new File("D://xiaobo//a.txt");
        try {
            FileOutputStream fileOutputStream = new FileOutputStream(file);
            FileInputStream fileInputStream = new FileInputStream(file);
            fileOutputStream.write(65);
            fileOutputStream.close();
            System.out.println("写人成功");
            for (int i = 0;i<file.length();i++){
                int read = fileInputStream.read();
                System.out.print((char) read);
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
// console
A
// a.txt
A
```

> example-- 02

```java
package java8.io;

import java.io.*;

/**
 * @Author: xiaobo
 * @Date: 2020/11/10 19:13
 */
public class FileOutpot01 {
    public static void main(String[] args) {
        File file = new File("D://xiaobo//a.txt");
        try {
            FileOutputStream fileOutputStream = new FileOutputStream(file);
            FileInputStream fileInputStream = new FileInputStream(file);
//            fileOutputStream.write(65);
            byte[] bytes = "hello xiaobo".getBytes();
            fileOutputStream.write(bytes);
            fileOutputStream.close();
            System.out.println("写人成功");
            for (int i = 0;i<file.length();i++){
                int read = fileInputStream.read();
                System.out.print((char) read);
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
package java8.io;

import java.io.*;

/**
 * @Author: xiaobo
 * @Date: 2020/11/10 19:13
 */
public class FileOutpot01 {
    public static void main(String[] args) {
        File file = new File("D://xiaobo//a.txt");
        try {
            FileOutputStream fileOutputStream = new FileOutputStream(file);
            FileInputStream fileInputStream = new FileInputStream(file);
//            fileOutputStream.write(65);
            byte[] bytes = "hello xiaobo".getBytes();
            fileOutputStream.write(bytes);
            fileOutputStream.close();
            System.out.println("写人成功");
            for (int i = 0;i<file.length();i++){
                int read = fileInputStream.read();
                System.out.print((char) read);
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
// console
hello xiaobo
```





### FileWriter

> Java FileWriter类用于将面向字符的数据写入文件。 它是面向字符的类，用于java中的文件处理。与FileOutputStream类不同，您不需要将字符串转换为字节数组，因为它提供了直接写入字符串的方法。



> 该类的声明

```java
public class FileWriter extends OutputStreamWriter  

```

> 构造方法

| FileWriter(String file) | Creates a new file. It gets file name in [string](https://www.javatpoint.com/java-string). |
| ----------------------- | ------------------------------------------------------------ |
| FileWriter(File file)   | Creates a new file. It gets file name in File [object](https://www.javatpoint.com/object-and-class-in-java). |



> method

| void write(String text) | 用于将字符串写入FileWriter。   |
| ----------------------- | ------------------------------ |
| void write(char c)      | 用于将字符写入FileWriter。     |
| void write(char[] c)    | 用于将字符数组写入FileWriter。 |
| void flush()            | 刷新FileWriter的数据。         |
| void close()            | 关闭FileWriter。               |

> example -1

```java
public class ExWriter{
    public static void main(String[] args){
        
        try {
            FileWriter   fileWriter = new FileWriter(file);
            fileWriter.write("xiaobo");
            fileWriter.flush();
        } catch (IOException e) {
            e.printStackTrace();
        }
        System.out.println("success");
    }
    
}
```







### FileReader

> Java FileReader类用于从文件读取数据。 它以字节格式返回数据，例如FileInputStream类。它是面向字符的类，用于Java中的文件处理。



> 类声明

```java
public class FileReader extends InputStreamReader  
```

> 构造方法

| FileReader(String file) | 它以字符串形式获取文件名。 它以读取模式打开给定的文件。 如果文件不存在，则抛出FileNotFoundException。 |
| ----------------------- | ------------------------------------------------------------ |
| FileReader(File file)   | 它在文件实例中获取文件名。 它以读取模式打开给定的文件。 如果文件不存在，则抛出FileNotFoundException。 |



> method

| int read()   | 它用于返回ASCII格式的字符。 它在文件末尾返回-1。 |
| ------------ | ------------------------------------------------ |
| void close() | 关闭流                                           |

> example--01

```java
public class FileReaderExam{
    public static void main(String[] args) throw IOException{
       FileReader fr = new FileReader("D://xiaobo//a.txt");
    	int i ;
        while( (i = fr.read()) != -1){
            System.out.print((char)i);
        }
    }   
}
```











## 3. 字节操作



### BufferedInputStream

> Java BufferedInputStream类用于从流中读取信息。 它在内部使用缓冲区机制来提高性能。BufferedInputStream的要点是：当跳过或读取流中的字节时，内部缓冲区自动从所包含的输入流中重新填充一次，每次很多字节。 创建后，将创建一个内部缓冲区数组。

==*内置缓存字节数组的大小 8KB*==

**说一下这里的缓冲区**

不带缓冲的操作，每读一个字节就要写入一个字节，由于涉及磁盘的IO操作相比内存的操作要慢很多，所以不带缓冲的流效率很低。带缓冲的流，可以一次读很多字节，但不向磁盘中写入，只是先放到内存里。等凑够了缓冲区大小的时候一次性写入磁盘，这种方式可以减少磁盘操作次数，速度就会提高很多！

要注意在使用`BufferedOutputStream`写完数据后，要调用`flush()`方法或`close()`方法，强行将缓冲区中的数据写出。否则可能无法写出数据。与之相似还`BufferedReader`和`BufferedWriter`两个类。



> 类声明 
>
> 继承关系
>
> - InputStream
>   - FilterInputStream
>     - BufferedInputStream

```
public class BufferedInputStream extends FilterInputStream  

```

> 构造方法

| BufferedInputStream(InputStream IS)           | 它创建BufferedInputStream并将其参数（输入流IS）保存起来供以后使用。 |
| --------------------------------------------- | ------------------------------------------------------------ |
| BufferedInputStream(InputStream IS, int size) | 它创建具有指定缓冲区大小的BufferedInputStream，并将其参数（输入流IS）保存起来以备后用。 |



> method

| int available()                     | 它返回可以从输入流读取的估计字节数(从源中获取*有效可供读取的字节数*)，不会被输入流的下一个调用方法阻塞。 |
| ----------------------------------- | ------------------------------------------------------------ |
| int read()                          | 它从输入流中读取下一个数据字节。                             |
| int read(byte[] b, int off, int ln) | 从给定的偏移量开始，它将指定的字节输入流中的字节读入指定的字节数组。 |
| void close()                        | 它关闭输入流并释放与该流关联的任何系统资源。                 |
| void reset()                        | 它将流重新定位在此输入流上最后一次调用mark方法的位置。       |
| void mark(int readlimit)            | 标记当前buf中读取下一个字节的下标                            |
| long skip(long x)                   | 跳过n个字节、 不仅仅是buf中的有效字节、也包括in的源中的字节  |
| boolean markSupported()             | 查看此流是否支持mark                                         |



> example -- 01

```java
package java8.io;

import java.io.BufferedInputStream;
import java.io.FileInputStream;
import java.io.IOException;

/**
 * @Author: xiaobo
 * @Date: 2020/11/10 23:16
 */
public class BufferInputSreeam {
    public static void main(String[] args) throws IOException {
        BufferedInputStream bufferInputSreeam =
                new BufferedInputStream( new FileInputStream("D://xiaobo//a.txt"));
        int i ;
        while ((i = bufferInputSreeam.read()) != -1) {
            System.out.print((char)i);
        }
        bufferInputSreeam.close();
    }
}

```



### BufferedOutputStream

> Java BufferedOutputStream类用于缓冲输出流。 它在内部使用缓冲区存储数据。 与将数据直接写入流相比，它提高了效率。 因此，它可以提高性能。要在OutputStream中添加缓冲区，请使用BufferedOutputStream类。

```java
// 在OutputStream中添加缓冲区的语法
OutputStream os = new BufferedOutputStream("D://xiaobo//a.txt")
```

==*默认缓冲大小是 8192 字节( 8KB )*==

> 类声明
>
> 继承关系
>
> - OutputStream
>   - FilterOutputStream
>     - BufferedOutputStream

```
public class BufferedOutputStream extends FilterOutputStream  
```



> 构造函数

| BufferedOutputStream(OutputStream os)           | 它创建新的缓冲输出流，该输出流用于将数据写入指定的输出流。   |
| ----------------------------------------------- | ------------------------------------------------------------ |
| BufferedOutputStream(OutputStream os, int size) | 它创建新的缓冲输出流，该缓冲流用于将数据写入具有指定缓冲区大小的指定输出流。 |

> method

| void write(int b)                      | It writes the specified byte to the buffered output stream.  |
| -------------------------------------- | ------------------------------------------------------------ |
| void write(byte[] b, int off, int len) | 它将指定字节输入流中的字节从给定偏移量开始写入指定字节数组中 |
| void flush()                           | It flushes the buffered output stream.                       |



> example -- 01
>
> 会把原来的覆盖

```java
package java8.io;

import java.io.BufferedOutputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.OutputStream;

/**
 * @Author: xiaobo
 * @Date: 2020/11/10 23:27
 */
public class BufferedOPStream {
    public static void main(String[] args) throws IOException {
        BufferedOutputStream os = new BufferedOutputStream(new FileOutputStream("D:\\xiaobo\\a.txt"));
        os.write("xiaobo".getBytes());
        os.flush();
        os.close();
        System.out.println("OK");
    }
}

```



## 4.字符操作



### Writer

> 它是用于写入字符流的抽象类。 子类必须实现的方法是write（char []，int，int），flush（）和close（）。 大多数子类将覆盖此处定义的某些方法，以提供更高的效率和/或功能。
>
> Writer类是Java IO中所有Writer的基类。子类包括BufferedWriter和PrintWriter等等。



> fields

| protected Object | lock | The object used to synchronize operations on this stream. |
| ---------------- | ---- | --------------------------------------------------------- |
|                  |      |                                                           |



> constructor

| protected | Writer()            | It creates a new character-stream writer whose critical sections will synchronize on the writer itself. |
| --------- | ------------------- | ------------------------------------------------------------ |
| protected | Writer(Object lock) | It creates a new character-stream writer whose critical sections will synchronize on the given [object](https://www.javatpoint.com/object-and-class-in-java). |





> example--01

```java
package java8.io;

import java.io.File;
import java.io.FileWriter;
import java.io.IOException;
import java.io.Writer;

/**
 * @Author: xiaobo
 * @Date: 2020/11/10 23:43
 */
public class WriterEx {
    public static void main(String[] args) {
        try {
            Writer writer =  new FileWriter("D://xiaobo//a.txt");
            writer.write("xiaobo777");
            writer.flush();
            writer.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

```



### Reader

>Java Reader是用于读取字符流的抽象类。 子类必须实现的唯一方法是read（char []，int，int）和close（）。 但是，大多数子类将覆盖某些方法以提供更高的效率和/或附加功能，或者一些实现类是BufferedReader，CharArrayReader，FilterReader，InputStreamReader，PipedReader，StringReader
>
>我们大多数用的是都是子类
>
>Reader类是Java IO中所有Reader的基类。子类包括BufferedReader，PushbackReader，InputStreamReader，StringReader和其他Reader。



> 构造函数

| protected | Reader()            | 创建了一个新的字符读流                                       |
| --------- | ------------------- | ------------------------------------------------------------ |
| protected | Reader(Object lock) | It creates a new character-stream reader whose critical sections will synchronize on the given object. |

> method

| abstract void | close()                             | 它关闭流并释放与其关联的所有系统资源。 |
| ------------- | ----------------------------------- | -------------------------------------- |
| void          | mark(int readAheadLimit)            | 它标记了流中的当前位置。               |
| boolean       | markSupported()                     | 它告诉此流是否支持mark（）操作。       |
| int           | read()                              | 它读取一个字符。                       |
| int           | read(char[] cbuf)                   | 它将字符读入数组。                     |
| abstract int  | read(char[] cbuf, int off, int len) | 它将字符读入数组的一部分。             |
| int           | read(CharBuffer target)             | 尝试将字符读入指定的字符缓冲区。       |
| boolean       | ready()                             | 诉此流是否已准备好被读取。             |
| void          | reset()                             | 重置流。                               |
| long          | skip(long n)                        | 跳过字符。                             |



> example--01

```java
package java8.io;

import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.IOException;

/**
 * @Author: xiaobo
 * @Date: 2020/11/11 0:37
 */
public class ReaderEx {
    public static void main(String[] args) {
        try {
            FileReader fileReader = new FileReader("D://xiaobo//a.txt");
            int i  ;
            while ( ( i= fileReader.read()) != -1){
                System.out.print((char)i);
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        }catch (IOException ioex){
            ioex.printStackTrace();
        }

    }
}
// console
xiaobo777
```



### BufferedWriter

> Java BufferedWriter类用于为Writer实例提供缓冲。 它使性能快速。 它继承了Writer类。 缓冲字符用于有效地写入单个数组，字符和字符串。
>
> BufferedWriter不会一次将一个字符写入网络或磁盘，而是一次写入一个更大的块。 这通常要快得多，特别是对于磁盘访问和更大的数据量而言。



> 类声明

```
public class BufferedWriter extends Writer  
```

> 构造函数

| BufferedWriter(Writer wrt)           | It is used to create a buffered character output stream that uses the default size for an output buffer. |
| ------------------------------------ | ------------------------------------------------------------ |
| BufferedWriter(Writer wrt, int size) | It is used to create a buffered character output stream that uses the specified size for an output buffer. |

> method

| void newLine()                            | 通过编写行分隔符来添加新行。                             |
| ----------------------------------------- | -------------------------------------------------------- |
| void write(int c)                         | It is used to write a single character.                  |
| void write(char[] cbuf, int off, int len) | It is used to write a portion of an array of characters. |
| void write(String s, int off, int len)    | It is used to write a portion of a string.               |
| void flush()                              | It is used to flushes the input stream.                  |
| void close()                              | It is used to closes the input stream                    |

> example--01

```java
public class BufferedWriterExample {  
public static void main(String[] args) throws Exception {     
    FileWriter writer = new FileWriter("D:\\xiaobo.txt");  
    BufferedWriter buffer = new BufferedWriter(writer);  
    buffer.write("Welcome");  
    buffer.close();  
    System.out.println("Success");  
    }  
}  
```



### BufferedReader

> Java BufferedReader类java.io.BufferedReader为Java Reader实例提供缓冲。 缓冲可以大大提高IO的速度。 Java BufferedReader不会一次从底层Reader读取一个字符，而是一次读取一个更大的块（数组）。 这通常要快得多，尤其是对于磁盘访问和更大的数据量而言。



> Java BufferedReader与BufferedInputStream相似，但是它们并不完全相同。 BufferedReader和BufferedInputStream之间的主要区别是BufferedReader读取字符（文本），而BufferedInputStream读取原始字节。



> 类声明
>
> - Reader
>   - BufferedReader

```
public class BufferedReader extends Reader  
```

> 构造函数

| BufferedReader(Reader rd)           | 创建使用默认大小的输入缓冲区的缓冲字符输入流。 |
| ----------------------------------- | ---------------------------------------------- |
| BufferedReader(Reader rd, int size) | 创建使用指定大小的输入缓冲区的缓冲字符输入流。 |

> method

| int read()                              | 读取单个字符。                                               |
| --------------------------------------- | ------------------------------------------------------------ |
| int read(char[] cbuf, int off, int len) | 将字符读入数组的一部分。                                     |
| boolean markSupported()                 | 测试标记和重置方法的输入流支持。                             |
| String readLine()                       | 读取一行文本。                                               |
| boolean ready()                         | It is used to test whether the input stream is ready to be read. |
| long skip(long n)                       | It is used for skipping the characters.                      |
| void reset()                            | 将流重新定位在此输入流上最后一次调用mark方法的位置。重置流   |
| void mark(int readAheadLimit)           | It is used for marking the present position in a stream.     |
| void close()                            | It closes the input stream and releases any of the system resources associated with the stream. |

> example--01

```java
public class BufferedReaderExample {  
    public static void main(String args[])throws Exception{    
          FileReader fr=new FileReader("D:\\testout.txt");    
          BufferedReader br=new BufferedReader(fr);    
  
          int i;    
          while((i=br.read())!=-1){  
          System.out.print((char)i);  
          }  
          br.close();    
          fr.close();    
    }    
}    
```



### InputStreamReader

> InputStreamReader是从字节流到字符流的桥梁：它读取字节，并使用指定的字符集将它们解码为字符。 它使用的字符集可以通过名称指定，也可以明确指定，或者可以接受平台的默认字符集。
>
> Java InputStreamReader会将InputStream的字节解释为文本而不是数字数据。 因此，Java InputStreamReader类是Java Reader类的子类。Java InputStreamReader通常用于从文件（或网络连接）中读取字符，其中字节表示文本



### OutputStreamWriter





## 5.流式IORandomAccessFile