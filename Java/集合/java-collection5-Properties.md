[TOC]

# Properties class in java



## 使用

> 属性对象包含作为字符串的键和值对。 java.util.Properties类是Hashtable的子类，可用于基于属性键获取属性值。 Properties类提供了从属性文件中获取数据并将数据存储到属性文件中的方法。 而且，它可以用来获取系统的属性。



**需要注意的是：**

- 1.如果从属性文件中更改了信息，则不需要重新编译：
- 2.如果从属性文件中更改了任何信息，则无需重新编译java类。 
- 3.它用于存储经常更改的信息。

> 继承树和声明

```java
public
class Properties extends Hashtable<Object,Object>
```



> 构造函数

| Properties()                    | 创建一个没有默认值的空属性列表。   |
| ------------------------------- | ---------------------------------- |
| Properties(Properties defaults) | 指定的默认值创建一个空的属性列表。 |



> method

| public void load(Reader r)                                   | 从Reader对象加载数据。                                    |
| ------------------------------------------------------------ | --------------------------------------------------------- |
| public void load(InputStream is)                             | 从InputStream对象加载数据                                 |
| public void loadFromXML(InputStream in)                      | 用于将指定输入流上XML文档表示的所有属性加载到此属性表中。 |
| public String getProperty(String key)                        | 根据键返回值。                                            |
| public String getProperty(String key, String defaultValue)   | 搜索具有指定键的属性。                                    |
| public void setProperty(String key, String value)            | 调用Hashtable的put方法。                                  |
| public void list(PrintStream out)                            | 将属性列表输出到指定的输出流。                            |
| public void list(PrintWriter out))                           | 用于将属性列表输出到指定的输出流。                        |
| public Enumeration<?> propertyNames())                       | 返回属性列表中所有键的枚举。                              |
| public Set<String> stringPropertyNames()                     | 从属性列表中返回一组键，其中键及其对应的值为字符串。      |
| public void store(Writer w, String comment)                  | 属性写入writer对象。                                      |
| public void store(OutputStream os, String comment)           | 将属性写入OutputStream对象。                              |
| public void storeToXML(OutputStream os, String comment)      | 将属性写入writer对象以生成XML文档                         |
| public void storeToXML(Writer w, String comment, String encoding) | 将属性写入Writer对象，以使用指定的编码生成XML文档。       |



> exampl--01
>
> 读取属性

![image-20201114232927071](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201114232927071.png)

```java
package java8.propertiesClass;

import java.io.File;
import java.io.FileReader;
import java.util.Iterator;
import java.util.Map;
import java.util.Properties;
import java.util.Set;

/**
 * @Author: xiaobo
 * @Date: 2020/11/14 23:26
 */
public class Properties01 {
    public static void main(String[] args) {
        File file = new File("D://xiaobo//a.properties");
        Properties properties = new Properties();
        try {
            FileReader reader = new FileReader(file);
            properties.load(reader);
        }catch (Exception o){
            o.printStackTrace();
        }
        System.out.println(properties.getProperty("user"));
        System.out.println(properties.getProperty("password"));

        // 通过System.getProperties（）方法，我们可以获得系统的所有属性
        Properties properties1 = System.getProperties();
        Set<Map.Entry<Object, Object>> entrySet = properties1.entrySet();
        Iterator<Map.Entry<Object, Object>> entryIterator = entrySet.iterator();
        while (entryIterator.hasNext()) {
            Map.Entry<Object, Object> next = entryIterator.next();
            System.out.println(next.getKey()+"="+next.getValue());
        }

    }
}
//=========

xiaobo
123
java.runtime.name=Java(TM) SE Runtime Environment
sun.boot.library.path=C:\Program Files\Java\jdk1.8.0_172\jre\bin
java.vm.version=25.172-b11
java.vm.vendor=Oracle Corporation
java.vendor.url=http://java.oracle.com/
path.separator=;
java.vm.name=Java HotSpot(TM) 64-Bit Server VM
file.encoding.pkg=sun.io
user.country=CN
user.script=
sun.java.launcher=SUN_STANDARD
sun.os.patch.level=
java.vm.specification.name=Java Virtual Machine Specification
user.dir=E:\eclipse-workspace\MyJava8Stu
java.runtime.version=1.8.0_172-b11
java.endorsed.dirs=C:\Program Files\Java\jdk1.8.0_172\jre\lib\endorsed
........
 ........
```



> example--02 写属性

```java
package java8.propertiesClass;

import java.io.File;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;
import java.util.Properties;

/**
 * @Author: xiaobo
 * @Date: 2020/11/14 23:45
 */
public class Properties02 {
    public static void main(String[] args) {
        File file = new File("D://xiaobo//b.properties");
        Properties properties = new Properties();
        if(!file.exists()){
            try {
                file.createNewFile();
                System.out.println("创建b.properties");
                FileWriter fileWriter = new FileWriter(file);
                properties.setProperty("name","xiaobo");
                properties.setProperty("blog","www.xiaobo.life");
                properties.store(fileWriter,"is xiaobo_blog");
            }catch (IOException E){
                E.printStackTrace();
            }
        }else {
            System.out.println("文件已存在");
            System.out.println("读取文件属性....");
            try {
                properties.load(new FileReader(file));
                System.out.println(properties.getProperty("name"));
                System.out.println(properties.getProperty("blog"));
            }catch (Exception E){
                E.printStackTrace();
            }
        }


    }
}
// console
文件已存在
读取文件属性....
xiaobo
www.xiaobo.life
// b.propeties
#is xiaobo_blog
#Sat Nov 14 23:54:18 CST 2020
name=xiaobo
blog=www.xiaobo.life
```

