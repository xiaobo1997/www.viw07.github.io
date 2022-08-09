[TOC]

# 序列化和反序列化



## 介绍

> 什么是序列化和反序列化

​		Java中的序列化是一种将对象状态写入字节流的机制。

​		简单一句话 序列化就是把对象转字节序列的过程，反序列化就是把 子节序列转对象的过程，比如json

​		序列化是将对象的状态信息转换为可存储或传输的形式的过程。一般是以字节码或XML格式传输。而字节码或XML编码格式可以还原为完全相等的对象。这个相反的过程称为反序列化。

![image-20201118013007545](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201118013007545.png)





> 为什么

在Java中，我们可以通过多种方式来创建对象，并且只要对象没有被回收我们都可以复用该对象。但是，我们创建出来的这些Java对象都是存在于JVM的堆内存中的。只有JVM处于运行状态的时候，这些对象才可能存在。一旦JVM停止运行，这些对象的状态也就随之而丢失了。

但是在真实的应用场景中，我们需要将这些对象持久化下来，并且能够在需要的时候把对象重新读取出来。Java的对象序列化可以帮助我们实现该功能。

对象序列化机制（object serialization）是Java语言内建的一种对象持久化方式，通过对象序列化，可以把对象的状态保存为字节数组，并且可以在有需要的时候将这个字节数组通过反序列化的方式再转换成对象。对象序列化可以很容易的在JVM中的活动对象和字节数组（流）之间进行转换。



**注意：**😉

==不会对静态变量进行序列化，因为序列化只是保存对象的状态，静态变量属于类的状态。==



## 使用



### Serializable接口





> 调用方法

​		对于序列化对象，我们调用writeObject（）方法ObjectOutputStream，对于反序列化，我们调用ObjectInputStream类的readObject（）方法。



> 条件

必须实现Serializable接口才能序列化对象。未实现此接口的类将无法使其任何状态序列化或反序列化。



#### ObjectOutputStream

> ObjectOutputStream类用于将原始数据类型和Java对象写入OutputStream。 只有支持java.io.Serializable接口的对象才能写入流。



> 构造函数

| 1) public ObjectOutputStream(OutputStream out) throws IOException {} | 创建一个ObjectOutputStream  写入指定的OutputStream。 |
| ------------------------------------------------------------ | ---------------------------------------------------- |
|                                                              |                                                      |



> method

| 1) public final void writeObject(Object obj) throws IOException {} | 将指定的对象写入ObjectOutputStream流 |
| ------------------------------------------------------------ | ------------------------------------ |
| 2) public void flush() throws IOException {}                 | 刷新当前输出流。                     |
| 3) public void close() throws IOException {}                 | 关闭当前输出流                       |



> example--01

```java
package java8.io;

import java.io.FileOutputStream;
import java.io.IOException;
import java.io.ObjectOutputStream;
import java.io.Serializable;

/**
 * @Author: xiaobo
 * @Date: 2020/11/18 12:29
 */
public class Serialization01   {

    public static class Student implements Serializable{
        public Integer id;
        public String name;

        public Student(Integer id, String name) {
            this.id = id;
            this.name = name;
        }
    }

    public static void main(String[] args) {
        try {
            Student student = new Student(1, "xiaobo");
            FileOutputStream outputStream = new FileOutputStream("D://xiaobo//a.txt");
            ObjectOutputStream objectOutputStream = new ObjectOutputStream(outputStream);
            objectOutputStream.writeObject(student);
            objectOutputStream.flush();
            objectOutputStream.close();
            System.out.println("OK!");
        }catch (IOException e){
            e.printStackTrace();
        }
    }
}
// 
OK!
    
//a.txt
    
     sr  java8.io.Serialization01$Student?禶鎠汑 L idt Ljava/lang/Integer;L namet Ljava/lang/String;xpsr java.lang.Integer鉅亣8 I valuexr java.lang.Number啲?斷?  xp   t xiaobo
```





#### ObjectInputStream



> ObjectInputStream反序列化使用ObjectOutputStream.Constructor编写的对象和原始数据。



> 构造函数

| 1) public ObjectInputStream(InputStream in) throws IOException {} | 创建一个ObjectInputStream，它从指定的InputStream读取。 |
| ------------------------------------------------------------ | ------------------------------------------------------ |
|                                                              |                                                        |



> method

| 1) public final Object readObject() throws IOException, ClassNotFoundException{} | 从输入流中读取一个对象。 |
| ------------------------------------------------------------ | ------------------------ |
| 2) public void close() throws IOException {}                 | 关闭ObjectInputStream。  |



> example--01

```java
package java8.io;

import java.io.FileInputStream;
import java.io.IOException;
import java.io.ObjectInputStream;

/**
 * @Author: xiaobo
 * @Date: 2020/11/18 12:36
 */
public class Serialization02 {

    public static void main(String[] args) {
        try {

            FileInputStream inputStream = new FileInputStream("D://xiaobo//a.txt");
            ObjectInputStream stream = new ObjectInputStream(inputStream);
            Serialization01.Student object = (Serialization01.Student) stream.readObject();
            System.out.println(object.id+"======="+object.name);
            inputStream.close();
        }catch (Exception E){
            E.printStackTrace();
        }
    }
}
//
1=======xiaobo
```



### Extenalizable接口



> java中提供了另一个序列化接口`Externalizable`
>
> Externalizable接口提供了以压缩格式将对象状态写入字节流的便利。 它不是标记界面。



>method:

- **public void writeExternal(ObjectOutput out) throws IOException**
- **public void readExternal(ObjectInput in) throws IOException**



> example--01
>
> 可以把之前的对象状态持久化下来了。

```java
package java8.io;

import java.io.*;

/**
 * @Author: xiaobo
 * @Date: 2020/11/18 19:29
 */
public class Extenalizable01 {

    public static class Student implements Externalizable {
        public Integer id;
        public String name;

        public Student(Integer id, String name) {
            this.id = id;
            this.name = name;
        }

        public Student() {
        }

        @Override
        public void writeExternal(ObjectOutput out) throws IOException {
            out.writeObject(name);
            out.writeInt(id);
        }


        @Override
        public void readExternal(ObjectInput in) throws IOException, ClassNotFoundException {
            name = (String) in.readObject();
            id = in.readInt();
        }

        @Override
        public String toString() {
            return "Student{" +
                    "id=" + id +
                    ", name='" + name + '\'' +
                    '}';
        }
    }

    public static void main(String[] args) {
        try {
            Extenalizable01.Student student = new Extenalizable01.Student(1, "xiaobo");
            FileOutputStream outputStream = new FileOutputStream("D://xiaobo//a.txt");
            ObjectOutputStream objectOutputStream = new ObjectOutputStream(outputStream);
            objectOutputStream.writeObject(student);
            objectOutputStream.flush();
            objectOutputStream.close();
            System.out.println("OK!");
        } catch (IOException e) {
            e.printStackTrace();
        }
        System.out.println("读取中...........");
        try {
            FileInputStream inputStream = new FileInputStream("D://xiaobo//a.txt");
            ObjectInputStream stream = new ObjectInputStream(inputStream);
            Extenalizable01.Student object = (Extenalizable01.Student) stream.readObject();
            System.out.println(object.id+"======="+object.name);
            inputStream.close();
        }catch (Exception E){
            E.printStackTrace();
        }
    }

}
// console
OK!
读取中...........
1=======xiaobo

// a.txt
 sr  java8.io.Extenalizable01$Student觚镲c际?  xpt xiaobow   x
```

==当使用Externalizable接口来进行序列化与反序列化的时候需要开发人员重写`writeExternal()`与`readExternal()`方法。==

==没有在这两个方法中定义序列化实现细节，所以输出的内容为空。==

==在使用Externalizable进行序列化的时候，在读取对象时，会调用被序列化类的无参构造器去创建一个新的对象，然后再将被保存对象的字段的值分别填充到新对象中。所以，实现Externalizable接口的类必须要提供一个public的无参的构造器（如果User类中没有无参数的构造函数，在运行时会抛出异常：`java.io.InvalidClassException`）。==



### Transient 关键字

> 如果您不想序列化类的任何数据成员，则可以将其标记为瞬态。
>
> Java瞬态关键字在序列化中使用。 如果您将任何数据成员定义为瞬态数据，则不会被序列化。
>
> 如果反序列化对象，则将获得瞬态变量的默认值。



> example--01

```java
class Employee implements Serializable{  
 transient int id;  
 String name;  
 public Student(int id, String name) {  
  this.id = id;  
  this.name = name;  
 }  
}  
```

**现在，将不会序列化id，因此在序列化后反序列化对象时，将不会获得id的值。 它将始终返回默认值。 在这种情况下，它将返回0，因为id的数据类型是整数。**





### SerialVersionUID



> 运行时的序列化过程将一个ID与每个Serializable类相关联，该类称为SerialVersionUID。 它用于验证序列化对象的发送者和接收者。 发送者和接收者必须相同。 要进行验证，请使用SerialVersionUID。 发送方和接收方必须具有相同的SerialVersionUID，否则，在反序列化对象时将引发InvalidClassException。 我们还可以在Serializable类中声明我们自己的SerialVersionUID。 为此，您需要创建一个字段SerialVersionUID并为其分配一个值。 它必须是带有static和final的long类型。 建议在类中显式声明serialVersionUID字段，并将其设置为私有。



```java
import java.io.Serializable;  
class Employee implements Serializable{  
 private static final long serialVersionUID=1L;  
 int id;  
 String name;  
 public Student(int id, String name) {  
  this.id = id;  
  this.name = name;  
 }  
}  
```



### 注意



> 序列化关系--（HAS-A Relationship）

如果一个类具有对另一个类的引用，则所有引用都必须是可序列化的，否则将不执行序列化过程。 在这种情况下，NotSerializableException将在运行时引发。

​		==对象中的所有对象必须可序列化。==



> 集合，数组的Java序列化

对于数组或集合，数组或集合的所有对象都必须可序列化。 如果任何对象不可序列化，则序列化将失败。