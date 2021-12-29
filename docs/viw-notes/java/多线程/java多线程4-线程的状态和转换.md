[TOC]



# 1.线程的状态



## 操作系统的线程状态

![image-20201117001352481](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201117001352481.png)



**操作系统的线程状态：**

- 就绪状态： ready线程正在等待使用CPU，经调度程序调用之后可进入running状态。
- 执行状态： running线程正在使用cpu
- 等待状态 ： waiting线程等待其他资源或者等待事件



## Java线程的状态



![image-20201117000636824](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201117000636824.png)



![线程状态](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/线程状态.png)



```java
public enum State {
    NEW,//新创建
    RUNNABLE,// 可运行
    BLOCKED,// 被阻塞
    WAITING,// 等待
    TIMED_WAITING, // 计时等待
    TERMINATED; // 被终止
}
```



```java
thread.getState()//获取线程状态
```



### NEW

> 使用 new 创建Thread 实例 以后，只是创建了线程实例，还没有调用start()方法，现在的线程状态是 NEW

```java
private void testStateNew() {
    Thread thread = new Thread(() -> {});
    System.out.println(thread.getState()); // 输出 NEW 
}
```



### RUNNABLE

> 调用了start()方法，这个线程可能正在运行，有可能没有正在运行(等待资源)，这和JVM规范有关，这个状态的线程是在JVM中运行，

### BLOCKED

> 阻塞状态。处于BLOCKED状态的线程正等待锁的释放以进入同步区。
>
> 当线程处于被阻塞或等待状态时，它暂时不活动。它不运行任何代码且消耗最少的资
>
> 源。直到线程调度器重新激活它。



### WAITING

> 等待状态。处于等待状态的线程变成RUNNABLE状态需要其他线程唤醒。
>
> 它暂时不活动。它不运行任何代码且消耗最少的资源。直到线程调度器重新激活它。

**进入等待状态的方法**

- Object.wait()：使当前线程处于等待状态直到另一个线程唤醒它；

- Thread.join()：等待线程执行完毕，底层调用的是Object实例的wait方法；

- LockSupport.park()：除非获得调用许可，否则禁用当前线程进行线程调度。



**需要注意的是：**

当一个线程被阻塞或等待时（或终止时)，另一个线程被调度为运行状态。

当一个线程被重新激活（例 如， 因为超时期满或成功地获得了一个锁)，调度器检查它是否具有比当前运行线程更高的优先级。如果是这样，调度器从当前运行线程中挑选一个， 剥夺其运行权，选择一个新的线程

运行。



### TIMED_WATING

> 超时等待状态。线程等待一个具体的时间，时间到后会被自动唤醒。

**进入超时等待状态的方法**

Thread.sleep(long millis)：使当前线程睡眠指定时间；

Object.wait(long timeout)：线程休眠指定时间，等待期间可以通过notify()/notifyAll()唤醒；

Thread.join(long millis)：等待当前线程最多执行millis毫秒，如果millis为0，则会一直执行；

LockSupport.parkNanos(long nanos)： 除非获得调用许可，否则禁用当前线程进行线程调度指定时间；

LockSupport.parkUntil(long deadline)：同上，也是禁止线程进行调度指定时间；







### TERMINATED



线程因如下两个原因之一而被终止：

- 为 **run** 方法正常退出而自然死亡
- 因为一个没有捕获的异常终止了 nm 方法而意外死亡。



# 2.线程的转换



![image-20201117125926057](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201117125926057.png)



## BLOCK和RUNNABLE

> BLOCK是因为等待锁的释放

```java
package java8.multithreading.threadstatus;


/**
 * @Author: xiaobo
 * @Date: 2020/11/17 1:15
 */
public class Status01 {
    private synchronized void testMethod() {
        try {
            Thread.sleep(3000);
        }catch (InterruptedException e){
            e.printStackTrace();
        }
    }
    public void  blockTest() throws  InterruptedException{
        Thread a = new Thread(new Runnable() {
            @Override
            public void run() {
                testMethod();
            }
        }, "a");
        Thread b = new Thread(new Runnable() {
            @Override
            public void run() {
                testMethod();
            }
        }, "b");
        a.start();
        Thread.sleep(1000);
        b.start();
        System.out.println(a.getState());
        System.out.println(b.getState());
    }
    public static void main(String[] args) throws  InterruptedException{
        Status01 status01 = new Status01();
        status01.blockTest();

    }

}

//

TIMED_WAITING
BLOCKED

```

```java
package java8.multithreading.threadstatus;


/**
 * @Author: xiaobo
 * @Date: 2020/11/17 1:23
 */
public class Stats02 {
    // 同步方法争夺锁
    private synchronized void testMethod() {
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
    public void blockTest() {
        Thread a = new Thread(new Runnable() {
            @Override
            public void run() {
                testMethod();
            }
        }, "a");
        Thread b = new Thread(new Runnable() {
            @Override
            public void run() {
                testMethod();
            }
        }, "b");
        a.start();
        try {
            System.out.println(Thread.currentThread().getName());
            Thread.sleep(1000);// 
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        b.start();
        System.out.println(a.getState());
        System.out.println(b.getState());
    }
	/**
	main线程休眠，所以线程a的run()方法跟着执行，线程b再接着执行。
	*/
    public static void main(String[] args) {
        Stats02 status01 = new Stats02();
        status01.blockTest();

    }
}
//
main
TIMED_WAITING
RUNNABLE
```



## WATING & RUNNABLE

> 有3个方法可以使线程从RUNNABLE状态转为WAITING状态。主要是Object.wait()和Thread.join()。



**wait()**

​		调用wait()方法的线程要先得到锁对象。然后线程释放锁对象，等待其他线程使用 `notify()/notifyAll()` 唤醒该线程，

​		其他线程调用notify()方法只会唤醒单个等待锁的线程，如有有多个线程都在等待这个锁的话不一定会唤醒到之前调用wait()方法的线程。

​		调用notifyAll()方法唤醒所有等待锁的线程之后，也不一定会马上把时间片分给刚才放弃锁的那个线程，具体要看系统的调度。

**join()**

​		调用 `join()`方法不会释放锁，会等待该线程执行完毕(TERMINTED)

例：

```java
package java8.multithreading.threadstatus;


/**
 * @Author: xiaobo
 * @Date: 2020/11/17 1:23
 */
public class Stats02 {
    // 同步方法争夺锁
    private synchronized void testMethod() {
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public void blockTest() throws InterruptedException {
        Thread a = new Thread(new Runnable() {
            @Override
            public void run() {
                testMethod();
            }
        }, "a");
        Thread b = new Thread(new Runnable() {
            @Override
            public void run() {
                testMethod();
            }
        }, "b");
        System.out.println(Thread.currentThread().getName() + Thread.currentThread().getState());
        a.start();
        a.join();//等待线程死亡
        b.start();
        System.out.println(a.getState());
        System.out.println(b.getState());

    }

    public static void main(String[] args) throws InterruptedException {
        Stats02 status01 = new Stats02();
        status01.blockTest();
    }
}

//
mainRUNNABLE
TERMINATED
RUNNABLE
```

​		没有调用join方法，main线程不管a线程是否执行完毕都会继续往下走。

​		先执行main线程，然后因为 a 线程 调用join()方法，main线程等待a执行完毕，





## TIMED_WAITING与RUNNABLE

> TIMED_WAITING状态等待的时间是指定的。

**Thread.sleep(long)**

> 使当前线程睡眠指定时间。这里的“睡眠”只是暂时使线程停止执行，并不会释放锁。时间到后，线程会重新进入RUNNABLE状态。

**Object.wait(long)**

> wait(long)方法使线程进入TIMED_WAITING状态。这里的wait(long)方法与无参方法wait()相同的地方是，都可以通过其他线程调用notify()或notifyAll()方法来唤醒。
>
> 不同的地方是，有参方法wait(long)就算其他线程不来唤醒它，经过指定时间long之后它会自动唤醒，拥有去争夺锁的资格。

**Thread.join(long)**

> join(long)使当前线程执行指定时间，并且使线程进入TIMED_WAITING状态。





## 线程中断

> 线程中断机制是一种协作机制。通过中断操作并不能直接终止一个线程，而是通知需要被中断的线程自行处理。



**线程中断的几个方法**



- Thread.interrupt()：中断线程。这里的中断线程并不会立即停止线程，而是设置线程的中断状态为true（默认是flase）；

- Thread.interrupted()：测试当前线程是否被中断。线程的中断状态受这个方法的影响，意思是调用一次使线程中断状态设置为true，连续调用两次会使得这个线程的中断状态重新转为false；

- Thread.isInterrupted()：测试当前线程是否被中断。与上面方法不同的是调用这个方法并不会影响线程的中断状态。



**注意的是：**

在线程中断机制里，当其他线程通知需要被中断的线程后，线程中断的状态被设置为true，但是具体被要求中断的线程要怎么处理，完全由被中断线程自己而定，可以在合适的实际处理中断请求，也可以完全不处理继续执行下去。





![线程切换](https://img-blog.csdnimg.cn/20200414172944605.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIwNjY3NTEx,size_16,color_FFFFFF,t_70)