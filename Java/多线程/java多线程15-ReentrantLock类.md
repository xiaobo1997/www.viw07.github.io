[TOC]

![image-20201126161416792](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201126161416792.png)

# 1.ReentrantLock



## 概念



> 在前面可以看到Lock体系。ReentrantLock是Lock接口的实现。
>
> 看一下它的继承树
>
> ![image-20201125152909877](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201125152909877.png)



> ​		从源码上看，它内部有⼀个抽象 类 Sync ，是继承了**AQS**，⾃⼰实现的⼀个**同步器**。同时，ReentrantLock内部有两个⾮抽象类 NonfairSync 和 FairSync ，它们都继承了Sync。从名字上看得出，分别是”⾮公平同步器“和”公平同步器“的意思。这意味着ReentrantLock可以⽀持”公 平锁“和”⾮公平锁“。

```java
public class ReentrantLock implements Lock, java.io.Serializable {
    private static final long serialVersionUID = 7373984872572414699L;
  
    private final Sync sync;

    abstract static class Sync extends AbstractQueuedSynchronizer {
        private static final long serialVersionUID = -5179523762034025860L;
	  ....
    }
     static final class FairSync extends Sync {
     .....
     }
    static final class NonfairSync extends Sync {
        private static final long serialVersionUID = 7316153563782823691L;
    ....
    }
```

**两个同步器的源码可以发现，它们的实现都是”独占“的。都调⽤了AOS的 setExclusiveOwnerThread ⽅法，所以ReentrantLock的锁的”独占“的，也就是 说，它的锁都是”排他锁“，不能共享。** 

> 在ReentrantLock的构造⽅法⾥，可以传⼊⼀个 boolean 类型的参数，来指定它是 否是⼀个公平锁，默认情况下是⾮公平的。这个参数⼀旦实例化后就不能修改，只 能通过 isFair() ⽅法来查看。

```java
 public ReentrantLock(boolean fair) {
        sync = fair ? new FairSync() : new NonfairSync();
    }
```



## 使用



> 一个简单的例子

```java
    private ReentrantLock reentrantLock = new ReentrantLock();

    public class A implements Runnable {
        @Override
        public void run() {
            reentrantLock.lock();
            try {
                Thread.sleep(1000);
                System.out.println("aaa");
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                reentrantLock.unlock();
            }
        }
    }
```

> 同步

```java
package java8.juc.lock;

import java.util.concurrent.locks.ReentrantLock;

/**
 * @Author: xiaobo
 * @Date: 2020/11/25 21:21
 */
public class ReenteantLock2 {
    private static ReentrantLock reentrantLock = new ReentrantLock();

    public static void methodA(){
        for (int i = 0;i<3;i++){
            System.out.println(i+"：："+Thread.currentThread().getName());
        }
    }

    public static class threadA implements Runnable{
        @Override
        public void run() {
            try {
                reentrantLock.lock();
                Thread.sleep(1000);
                methodA();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }finally {
                reentrantLock.unlock();
            }
        }
    }
    public static class threadB implements Runnable{
        @Override
        public void run() {
            try {
                reentrantLock.lock();
                Thread.sleep(1000);
                methodA();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }finally {
                reentrantLock.unlock();
            }
        }
    }
    public static void main(String[] args) {
       new Thread(new threadA()).start();
       new Thread(new threadB()).start();
    }
}
//
0：：Thread-0
1：：Thread-0
2：：Thread-0
0：：Thread-1
1：：Thread-1
2：：Thread-1
```

> 使用 condition 可选择性通知
>
> 使用await()和signl实现wait/notify()。await之前要获取锁 结束释放锁
>
> await让线程等待并释放锁
>
> 使用condition来 实现等待/通知

```java
// class A 
public class A{
private ReentrantLock lock = new ReentrantLock();
private Condition c = lock.newCondition();
public void waitA(){
try{
	lock.lock();
	c.await();
}catch(Exception e){
 e.printStackTrace();
}finally{ lock.unlock();}
}
    public void sigin(){
        try{
            lock.lock();
            c.signal();
        }catch(..){
            ...
        }finally{ lock.unlock(); }
    }
}
// calss B 
public class B implements Runnable{
    ...
       public void run(){
        await();
    }
}
// class c
public class C{
    psvm{
        new Thread(new B()).start();
        A a = new A();
        a.signal();
    }
}
```



### 常用方法



```java
// 获取锁（阻塞式）
public void lock() {
    sync.lock();
}

// 获取锁（响应中断）
// 当线程获取锁并且阻塞在lockInterruptibly方法时 这个线程可以中断
public void lockInterruptibly() throws InterruptedException {
    sync.acquireInterruptibly(1);
}

// 尝试获取锁  如果成功，则返回 true；如果失败，则返回 false。也就是说，这个方法无论成败都会立即返回，获取不到锁（锁已被其他线程获取）时不会一直等待。
// 嗅探拿锁
public boolean tryLock() {
    return sync.nonfairTryAcquire(1);
}

// 尝试获取锁（有超时等待）在时间期限之内如果还获取不到锁，就返回 false。如果如果一开始拿到锁或者在等待期间内拿到了锁，则返回 true。
// 嗅探拿锁
public boolean tryLock(long timeout, TimeUnit unit)
        throws InterruptedException {
    return sync.tryAcquireNanos(1, unit.toNanos(timeout));
}

// 释放锁
public void unlock() {
    sync.release(1);
}

// 查询当前获取锁的次数
lock.getHoldCount();
// 查询正等待锁的线程数
lock.getQueueLength();
//是否为公平锁
lock.isFair();
// 是否被锁
lock.isLocked();
//是否当前线程获取锁
lock.isHeldByCurrentThread();
```







## 原理



### 重进入

> ReentrantLock虽然没能像synchronized关键字一样支持隐式的重进入，但是在调用lock()方 法时，已经获取到锁的线程，能够再次调用lock()方法获取锁而不被阻塞。

如：

![image-20201126124433748](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201126124433748.png)

### 公平锁的原理

```java
    static final class FairSync extends Sync {
        private static final long serialVersionUID = -3000897897090466540L;

        final void lock() {
            acquire(1);
        }

       // 
        protected final boolean tryAcquire(int acquires) {
            final Thread current = Thread.currentThread();
            int c = getState();
           // 状态为0  说明资源没有被占用
            if (c == 0) {
                 // 若队列中有其他线程在排队等待，则返回 false，表示获取失败；
            //   否则，再尝试去修改 state 的值
                if (!hasQueuedPredecessors() &&
                    compareAndSetState(0, acquires)) {
                    setExclusiveOwnerThread(current);
                    return true;
                }
            }
            // 当前线程已经得到了锁，重入
            else if (current == getExclusiveOwnerThread()) {
                int nextc = c + acquires;
                if (nextc < 0)
                    throw new Error("Maximum lock count exceeded");
                setState(nextc);
                return true;
            }
            return false;
        }
    }

```



### 非公平锁的原理

```java
 static final class NonfairSync extends Sync {
        private static final long serialVersionUID = 7316153563782823691L;

        /**
         * Performs lock.  Try immediate barge, backing up to normal
         * acquire on failure.
         */
        final void lock() {
            // CAS 修改 状态 为1
            if (compareAndSetState(0, 1))
                // 修改成功，设置当前线程为owner 表示获取了锁
                setExclusiveOwnerThread(Thread.currentThread());
            else
                // 否则没有获取锁
                acquire(1);
        }

        protected final boolean tryAcquire(int acquires) {
            return nonfairTryAcquire(acquires);
        }
    }

 final boolean nonfairTryAcquire(int acquires) {
            final Thread current = Thread.currentThread();
            // 获取状态
     		int c = getState();
     		// 当前线程获取锁 因为这个锁没有其他线程获取
            if (c == 0) {
                if (compareAndSetState(0, acquires)) {
                    setExclusiveOwnerThread(current);
                    return true;
                }
            }
     	// 锁已被获取  判断是否是当前线程
            else if (current == getExclusiveOwnerThread()) {
                int nextc = c + acquires;
                if (nextc < 0) // overflow
                    throw new Error("Maximum lock count exceeded");
                setState(nextc);
                return true;
            }
            return false;
        }

```

**如果该锁已经被线程所占有了，会继续检查占有线程是否为当前线程，如果是的话，同步状态加1返回true，表示可以再次获取成功。**



**区别：**

==公平锁增加了判断条件 hasQueuedPredecessors，先判断队列中有没有其他线程在等待，没有才去获取，有就失败，==

公平锁的hasQueuedPredecessors方法 在AQS的实现

![image-20201125232405149](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201125232405149.png)



### 可见性的原理

> ReentrantLok的重写AQS同步器的内部类Sync中 有维护state状态，	

==在线程读写共享变量之前 ，先读写一次state，再线程读写共享变量之后，再读写一次stat==

- 线程A  对 共享变量的读写  先行发生在 线程A释放锁 unlock()
- 线程A的 unlock 先行发生 线程B的lock()
- 线程A对共享变量的读写 先行发生在线程B的lock()



## 特性

- **`ReentrantLock` 提供了与 `synchronized` 相同的互斥性、内存可见性和可重入性**。
- `ReentrantLock` **支持公平锁和非公平锁**（默认）两种模式。
- ReentrantLock实现了Lock接口，支持了synchronized所不具备的灵活性
  - `synchronized` 无法中断一个正在等待获取锁的线程
  - `synchronized` 无法在请求获取一个锁时无休止地等待

## 比较

> reentrantlock的优势

- 可以多路控制线程处理，通过condition，更加灵活

- 可以实现公平锁，

- 一个 ReentrantLock 可以同时绑定多个 Condition 对象。

- 当持有锁的线程长期不释放锁的时候，正在等待的线程可以选择放弃等待，改为处理其他事情。

- ReentrantLock 可中断，而 synchronized 不行。

> synchronized的不足之处

如果临界区是只读操作，其实可以多线程⼀起执⾏，但使⽤synchronized的 话，同⼀时间只能有⼀个线程执⾏。

synchronized⽆法知道线程有没有成功获取到锁 使⽤synchronized，如果临界区因为IO或者sleep⽅法等原因阻塞了，⽽当前 线程⼜没有释放锁，就会导致所有线程等待。 



| sychronized的比较reentrantlock | 相同                                                   | 不同                                                |
| ------------------------------ | ------------------------------------------------------ | --------------------------------------------------- |
|                                | 互斥锁                                                 | synchronized 是语法层面实现，自动获取锁和释放锁     |
|                                | 可重入                                                 | ReentrantLock 是 API 层面实现，手动获取锁和释放锁。 |
|                                | 独占式获取锁，也就是在同一时刻只有一个线程能够获取锁。 |                                                     |
|                                | 默认都是非公平锁                                       |                                                     |



> 公平锁和非公平锁

公平锁保证线程在请求资源上的时间顺序一致

非公平锁会造成线程饥饿的情况

公平锁会经常切换上下文，影响性能

非公平锁减少了 上下文，因为默认是非公平，系统有更大的吞吐量

