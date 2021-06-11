[TOC]

![image-20201122164835976](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201122164835976.png)

# 1.多线程的死锁



> 死锁的概念

多个线程都在等待资源被释放，而一直都没有释放资源，线程全被阻塞



> 死锁的发生
>
> 同时满足四个条件
>
> 阻止死锁的话，只需要破坏其中一个即可。
>
> 此处破坏第四个条件即可

查看 `https://lingcoder.gitee.io/onjava8/#/book/24-Concurrent-Programming?id=%e6%ad%bb%e9%94%81`

![image-20201122164557360](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201122164557360.png)

1. 互斥条件。任务使用的资源中至少有一个不能共享的。 这里，一根筷子一次就只能被一个哲学家使用。
2. 至少有一个任务它必须持有一个资源且正在等待获取一个被当前别的任务持有的资源。也就是说，要发生死锁，哲学家必须拿着一根筷子并且等待另一根。
3. 资源不能被任务抢占， 任务必须把资源释放当作普通事件。哲学家很有礼貌，他们不会从其它哲学家那里抢筷子。
4. 必须有循环等待， 这时，一个任务等待其它任务所持有的资源， 后者又在等待另一个任务所持有的资源， 这样一直下去，知道有一个任务在等待第一个任务所持有的资源， 使得大家都被锁住。 在 `DiningPhilosophers.java` 中， 因为每个哲学家都试图先得到右边的 筷子, 然后得到左边的 筷子, 所以发生了循环等待。



> 



> 防止死锁

**Java** 编程语言中没有任何东西可以避免或打破这种死锁现象。

- 1.认真设计代码程序
- 2.避免一个线程同时获取多个锁
- 3.避免一个线程同时拥有多个资源，尽量一个线程占用一个资源
- 4.建议使用定时锁 Lock.tryLock(long timeout)来 代替内部锁 synchronized.....
- 5.对于数据库锁 加锁解锁尽量在同一个数据库中进行。

> 监测死锁

进入jdk 的bin目录

```
....>jps
```

得到线程pid

```
...>jstack -l pid
```



> 服务感知

一旦出现了 死锁 服务是可感知的，就不能再继续提供服务了。  通过dump线程来查看到底哪一个出问题。

> 死锁案例

```java
package java8.multithreading.deadlock;

/**
 * @Author: xiaobo
 * @Date: 2020/11/22 16:28
 */
public class DeadLock01 {

    private  static String string1 = new String();
    private  static String string2 = new String();

    public static void methodA() {
        synchronized (string1) {
            try {
                Thread.currentThread().sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("a--1");
            synchronized (string2) {
                System.out.println("a--2");
            }
        }
    }

    public  static void methodB() {
        synchronized (string2) {
            System.out.println("b--2");
            synchronized (string1) {
                System.out.println("b--1");
            }
        }
    }

    public static class a implements Runnable{
        @Override
        public void run() {
            methodA();
        }
    }

    public static class b implements Runnable{
        @Override
        public void run() {
            methodB();
        }
    }

    public static void main(String[] args) throws InterruptedException{
        new Thread(new a()).start();
        new Thread(new b()).start();
    }
}
//
b--2
a--1
```

通过 jps+jstack 查看

```java

C:\Program Files\Java\jdk1.8.0_172\bin>jps
24928
35776 KotlinCompileDaemon
34676 Jps
37812 DeadLock01
37800 Launcher

C:\Program Files\Java\jdk1.8.0_172\bin>jstack -l 37812
2020-11-22 16:38:34
Full thread dump Java HotSpot(TM) 64-Bit Server VM (25.172-b11 mixed mode):

"DestroyJavaVM" #13 prio=5 os_prio=0 tid=0x0000000003053800 nid=0x8ed8 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

   Locked ownable synchronizers:
        - None

"Thread-1" #12 prio=5 os_prio=0 tid=0x000000001b692000 nid=0x8a88 waiting for monitor entry [0x000000001c2ef000]
   java.lang.Thread.State: BLOCKED (on object monitor)
        at java8.multithreading.deadlock.DeadLock01.methodB(DeadLock01.java:30)
        - waiting to lock <0x0000000781097cb0> (a java.lang.String)
        - locked <0x0000000781097cc8> (a java.lang.String)
        at java8.multithreading.deadlock.DeadLock01$b.run(DeadLock01.java:45)
        at java.lang.Thread.run(Thread.java:748)

   Locked ownable synchronizers:
   
   .........
   
   Found one Java-level deadlock:
=============================
"Thread-1":
  waiting to lock monitor 0x000000000314b238 (object 0x0000000781097cb0, a java.lang.String),
  which is held by "Thread-0"
"Thread-0":
  waiting to lock monitor 0x000000000314c628 (object 0x0000000781097cc8, a java.lang.String),
  which is held by "Thread-1"

Java stack information for the threads listed above:
===================================================
"Thread-1":
        at java8.multithreading.deadlock.DeadLock01.methodB(DeadLock01.java:30)
        - waiting to lock <0x0000000781097cb0> (a java.lang.String)
        - locked <0x0000000781097cc8> (a java.lang.String)
        at java8.multithreading.deadlock.DeadLock01$b.run(DeadLock01.java:45)
        at java.lang.Thread.run(Thread.java:748)
"Thread-0":
        at java8.multithreading.deadlock.DeadLock01.methodA(DeadLock01.java:21)
        - waiting to lock <0x0000000781097cc8> (a java.lang.String)
        - locked <0x0000000781097cb0> (a java.lang.String)
        at java8.multithreading.deadlock.DeadLock01$a.run(DeadLock01.java:38)
        at java.lang.Thread.run(Thread.java:748)

Found 1 deadlock.
```

