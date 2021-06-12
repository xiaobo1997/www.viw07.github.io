[TOC]





# 1.synchronized锁机制-同步

主要参考：`java多线程核心编程技术 第2版`

![image-20201122010837417](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201122010837417.png)

## 介绍

> 首先 在java世界中 多线程的锁都是基于对象
>
> Java中的同步是控制多个线程对任何共享资源的访问的能力。
>
> Java同步是更好的选择，我们只允许一个线程访问共享资源。
>
> 只有共享资源进行读写才有必要同步，否则没必要同步



> synchronized关键字也是同步的意思,
>
> 有三个保障作用：1.原子性。2.有序性。3.可见性
>
> synchronized有三个用法

```java
//实例方法上  锁 当前实例 
public  synchronized void methodA{
    //
}

//静态方法上 锁 当前class对象
public static synchronized void methodB{
    //
}
// 代码块上，锁括号对象
private static   Object o = new Object();
public void methodC(){
  synchronized(o){
      // 
  }
}
```

> 说一下Class对象
>
> class对象：每一个类都有一个Class对象，在java世界中，创建一个类，就会创建一个对应的class类的class对象。
>
> Class类在java.lang 包下
>
> 参考：`https://blog.csdn.net/javazejian/article/details/70768369`

![参考](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201119141318574.png)





##  synchronized 深入



> java对象头：对象的锁存放位置
>
> 深入学习参考： `深入理解Java虚拟机第3版` 

每一个对象都有对象头，在Hot Spot虚拟机中，对象在内存存储的分布

​	![image-20201120224809231](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201120224809231.png)

`参考https://www.cnblogs.com/yewy/p/13584915.html`

> Mark Word的格式，在不同状态的存储格式

![image-20201120224420798](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201120224420798.png)

当对象状态为偏向锁，`Mark Word`存储的是偏向的线程ID；当状态为轻量级锁时，`Mark Word`存储的是指向线程栈中`Lock Record`的指针；当状态为重量级锁时，`Mark Word`为指向堆中的monitor对象的指针。

> monitor机制：代码案例在下，字节码的原理中
>
> 任意一个对象都拥有自己的监视器 monitor 执行方法的线程必须先获取该对象的监视器才能进入同步块和同步方法，如果没有获取到监视器的线程将会被阻塞在同步块和同步方法的入口处，进入到BLOCKED状态

通过查看字节码文件，可以看到 

方法级的同步是隐式的。同步方法的常量池中会有一个`ACC_SYNCHRONIZED`标志。

而同步代码块时添加了 `monitorenter`和 `monitorexit` 指令：

- 1.使用synchronized同步，必须获取对象的监视器 monitor，
- 2.只有获取 monitor 才能继续执行，获取的过程称之为 `互斥`，而同一个时刻只有一个线程拥有 monitor
- 3.每个对象拥有一个计数器，当线程获取该对象锁后，计数器就会+1，释放锁后就会将计数器-1

![image-20201120231144625](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201120231144625.png)

​		**`ACC_SYNCHRONIZED`和`monitorenter`、`monitorexit`都是基于Monitor实现的，Java虚拟机(HotSpot)中，Monitor是基于C++实现的**

> monitor<---->synchronized锁的重入性

==同一个得到锁的线程 在执行锁程中 不需要重新再获取锁==

在 锁程中 的methodB 执行时 不需要重新去获取锁

```java
public class A{
	public static void methodA(){}
	public void methodB(){
		synchronized(A.class){
		methodA();
		}
	}
}
```



> synchronized的happens-before关系  
>
> 临界区和竞争
>
> 参考大神书 `java并发编程的艺术`

​		程序员对内存模型的使用。程序员希望内存模型易于理解、易于编程。程序员希望基于 一个强内存模型来编写代码。 ·编译器和处理器对内存模型的实现。编译器和处理器希望内存模型对它们的束缚越少越 好，这样它们就可以做尽可能多的优化来提高性能。编译器和处理器希望实现一个弱内存模 型。 由于这两个因素互相矛盾，所以JSR-133专家组在设计JMM时的核心目标就是找到一个 好的平衡点：一方面，要为程序员提供足够强的内存可见性保证；另一方面，对编译器和处理 器的限制要尽可能地放松。

==程序员只要遵循happens-before规则，那他写的程序就能保证在JMM中具有强的内存可见性。==

​		happens-before的定义：

- 1.如果一个操作happens-before另一个操作，那么第一个操作的执行结果将对第二个操作 

可见，而且第一个操作的执行顺序排在第二个操作之前。 

- 2.两个操作之间存在happens-before关系，并不意味着Java平台的具体实现必须要按照 

happens-before关系指定的顺序来执行。如果重排序之后的执行结果，与按happens-before关系 

来执行的结果一致，那么这种重排序并不非法（也就是说，JMM允许这种重排序）。 



![image-20201120234742689](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201120234742689.png)



临界区：**当两个线程竞争同一资源时，如果对资源的访问顺序敏感，就称存在竞态条件。导致竞态条件发生的代码区称作临界区。**

对上图的总结：**根据定义：如果A happens-before B，则A的执行结果对B可见，并且A的执行顺序先于B。所以被synchronized修饰的临界区中， A进入临界区的操作 happens-before B进入临界区操作，这样对变量的修改都是在自己的工作内存修改完 写入主内存，这样其他线程读 都是可见 ，大概是这个意思吧**





==总结：如果一个操作A happens-before 操作B，那么操作A的结果是对操作B是可见的，就算这两个操作不在一个线程==



> synchronized与可见性
>
> 参考 `http://www.hollischuang.com/archives/2550`

​		被`synchronized`修饰的代码，在开始执行时会加锁，执行完成后会进行解锁。而为了保证可见性，有一条规则是这样的：对一个变量解锁之前，必须先把此变量同步回主存中。解锁后，后面线程就可以访问到被修改后的值。



> synchronized的内存语义

对共享变量的每一次操作都会刷新写入主内存中,

整个过程：

- 1.**线程A得到锁，进入临界区 对共享变量进行操作**
- 2**.线程A释放锁，并把操作后的共享变量写入主内存**
- 3.**线程B得到锁，从主内存获取共享变量的值**
- 4.**线程B 得到共享变量的值 并拷贝到自己的工作内存**

​		线程A的执行结果（i=2）对线程B是可见的，实现原理为：释放锁的时候会将值刷新到主内存中，其他线程获取锁时会强制从主内存中获取最新的值。

![image-20201121012607606](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201121012607606.png)



> synchronized(this),synchronized,synchronized(非this对象 o) 

- synchronized(this):

  - 1.对其他同步方法和同步代码块起同步作用，效果是按顺序执行
  - 2.同一时间只能有一个线程可以执行代码块中方法

- synchronized

  - 1.对其他同步方法和同步代码块起同步作用，效果是按顺序执行
  - 2.同一时间只能有一个线程可以执行同步方法中的代码，

- synchronized(非this 对象 o )：支持将容易对象做为锁（大多数是实例变量和方法参数）

  - 1.同一时间只有一个线程可以执行synchonized(非this 对象 o) 同步代码块的代码

  - 2.synchronized(不是this对象 o)与其他同步方法是异步的，相当于两把锁，不会和 锁this 同步方法去竞争锁

  - 3.提高了性能，因为对一个对象中有多个同步代码块时 他们是顺序执行的，

  - 4.synchronized(非 thix对象 x) 持有不同对象的锁 是异步的

    - ```java
      //=============
      public class A{
      
      public  void methodA{
      	 String str = new String;
      	synchronized(str){ .... }
      }
      }
      // =============
      ...线程A  线程B.....
      // =============
       A a = new A();
       new Thread( new ThreadA(a)).start;
       new Thread( new ThreadB(a)).start;
      //
      执行结果是异步的
      ```

  - 5.synchronized(非 this 对象x ) 是将 对象x 作为 监视器 monitor

  - 6.多个线程同时执行 synchronized(o){ ....} 的同步代码块时 是同步的

  - 7.当其他线程 执行o对象中的 synchronized 同步方法时  是同步的

  - 8.其他线程执行 o对象方法 里面 的  synchronized(this)是同步的

    - ```java
      public class A{
      public void methodA(){
      	String str = new String();
      	synchronized(str){
      	 .....
      	 methodB();
      	}
      }
      public synchronized void methodB(){....}
      }
      ```



> class对象的单例与sychronzied，上面有说过。

每一个 类对应一个 Class对象实例是一个 ，是单例的。在内存中存在一份Class类对象。



> String常量池和synchronized的关系

**同步代码块时不使用string作为锁对象**

如果是string的话 因为JVM会放入常量池，造成 不同线程会一直持有相同的锁，其他 线程就不能执行 会死循环

```java
public void methodA(Stirng param){
	synchronized(param){ ...}
}
```

如果使用就使用 new String() 创建不同的字符串对象

```java
public void methodA(Object object){
    synchronized(object){ ...}
} 
```





### synchronized在字节码中的原理



> 同步方法 

```java
package java8.multithreading.syn;

/**
 * @Author: xiaobo
 * @Date: 2020/11/20 0:51
 */
public class Synchronized02 {
    synchronized  public  static  void  methodA(){}
    public static void main(String[] args) {
        methodA();
    }
}

```

```shell
E:\eclipse-workspace\MyJava8Stu\src\java8\multithreading\syn>javac Synchronized02.java

E:\eclipse-workspace\MyJava8Stu\src\java8\multithreading\syn>javap -c -v Synchronized02.class


Classfile /E:/eclipse-workspace/MyJava8Stu/src/java8/multithreading/syn/Synchronized02.class
  Last modified 2020-11-20; size 364 bytes
  MD5 checksum 181bc2976526fd289cea38c78752fe02
  Compiled from "Synchronized02.java"
public class java8.multithreading.syn.Synchronized02
  minor version: 0
  major version: 52
  flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
   #1 = Methodref          #4.#14         // java/lang/Object."<init>":()V
   #2 = Methodref          #3.#15         // java8/multithreading/syn/Synchronized02.methodA:()V
   #3 = Class              #16            // java8/multithreading/syn/Synchronized02
   #4 = Class              #17            // java/lang/Object
   #5 = Utf8               <init>
   #6 = Utf8               ()V
   #7 = Utf8               Code
   #8 = Utf8               LineNumberTable
   #9 = Utf8               methodA
  #10 = Utf8               main
  #11 = Utf8               ([Ljava/lang/String;)V
  #12 = Utf8               SourceFile
  #13 = Utf8               Synchronized02.java
  #14 = NameAndType        #5:#6          // "<init>":()V
  #15 = NameAndType        #9:#6          // methodA:()V
  #16 = Utf8               java8/multithreading/syn/Synchronized02
  #17 = Utf8               java/lang/Object
{
  public java8.multithreading.syn.Synchronized02();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=1, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: return
      LineNumberTable:
        line 7: 0

  public static synchronized void methodA();
    descriptor: ()V
    flags: ACC_PUBLIC, ACC_STATIC, ACC_SYNCHRONIZED
    Code:
      stack=0, locals=0, args_size=0
         0: return
      LineNumberTable:
        line 8: 0

  public static void main(java.lang.String[]);
    descriptor: ([Ljava/lang/String;)V
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
      stack=0, locals=1, args_size=1
         0: invokestatic  #2                  // Method methodA:()V
         3: return
      LineNumberTable:
        line 10: 0
        line 11: 3
}
SourceFile: "Synchronized02.java"

```

==通过反编译的字节码可以看到 在methodA上有一个ACC_SYNCHRONIZED 标志 说明方法是同步的==



> 同步代码块

```javaj
package java8.multithreading.syn;

/**
 * @Author: xiaobo
 * @Date: 2020/11/20 0:51
 */
public class Synchronized02 {
    public void methodA() {
        synchronized (this) {
            int i = 1;
        }
    }

    public static void main(String[] args) {
        Synchronized02 synchronized02 = new Synchronized02();
        synchronized02.methodA();
    }
}

```

```shell
E:\eclipse-workspace\MyJava8Stu\src\java8\multithreading\syn>javac Synchronized02.java

E:\eclipse-workspace\MyJava8Stu\src\java8\multithreading\syn>javap -c -v Synchronized02.class
Classfile /E:/eclipse-workspace/MyJava8Stu/src/java8/multithreading/syn/Synchronized02.class
  Last modified 2020-11-20; size 500 bytes
  MD5 checksum 7ac5244898b14eb157e7024767fdb9c4
  Compiled from "Synchronized02.java"
public class java8.multithreading.syn.Synchronized02
  minor version: 0
  major version: 52
  flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
   #1 = Methodref          #5.#19         // java/lang/Object."<init>":()V
   #2 = Class              #20            // java8/multithreading/syn/Synchronized02
   #3 = Methodref          #2.#19         // java8/multithreading/syn/Synchronized02."<init>":()V
   #4 = Methodref          #2.#21         // java8/multithreading/syn/Synchronized02.methodA:()V
   #5 = Class              #22            // java/lang/Object
   #6 = Utf8               <init>
   #7 = Utf8               ()V
   #8 = Utf8               Code
   #9 = Utf8               LineNumberTable
  #10 = Utf8               methodA
  #11 = Utf8               StackMapTable
  #12 = Class              #20            // java8/multithreading/syn/Synchronized02
  #13 = Class              #22            // java/lang/Object
  #14 = Class              #23            // java/lang/Throwable
  #15 = Utf8               main
  #16 = Utf8               ([Ljava/lang/String;)V
  #17 = Utf8               SourceFile
  #18 = Utf8               Synchronized02.java
  #19 = NameAndType        #6:#7          // "<init>":()V
  #20 = Utf8               java8/multithreading/syn/Synchronized02
  #21 = NameAndType        #10:#7         // methodA:()V
  #22 = Utf8               java/lang/Object
  #23 = Utf8               java/lang/Throwable
{
  public java8.multithreading.syn.Synchronized02();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=1, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: return
      LineNumberTable:
        line 7: 0

  public void methodA();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=2, locals=4, args_size=1
         0: aload_0
         1: dup
         2: astore_1
         3: monitorenter
         4: iconst_1
         5: istore_2
         6: aload_1
         7: monitorexit
         8: goto          16
        11: astore_3
        12: aload_1
        13: monitorexit
        14: aload_3
        15: athrow
        16: return
      Exception table:
         from    to  target type
             4     8    11   any
            11    14    11   any
      LineNumberTable:
        line 9: 0
        line 10: 4
        line 11: 6
        line 12: 16
      StackMapTable: number_of_entries = 2
        frame_type = 255 /* full_frame */
          offset_delta = 11
          locals = [ class java8/multithreading/syn/Synchronized02, class java/lang/Object ]
          stack = [ class java/lang/Throwable ]
        frame_type = 250 /* chop */
          offset_delta = 4

  public static void main(java.lang.String[]);
    descriptor: ([Ljava/lang/String;)V
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
      stack=2, locals=2, args_size=1
         0: new           #2                  // class java8/multithreading/syn/Synchronized02
         3: dup
         4: invokespecial #3                  // Method "<init>":()V
         7: astore_1
         8: aload_1
         9: invokevirtual #4                  // Method methodA:()V
        12: return
      LineNumberTable:
        line 15: 0
        line 16: 8
        line 17: 12
}
SourceFile: "Synchronized02.java"

```

==同步代码块主要是monitorenter和monitorexit指令，`monitorenter`和`monitorexit`两个指令 维护着一个加锁 释放锁的记录器  这个对象得到锁就+1，释放锁就-1  只到为0 其他线程就可以执行了==

参考对synchronized的实现原理：`http://hollischuang.gitee.io/tobetopjavaer/#/basics/concurrent-coding/synchronized?id=synchronized%e7%9a%84%e7%94%a8%e6%b3%95`



##  synchronized锁实例方法



> 非线程安全--实例变量：多个对象访问同一个对象实例变量 可能出现非线程安全。如果是方法中的变量，就不会，因为此时变量是方法内部私有，作用域是在方法内部的。





> 线程安全问题
>
> 不加锁

```java
package java8.multithreading.syn;

/**
 * @Author: xiaobo
 * @Date: 2020/11/20 0:09
 */
public class Synchronized01 {

    public void methodA(int n) {
        for (int i = 0; i < 5; i++) {
            n = n - 10;
            System.out.println(n);
            try {
                Thread.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    public static class A implements Runnable {
        Synchronized01 s;

        A(Synchronized01 s) {
            this.s = s;
        }

        @Override
        public void run() {
            s.methodA(100);
        }
    }


    public static class B implements Runnable {
        Synchronized01 s;

        B(Synchronized01 s) {
            this.s = s;
        }

        @Override
        public void run() {
            s.methodA(100);
        }
    }
    public static void main(String[] args) {
        Synchronized01 s = new Synchronized01();
        new Thread(new A(s)).start();
        new Thread(new B(s)).start();

    }

}
// 不同步，各自输出各自的
90
90
80
80
70
70
60
60
50
50

```

> 加锁

```java
package java8.multithreading.syn;

/**
 * @Author: xiaobo
 * @Date: 2020/11/20 0:09
 */
public class Synchronized01 {

   synchronized public void methodA(int n) {
        for (int i = 0; i < 5; i++) {
            n = n - 10;
            System.out.println(n);
            try {
                Thread.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    public static class A implements Runnable {
        Synchronized01 s;

        A(Synchronized01 s) {
            this.s = s;
        }

        @Override
        public void run() {
            s.methodA(100);
        }
    }


    public static class B implements Runnable {
        Synchronized01 s;

        B(Synchronized01 s) {
            this.s = s;
        }

        @Override
        public void run() {
            s.methodA(100);
        }
    }
    public static void main(String[] args) {
        Synchronized01 s = new Synchronized01();
        new Thread(new A(s)).start();
        new Thread(new B(s)).start();

    }

}
// 同步，得到锁的对象先执行，执行完毕再释放锁 ，其他等待线程随机运行（如果多个）
// 不管哪个进程调用synchronized声明的方法时就上锁，方法执行释放锁，然后下一个线程再进入，不释放锁其他线程就无法执行这个方法
90
80
70
60
50
90
80
70
60
50
```

==两个线程同时访问同一个对象的实例变量是线程安全的==



> 多个对象多个锁

```java
package java8.multithreading.syn;

/**
 * @Author: xiaobo
 * @Date: 2020/11/20 0:09
 */
public class Synchronized01 {

   synchronized public void methodA(int n) {
        for (int i = 0; i < 5; i++) {
            n = n - 10;
            System.out.println(n);
            try {
                Thread.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    public static class A implements Runnable {
        Synchronized01 s;

        A(Synchronized01 s) {
            this.s = s;
        }

        @Override
        public void run() {
            s.methodA(100);
        }
    }


    public static class B implements Runnable {
        Synchronized01 s;

        B(Synchronized01 s) {
            this.s = s;
        }

        @Override
        public void run() {
            s.methodA(100);
        }
    }
    //多个对象
    public static void main(String[] args) {
        Synchronized01 s1 = new Synchronized01();
        Synchronized01 s2 = new Synchronized01();
        new Thread(new A(s1)).start();
        new Thread(new B(s2)).start();

    }

}
//
90
90
80
80
70
70
60
60
50
50
```

==因为创建两个对象， 就创建了两个锁，而synchronized是锁对象，必须是同一个对象，如果不是同一个 那就没有线程安全问题了，因为JVM创建多个锁 而不是一个锁去竞争 上面创建了两个对象 两个实例变量 多个锁 自己的线程访问自己的实例变量，不存在竞争关系。==



> 异步执行

```java
package java8.multithreading.syn;

/**
 * @Author: xiaobo
 * @Date: 2020/11/20 0:09
 */
public class Synchronized01 {

   synchronized public void methodA(int n) {
        for (int i = 0; i < 5; i++) {
            n = n - 10;
            System.out.println(n);
            try {
                Thread.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    public void methodB(int n) {
        for (int i = 0; i < 5; i++) {
            n = n - 10;
            System.out.println(n);
            try {
                Thread.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }



    public static class A implements Runnable {
        Synchronized01 s;

        A(Synchronized01 s) {
            this.s = s;
        }

        @Override
        public void run() {
            s.methodA(200);
        }
    }


    public static class B implements Runnable {
        Synchronized01 s;

        B(Synchronized01 s) {
            this.s = s;
        }

        @Override
        public void run() {
            s.methodB(100);
        }
    }
    public static void main(String[] args) {
        Synchronized01 s1 = new Synchronized01();
        Synchronized01 s2 = new Synchronized01();
        new Thread(new A(s1)).start();
        new Thread(new B(s1)).start();

    }

}
//
190
90
80
180
70
170
60
160
50
150
```

==虽然B先拿到锁，但是A可以异步的执行methodA,完全不影响，除非在methodB上加锁==



### synchronized锁重入和锁重入继承

> synchronized锁拥有重入锁的功能，什么是重入在上面有介绍，下面代码展示



```java
package java8.multithreading.syn;

/**
 * @Author: xiaobo
 * @Date: 2020/11/21 1:34
 */
public class Synchronized03 {

    public static int i = 5;

    synchronized static void methodA() throws InterruptedException {
        System.out.println("A");
        i--;
        Thread.sleep(1000);
        System.out.println("父" + i);
        methodB();
    }

    static void methodB() {
        System.out.println("B");
        methodC();
    }

    static void methodC() {
        System.out.println("C");
    }

    public static void main(String[] args) throws InterruptedException {
        //   methodA();
    }
}

```

```java
package java8.multithreading.syn;

/**
 * @Author: xiaobo
 * @Date: 2020/11/21 1:43
 */
public class Synchronized04 extends Synchronized03 {

    static class A implements Runnable {
        Synchronized04 syn;

        public A(Synchronized04 synchronized04) {
            this.syn = synchronized04;
        }

        @Override
        public void run() {
            try {
                syn.method();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

        }
    }

    synchronized void method() throws InterruptedException {
        while (i > 0) {

            System.out.println("a");
            System.out.println("子" + i);
            i--;
            Thread.sleep(1000);
            System.out.println("子" + i);
            super.methodA();
        }
    }

    public static void main(String[] args) throws InterruptedException {
        Synchronized04 synchronized04 = new Synchronized04();
        new Thread(new A(synchronized04)).start();
    }
}
//
a
子5
子4
A
父3
B
C
a
子3
子2
A
父1
B
C
a
子1
子0
A
父-1
B
C
```

**一个线程得到对象的锁，此时需要再次获取锁，而此时没有释放锁，那是可以获取锁的**



**当线程执行的代码重新异常，会自动释放锁，后面线程继续拿锁执行，但是如果调用sleep()就不会释放锁**



### 对象继承关系时的synchronized



> 子类重写父类方法，如果不需要synchronized，则不同步

```java
public class A{
 public int = 10;
synchronized public void method(){
	i --;
}
}

public class B extends A{

 public void method(){
	i --;
}
}
// 不同步 线程不安全
```



## synchronized锁代码块





> 锁方法时的弊端

因为在锁方法时，性能比较低，其他线程都是等待状态，因此锁代码块可以异步的执行方法中没被锁的代码。





> 使用
>
> synchronized同步代码块是 其他线程必须等待得到对象锁的线程执行完这一代码块，才能执行

```java
package java8.multithreading.syn;

/**
 * @Author: xiaobo
 * @Date: 2020/11/21 16:25
 */
public class Synchronized05 {
    public static class A {
        public void methodA() {
            System.out.println("begin");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            synchronized (this) {

                System.out.println("end ");
            }
        }
    }
    public static class threadA implements Runnable {
        private A a;
        public threadA(A a) {
            this.a = a;
        }
        @Override
        public void run() {
            System.out.println("threadA:" + System.currentTimeMillis());
            a.methodA();
            System.out.println("threadA:" + System.currentTimeMillis());
        }
    }

    public static class threadB implements Runnable {
        private A a;
        public threadB(A a) {
            this.a = a;
        }
        @Override
        public void run() {
            System.out.println("threadB:" + System.currentTimeMillis());
            a.methodA();
            System.out.println("threadB:" + System.currentTimeMillis());
        }
    }
    public static void main(String[] args) throws InterruptedException {
        A a = new A();
        new Thread(new threadA(a)).start();
        Thread.sleep(1000);
        new Thread(new threadB(a)).start();
    }
}

//
threadA:1605959214475
begin
threadB:1605959215475
begin
end 
threadA:1605959215476
end 
threadB:1605959216476
```

==当一个线程访问一个对象的同步代码块时，其他线程仍然可以访问这个对象的非同步代码块，这样就相当于同步实例方法提高了性能==





> synchronized(this),synchronized
>
> 都是锁当前对象
>
> synchronized(this)是把当前代码块所在对象做为锁
>
> synchronized 是把当前方法所在对象做为锁

- ==synchronized(this)==

  - 是会持有当前对象的锁，是同步的。在这个代码块中就是同步执行的。不在这个代码块中就是异步执行的。

  - 当一个线程访问同步代码块时，其他线程访问同一对象的其他同步代码块将BLOCKED，因为他们是同一个监视器monitor。

  - ```java
    package java8.multithreading.syn;
    
    /**
     * @Author: xiaobo
     * @Date: 2020/11/21 20:16
     */
    public class Synchronized06 {
        static public class A {
            public void methodA() {
                synchronized (this) {
                    System.out.println("A:" + Thread.currentThread().getName());
                }
            }
    
            public void methodB() {
                synchronized (this) {
                    System.out.println("B:" + Thread.currentThread().getName());
                }
            }
        }
    
        public static class threadA implements Runnable {
            private A a;
    
            public threadA(A a) {
                this.a = a;
            }
    
            @Override
            public void run() {
                a.methodA();
            }
        }
    
        public static class threadB implements Runnable {
            private A a;
    
            public threadB(A a) {
                this.a = a;
            }
            @Override
            public void run() {
                a.methodB();
            }
        }
    
        public static void main(String[] args) throws InterruptedException {
            A a = new A();
            Thread threadA = new Thread(new threadA(a));
            threadA.setName("a");
            threadA.start();
            Thread.sleep(1000);
            Thread threadB = new Thread(new threadB(a));
            threadB.setName("b");
            threadB.start();
        }
    }
    //
    A:a
    B:b
    ```



> 同步代码块解决无限等待
>
> synchronized(非this对象  o)

```java
public class A{
 	synchronized void m1{
		while(true){...}
	}
	synchronized void m2{
	 ....
	}
}
// ========

public class A{
    Object o1 = new Object(); Object o1 = new Object();
 	 void m1{
         synchronized(o1){         
		while(true){...}
         } 
	}
	 void m2{
    synchronized(o2){         
		...
	}
}

```



## synchronized锁static



> 在静态方法static上使用synchronized 关键字时，锁的是当前静态方法所在类的 Class类对象(单例)
>
> ==锁静态方法时对所有对象实例起作用==
>
> ==锁静态代码块时对所有对象实例起作用==
>
> ```java
> public class A{
> ...
> }
> 
> public class Service{
> 	public void method(){
> 	 synchronized(A.class){...}
> 	}
> }
> ```
>
> 



```java
package java8.multithreading.syn;

/**
 * @Author: xiaobo
 * @Date: 2020/11/21 21:54
 */
public class SynA01 {
    synchronized static  void methodA(){
        System.out.println("a begin");
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("a end");
    }
    synchronized static  void methodB() {
        System.out.println("b begin");
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("b end");
    }
     static  void methodC() {
         System.out.println("c begin");
         try {
             Thread.sleep(1000);
         } catch (InterruptedException e) {
             e.printStackTrace();
         }
         System.out.println("c end");
    }
}
//
```

```java
package java8.multithreading.syn;

/**
 * @Author: xiaobo
 * @Date: 2020/11/21 21:51
 */
public class Synchronized08 {
   public static class aa implements  Runnable{
       SynA01 a01;
       public aa(SynA01 a01){
           this.a01 = a01;
       }
       @Override
       public void run() {
           a01.methodA();
       }
   }
    public static class bb implements  Runnable {
        SynA01 a01;
        public bb(SynA01 a01){
            this.a01 = a01;
        }
        @Override
        public void run() {
            a01.methodB();
        }
    }
    public static class cc implements  Runnable{
        SynA01 a01;
        public cc(SynA01 a01){
            this.a01 = a01;
        }
        @Override
        public void run() {
            a01.methodC();
        }
    }

    public static void main(String[] args) throws InterruptedException{
        SynA01 a01 = new SynA01();
        new Thread( new aa(a01) ).start();
        Thread.sleep(1000);
        new Thread( new bb(a01) ).start();
        Thread.sleep(1000);
        new Thread( new cc(a01) ).start();
    }
}
//
a begin
a end
b begin
c begin
b end
c end
```

==线程aa  bb  cc是异步的，aa bb 是同步的==

==线程aa  bb是把 类SynA01类的Class对象作为锁。cc 是把 SynA01类作为锁==



