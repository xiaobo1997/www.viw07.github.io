[TOC]



![image-20201216220002471](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201216220002471.png)

图来自网上，地址在后面写出来

# 1.类加载机制



> 简单说一下什么是类加载机制

jvm虚拟机把类信息从class文件（二进制流）读取到内存中，然后包括准备，解析，赋初值等 ，然后最后一个转换成虚拟机直接使用的java类型，

**注意的是 类是在运行时第一次使用动态加载的，要不然的话 那么多信息是很占内存的，**

**这样的加载也可以说是懒加载，用的时侯才加载嘛**





## 类的生命周期和类加载的过程



> 说一下类的生命周期，类的生命周期从被使用然后加载到虚拟机内存中，然后到从虚拟机内存中卸载的过程，主要是7步

![image-20201216215216598](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201216215216598.png)

- 1.加载---第一次要使用了嘛，然后就动态加载，这个加载就是加载class这个二进制流文件，主要是通过类的全限定类名

- 2.验证---加载以后，就需要去验证字节码文件的正确性，比如说前多少字节是干嘛的 ，比如说主版本号啊什么的，符不符合class文件的规范啊，还有符号引用的验证啊，常量池的常量是不是都可以被处理的类型等等

- 3.准备---这一步主要是给类的静态变量去分配内存设置类变量初始值的阶段，静态变量在哪呢，主要是方法区，jdk8的话主要是堆了，注意了，实例变量是不会在这一步被分配赋值的，而是在跟着对象实例化的时侯分配在堆上的

  - ```java
    /** 这里注意，int被static修饰，而在准备时 还没有 指令给a赋1，
     所以a在准备时其实是 0，也就是默认的值
    */
    public static int a = 1;
    
    /** 如果还被final修饰,也就是说现在a被 static final 一起修饰，
    那么他在编译时 是会被生成constantValue属性修饰，而这个修饰可以
    让a在准备这一步  就把 1 赋给a
    那么就相当于把a直接放入常量池了，类还没有加载就已经有这个值了，
    常量池好像有java这个string
    */
    public static final a= 1;
    ```

    

- 4.解析---主要是把类中的常量池符号引用变成了直接引用，主要是针对 类和接口，字段，类方法，接口方法，方法类型，**方法句柄（这个是定位对象的）**等下，

- 5.初始化---这一步也是Java虚拟机才真正开始执行类中编写的Java程序代码的时侯，在准备阶段，基本数据类型和string已经被赋了默认值，而这一步是赋正确的值

  - 作用:对静态变量声明的初始值初始化，对静态代码块设置的初始值初始化
  - 类加载过程的最后一步，是执行类构造器 <clinit>() 方法的过程。
  - 说一下在类中的静态代码块，如果在静态代码块中给变量赋值，那么这个变量在这个静态代码块赋值后不可以访问，因为  **静态语句块中只能访问定义在静态语句块之前的变量，**







## 类加载的初始化时机



> 什么时侯初始化

明确的规定了有下面的情况时立即初始化

- 1.new 实例化类时，也就是new关键字
- 2.用到了反射包方法时，具体包我忘记了，也就是 class.forName(xxx);时，因为反射调用类时，类是要初始化的
- 3.初始化子类时，如果父类没初始化，需要先初始化父类
- 4.程序入口包含main方法时的主类
- 5.调用类的静态方法时



这些情况下的行为是对类的**主动引用，**

而不会让类初始化的叫**被动引用**



```java
//被动引用：在这里只会初始化父类，子类是不会的，因为 a 是在父中定义的

class SuperClass{
    static{
        xxx
    }
    static int  a = 1;
}

class SonClass extends superClass{
    static{
        xxx
    }
}

// 
psvm[IDEA]{
    SonClass.a;
}
```



**注意：接口初始化时 不要求父接口也全部初始化，需要才**





## 类加载器

### 类和类加载器

> 类加载器

简单说就是虚拟机把在通过类全限定类名读取class二进制字节流文件这个过程的执行代码 叫类加载器（Class loader）



> 类和类加载器的关系

类加载器会给类或接口一个唯一的标识符来标识类或接口

也就是说，一个类的唯一性是这个类和加载这个类的加载器一起确认的。

所以我们比较一个类需要在同一个加载器下比较，要不然肯定不相等



### 类加载器的体系结构

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201216231630912.png" alt="image-20201216231630912" style="zoom:67%;" />



### 加载器种类

从java虚拟机看，两种类加载器， 启动类加载器和其他的加载器，启动类加载器是大佬用C++写的，

从开发的角度看虚拟机提供了三种类加载器，但是我们还可以自定义类加载器

- 1.Bootstrap ClassLoader----这个是启动类的加载器，他主要是启动程序的，那么肯定是把 <JAVA_HOME>/lib 中的jar加载到虚拟机内存中，要不然你都无法启动
- 2.Extension ClassLoader----这个是控制类加载器， 负责加载 `<JAVA_HOME>\lib\ext` 目录中的所有类库
- 3.Application ClassLoader--- 一看名字就知道这个是 启动类的加载器，它负责加载用户类路径（classpath）上所指定的类库，开发者可以直接使用这个类加载器，如果应用程序中没有自定义过自己的类加载器，一般情况下这个就是程序中默认的类加载器

### 类加载器的特别

- 1.层级结构
- 2.代理模式
- 3.可见性
- 4.不允许卸载：类装载器可以装载一个类但是不可以卸载它，不过可以删除当前的类装载器，然后创建一个新的类装载器装载。



### 双亲委派模型

#### 是什么 

简单点说，向上判断是否有加载器，向下加载器动态加载类，



核心代码

**通过指定类的**全限定名称**，由类加载器**检测**、**装载**、**创建**并返回该类的`java.lang.Class`对象。**

```java
 protected Class<?> loadClass(String name, boolean resolve)
        throws ClassNotFoundException
    {
        synchronized (getClassLoadingLock(name)) {
            // First, check if the class has already been loaded
            Class<?> c = findLoadedClass(name);
            if (c == null) {
                long t0 = System.nanoTime();
                try {
                    if (parent != null) {
                        c = parent.loadClass(name, false);
                    } else {
                        c = findBootstrapClassOrNull(name);
                    }
                } catch (ClassNotFoundException e) {
                    // ClassNotFoundException thrown if class not found
                    // from the non-null parent class loader
                }

                if (c == null) {
                    // If still not found, then invoke findClass in order
                    // to find the class.
                    long t1 = System.nanoTime();
                    c = findClass(name);

                    // this is the defining class loader; record the stats
                    sun.misc.PerfCounter.getParentDelegationTime().addTime(t1 - t0);
                    sun.misc.PerfCounter.getFindClassTime().addElapsedTimeFrom(t1);
                    sun.misc.PerfCounter.getFindClasses().increment();
                }
            }
            if (resolve) {
                resolveClass(c);
            }
            return c;
        }
    }
```



#### 为什么

首先 Object类是顶级父类，

而双亲委派模型可以让不管你使用哪个类加载器加载Object，最后一个都是委派给最顶端的启动类加载器加载，这样是不是就保持了加载器是同一个了，如果不一致，那么多个不同的Object使用不同的类加载器，永远不会相等比较，instance判断类类型也就没什么作用了



而且还可以避免重复加载，父类加载一次该子类，等子类加载时 为什么还要加载一次呢，



#### 怎么自己设计一个自定义类加载器



> 首先继承ClassLoader,重写 findClass方法
>
> findClass主要是来寻找类的，通过全限定类名

```java
public class MyTestClassLoader extends ClassLoader {

    private String rootDir;

    public FileSystemClassLoader(String rootDir) {
        this.rootDir = rootDir;
    }

    protected Class<?> findClass(String name) throws ClassNotFoundException {
        byte[] classData = getClassData(name);
        if (classData == null) {
            throw new ClassNotFoundException();
        } else {
            return defineClass(name, classData, 0, classData.length);
        }
    }

    private byte[] getClassData(String className) {
        String path = classNameToPath(className);
        try {
            InputStream ins = new FileInputStream(path);
            ByteArrayOutputStream baos = new ByteArrayOutputStream();
            int bufferSize = 4096;
            byte[] buffer = new byte[bufferSize];
            int bytesNumRead;
            while ((bytesNumRead = ins.read(buffer)) != -1) {
                baos.write(buffer, 0, bytesNumRead);
            }
            return baos.toByteArray();
        } catch (IOException e) {
            e.printStackTrace();
        }
        return null;
    }

    private String classNameToPath(String className) {
        return rootDir + File.separatorChar
                + className.replace('.', File.separatorChar) + ".class";
    }
}
```







