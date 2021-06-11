[TOC]

![image-20201126221134918](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201126221134918.png)

# 1LockSupport



## 概念



> 当需要阻塞或唤醒一个线程的时候，都会使用LockSupport工具类来完成相应 工作。LockSupport定义了一组的公共静态方法，这些方法提供了最基本的线程阻塞和唤醒功 能，而LockSupport也成为构建同步组件的基础工具。





> 类声明

```java
public class LockSupport {
    private LockSupport() {} // Cannot be instantiated.
```



## 使用



## 方法

- 阻塞线程 

  - void park()：阻塞当前线程，如果调用unpark方法或者当前线程被中断，从能从park()方法中返回
  - void park(Object blocker)：功能同方法1，入参增加一个Object对象，用来记录导致线程阻塞的阻塞对象，方便进行问题排查；
  - void parkNanos(long nanos)：阻塞当前线程，最长不超过nanos纳秒，增加了超时返回的特性；
  - void parkNanos(Object blocker, long nanos)：功能同方法3，入参增加一个Object对象，用来记录导致线程阻塞的阻塞对象，方便进行问题排查；
  - void parkUntil(long deadline)：阻塞当前线程，知道deadline； 
  - void parkUntil(Object blocker, long deadline)：功能同方法5，入参增加一个Object对象，用来记录导致线程阻塞的阻塞对象，方便进行问题排查；

  

- 唤醒线程 

  - void unpark(Thread thread):唤醒处于阻塞状态的指定线程



==**synchronzed致使线程阻塞，线程会进入到BLOCKED状态，而调用LockSupprt方法阻塞线程会致使线程进入到WAITING状态。**==

```java
public class LockSupport1 {
    public static void main(String[] args) {
        Thread thread = new Thread(()->{
            LockSupport.park();//阻塞当前线程
            System.out.println(Thread.currentThread().getName());
        });
        thread.start();
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        LockSupport.unpark(thread);//唤醒处于阻塞状态的指定线程
    }
}
```

**thread线程调用LockSupport. park()致使thread阻塞，当mian线程睡眠1秒结束后通过LockSupport.unpark(thread)方法唤醒thread线程,thread线程被唤醒执行后续操作。**

**LockSupport.unpark(thread)可以指定线程对象唤醒指定的线程**。