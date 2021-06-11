[TOC]

# 1.Java线程间的通信



## 介绍



> 线程通信的目的是使线程能够相互发送信号。 另外，线程通信使线程能够等待来自其他线程的信号。允许同步线程相互通信。





## 使用



### 等待/通知



> Java多线程的等待/通知机制是基于`Object`类的`wait()`方法和`notify()`, `notifyAll()`方法来实现的。





**wait():**

​		使当前线程释放锁，并等待直到另一个线程为此对象调用notify（）方法或notifyAll（）方法，或者经过指定的时间。

| public final void wait()throws InterruptedException          | 等待直到对象被通知。 |
| ------------------------------------------------------------ | -------------------- |
| public final void wait(long timeout)throws InterruptedException | 等待指定的时间。     |

​		注意：当前线程必须拥有此对象的监视器（`holdLock()`），只能从同步方法中调用它(要先得到锁然后等待然后释放锁)，否则它将引发异常。

​		比如 线程A，线程B，A得到锁然后等待释放锁，线程B得到锁开始执行，在某一时刻通过notify()或notifyAll()通知线程A进入等待状态，此时线程B并没有释放锁，除非线程B释放锁或者执行完毕





**notify():**

​		唤醒正在此对象的监视器上等待的单个线程。 如果有任何线程在该对象上等待，则选择其中一个唤醒。 选择是任意的，并且可以根据实现情况进行选择。

```java
public final void notify()
```

**notifyAll():**

​		唤醒正在此对象的监视器上等待的所有线程。

```java
public final void notifyAll()
```



**图解：**

![image-20201117223010832](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201117223010832.png)



==因为锁和对象有关，wait() notify() notifyAll()都是类方法==

| wait()                                | sleep()                      |
| :------------------------------------ | :--------------------------- |
| wait()方法释放锁                      | sleep()方法不释放锁。        |
| 是Object类的方法                      | 是Thread类的方法             |
| 非静态方法                            | 静态方法                     |
| notify（）或notifyAll（）方法进行通知 | 指定的时间后，方法执行完成。 |

wait可以指定时间，也可以不指定；而sleep必须指定时间。

wait释放cpu资源，同时释放锁；sleep释放cpu资源，但是不释放锁，所以易死锁。

wait必须放在同步块或同步方法中，而sleep可以再任意位置



> example--01

```java
package java8.multithreading.inter;

/**
 * @Author: xiaobo
 * @Date: 2020/11/17 23:01
 */
public class InterThread02 {

    private static Object lock = new Object();

    static class A implements Runnable {
        @Override
        public void run() {
            synchronized (lock) {
                for (int i = 0; i < 5; i++) {
                    System.out.println(Thread.currentThread().getName() + "==" + i);
                    try {
                        lock.notify();// 通知等待的线程
                        lock.wait();// 等待释放锁
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                lock.notify();
            }
        }
    }

    private static class B implements Runnable {
        @Override
        public void run() {
            synchronized (lock) {
                for (int i = 0; i < 5; i++) {
                    System.out.println(Thread.currentThread().getName() + "==" + i);
                    try {
                        lock.notify();// 通知等待的其他线程
                        lock.wait();// 等待 释放锁 等待通知
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    lock.notify();
                }
            }
        }
    }

    public static void main(String[] args) throws  InterruptedException {
        new Thread(new A()).start();
        Thread.sleep(1000);
        new Thread(new B()).start();

    }
}
//
Thread-0==0
Thread-1==0
Thread-0==1
Thread-1==1
Thread-0==2
Thread-1==2
Thread-0==3
Thread-1==3
Thread-0==4
Thread-1==4
```

==等待通知是同一个对象的锁，如果是不同的对象这个机制就不生效==





### 锁和互斥同步



> 对象锁：java世界中 锁基于对象
>
> 一个锁同一时刻只能被一个线程拥有



​		Java 提供了两种锁机制来控制多个线程对共享资源的互斥访问，第一个是 JVM 实现的 synchronized，而另一个是 JDK 实现的 ReentrantLock。





> example--01
>
> 不加锁 线程A和线程B各自独立工作

```java
package java8.multithreading.inter;

/**
 * @Author: xiaobo
 * @Date: 2020/11/18 0:03
 */
public class SynThread01 {

    static class A implements Runnable {
        @Override
        public void run() {
            for (int i = 0; i < 10; i++) {
                System.out.println(Thread.currentThread().getName() + "==" + i);
            }
        }
    }
    private static class B implements Runnable {
        @Override
        public void run() {
                for (int i = 0; i < 10; i++) {
                    System.out.println(Thread.currentThread().getName() + "==" + i);
                }
            }
        }
    public static void main(String[] args)  throws  InterruptedException{
        new Thread(new A()).start();
     //   Thread.sleep(1000);
        new Thread(new java8.multithreading.inter.SynThread01.B()).start();
    }
}
//

...
    Thread-0==3
Thread-0==4
Thread-0==5
Thread-1==0
Thread-1==1
Thread-1==2
Thread-1==3
Thread-1==4
    ...
```



> example--2
>
> 加锁 先执行A再执行B

```java
package java8.multithreading.inter;

/**
 * @Author: xiaobo
 * @Date: 2020/11/18 0:03
 */
public class SynThread01 {
    private static Object lock = new Object();

    static class A implements Runnable {
        @Override
        public void run() {
            synchronized (lock) {
                for (int i = 0; i < 10; i++) {
                    System.out.println(Thread.currentThread().getName() + "==" + i);
                }
            }
        }
    }

    private static class B implements Runnable {
        @Override
        public void run() {
            // 锁代码块  lock对象
            synchronized (lock) {
                for (int i = 0; i < 10; i++) {
                    System.out.println(Thread.currentThread().getName() + "==" + i);
                }
            }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        new Thread(new A()).start();
        /**
        
        */
          Thread.sleep(1000);
        new Thread(new java8.multithreading.inter.SynThread01.B()).start();
    }
}
```

==主线程里使用sleep方法睡眠了10毫秒，是为了防止线程B先得到锁。因为如果同时start，线程A和线程B都是出于就绪状态，操作系统可能会先让B运行。这样就会先输出B的内容，然后B执行完成之后自动释放锁，线程A再执行。==



### 信号量模型



> ```java
> public class Semaphore implements java.io.Serializable
> ```
>
> JDK提供了一个类似于“信号量”功能的类



==volitile==关键字能够保证内存的可见性，如果用volitile关键字声明了一个变量，在一个线程里面改变了这个变量的值，那其它线程是立马可见更改后的值的。



> example--01
>
> 

```java
package java8.multithreading.inter;

/**
 * @Author: xiaobo
 * @Date: 2020/11/18 0:21
 */
public class Volatile01 {
    private static volatile  int flag = 0;

  private   static class A implements Runnable {
        @Override
        public void run() {
            while (flag<5) {
                synchronized (this){
                    System.out.println("A:"+flag);
                    flag= flag+1;

                }
            }
        }
    }

    private static class B implements Runnable {
        @Override
        public void run() {
            while (flag<5) {
                synchronized (this){
                    System.out.println("B:"+flag);
                     flag= flag+1;

                }
            }
        }
    }

    public static void main(String[] args) throws InterruptedException{
        new Thread(new A()).start();
        new Thread(new B()).start();
    }

}
//
A:0
A:1
A:2
B:2
A:3
B:4

```

==`volatile`变量需要进行原子操作==

​		**多个线程（超过2个）需要相互合作，我们用简单的“锁”和“等待通知机制”就不那么方便了。这个时候就可以用到信号量。**

### 通道

> 管道是基于“管道流”的通信方式。JDK提供了`PipedWriter`、 `PipedReader`、 `PipedOutputStream`、 `PipedInputStream`。其中，前面两个是基于字符的，后面两个是基于字节流的。
>
> ==管道流只能实现单向发送，如果要两个线程之间互通讯，则需要两个管道流*==

```java
package java8.multithreading.inter;

import com.sun.corba.se.pept.transport.ReaderThread;

import java.io.IOException;
import java.io.PipedReader;
import java.io.PipedWriter;

/**
 * @Author: xiaobo
 * @Date: 2020/11/18 0:32
 */
public class Pipe {
	
    // 消费者 ---关联生产者（输出流）
    private static class ReaderThread implements Runnable {
        private PipedReader readerThread;
        public ReaderThread(PipedReader readerThread) {
            this.readerThread = readerThread;
        }
        @Override
        public void run() {
            int flag  = 0;

                try {
                    while ((flag = readerThread.read())!= -1){
                        System.out.println("consumer" + (char)flag);
                    }
                } catch (IOException e) {
                    e.printStackTrace();

        }}
    }
    // 生产者--  关联消费者（输入流）
    private static class WriterThread implements Runnable {
        private PipedWriter writer;
        public WriterThread(PipedWriter writer) {
            this.writer = writer;
        }

        @Override
        public void run() {
                try {
                    writer.write("w");
                } catch (IOException e) {
                    e.printStackTrace();
                }finally {
                    try {
                        writer.close();
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
            }

        }

    public static void main(String[] args)  throws  InterruptedException{
        // 创建 Pipe 输入流 
        PipedReader reader = new PipedReader();
        // 创建 pipe 输出流
        PipedWriter writer = new PipedWriter();
        try {
            writer.connect(reader);//  要连接，才能通信
        } catch (IOException e) {
            e.printStackTrace();
        }
        new Thread(new ReaderThread(reader)).start();
         Thread.sleep(1000);
        new Thread(new WriterThread(writer)).start();
    }
}
//
consumerw
```



### join方法

> 作用是让当前线程陷入“等待”状态，等join的这个线程执行完成后，再继续执行当前线程。



==有时候，主线程创建并启动了子线程，如果子线程中需要进行大量的耗时运算，主线程往往将早于子线程结束之前结束。== 

如果主线程想等待子线程执行完毕后，获得子线程中的处理完的某个数据，就要用到join方法了。

所以前面 main线程 sleep 一段时间 然后执行后面的



> example--1

```java
package java8.multithreading.inter;

/**
 * @Author: xiaobo
 * @Date: 2020/11/18 1:16
 */
public class Join01 {

    static class  A implements  Runnable{
        @Override
        public void run() {
            try {
                System.out.println("子线程");
                Thread.sleep(1000L);
                System.out.println("1s 以后 子线程");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    public static void main(String[] args) throws InterruptedException{
       Thread thread =new Thread(new A());
thread.start();
thread.join();
        System.out.println("main");
    }
}
//  + join
子线程
1s 以后 子线程
main

// 没 join

main
子线程
1s 以后 子线程
```

​		join()方法有两个重载方法，一个是join(long)， 一个是join(long, int)。join()方法及其重载方法底层都是利用了wait(long)这个方法。





### 类ThreadLocal

> ThreadLocal是一个本地线程副本变量工具类。内部是一个**弱引用**的Map来维护。



==ThreadLocal让每个线程有自己”独立“的变量，线程之间互不影响。它为每个线程都创建一个**副本**，每个线程可以访问自己内部的副本变量。==

### 类InheritableThreadLocal

> InheritableThreadLocal类与ThreadLocal类稍有不同，Inheritable是继承的意思。它不仅仅是当前线程可以存取副本值，而且它的子线程也可以存取这个副本值。

### sleep方法



> sleep方法是Thread类的一个静态方法。它的作用是让当前线程睡眠一段时间。它有这样两个方法：
>
> - Thread.sleep(long)
> - Thread.sleep(long, int)



==sleep方法是不会释放当前的锁的，而wait方法会==



## 总结

