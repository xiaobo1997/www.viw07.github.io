[TOC]

![image-20201126220027255](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201126220027255.png)

# 1.Condition





## 概念



> 使用 condition 可选择性通知
>
> 使用notify 或notifyAll 会让所以等待的线程唤醒，而condition中的await更加灵活 有选择性，可以满足我们的复杂业务需求

> 任意一个Java对象，都拥有一组监视器方法（定义在java.lang.Object上），主要包括wait()、 wait(long timeout)、notify()以及notifyAll()方法，这些方法与synchronized同步关键字配合，可以 实现等待/通知模式。Condition接口也提供了类似Object的监视器方法，与Lock配合可以实现等 待/通知模式

> 作用

condition可以控制处理线程的状态，







## 使用

> Condition定义了等待/通知两种类型的方法，当前线程调用这些方法时，需要提前获取到 Condition对象关联的锁。



> 获取一个Condition必须通过Lock的newCondition()方法。

> 通知部分线程

```java
// class A
A{
    private ReentrantLock lock = new ReentrantLock();
    private Condition c1 = lock.newCondition();
    private Condition c2 = lock.newCondition();
    methodA{
        lock.lock();
        c1.await();
        ...
    }
    methodB{
        lock.lock():
        c2.await();
        ...
    }
    signalA(){
        lock.lock();
        c1.signalAll();
        ...
    }
    
    signalB(){
        lock.lock();
        c2.signalAll();
        ...
    }
}
//class B
Thread a;
Thread b;
psvm{
    a.start();
    A.signalA();
}
```

### 方法

![image-20201126183503928](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201126183503928.png)

```java
public interface Condition {
    // 使当前线程等待，直到被signal唤醒或被中断
    void await() throws InterruptedException;

    // 使当前线程等待，直到被signal唤醒（不响应中断）
    void awaitUninterruptibly();

    // 使当前线程等待，直到被signal唤醒、或被中断、或到达等待时间
    long awaitNanos(long nanosTimeout) throws InterruptedException;

    // 使当前线程等待，直到被signal唤醒、或被中断、或到达等待时间（与上面方法类似）
    boolean await(long time, TimeUnit unit) throws InterruptedException;

    // // 使当前线程等待，直到被signal唤醒、或被中断、或到达给定的截止时间
    boolean awaitUntil(Date deadline) throws InterruptedException;

    // 唤醒一个等待的线程
    void signal();

    // 唤醒所有等待的线程
    void signalAll();
}
```



## 底层数据结构

> 复用了AQS同步器静态内部列的Node类，
>
> ![image-20201126184829376](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201126184829376.png)
>
> 组成的一个拥有头节点和尾节点的FIFO队列，
>
> **等待队列**：
>
> ![image-20201126184701619](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201126184701619.png)
>
> **同步队列：** 
>
> ![image-20201126184943207](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201126184943207.png)



在objecd的监视器中 一个对象拥有一个等待队列和同步队列，而同步器（condition是它的内部静态类）拥有多个等待队列和同步队列



**等待队列是一个单向队列**

**同步队列是双向**





## 原理实现

> condition是AQS的一个内部类
>
> 每个Condition对象都包含着一个队 列（以下称为等待队列），该队列是Condition对象实现等待/通知功能的关键。



### await()内部源码

```java
  public final void await() throws InterruptedException {
            if (Thread.interrupted())
                throw new InterruptedException();
      //当前线程增加到等待队列
            Node node = addConditionWaiter();
      // 释放锁 并且然后通知同步队列下一个节点的线程
            long savedState = fullyRelease(node);
            int interruptMode = 0;
      //自旋
            while (!isOnSyncQueue(node)) {
                // 当前线程等待
                LockSupport.park(this);
                if ((interruptMode = checkInterruptWhileWaiting(node)) != 0)
                    break;
            }
      		// 当前线程自旋等待获取锁 
            if (acquireQueued(node, savedState) && interruptMode != THROW_IE)
                interruptMode = REINTERRUPT;
            if (node.nextWaiter != null) // clean up if cancelled
                unlinkCancelledWaiters();
      // 处理中断
            if (interruptMode != 0)
                reportInterruptAfterWait(interruptMode);
        }



    private Node addConditionWaiter() {
            Node t = lastWaiter;
            // If lastWaiter is cancelled, clean out.
            if (t != null && t.waitStatus != Node.CONDITION) {
                unlinkCancelledWaiters();
                t = lastWaiter;
            }
        // 当前线程转节点
            Node node = new Node(Thread.currentThread(), Node.CONDITION);
            if (t == null)
                // 放入第一个位置
                firstWaiter = node;
            else
                // 在末尾插入
                t.nextWaiter = node;
            lastWaiter = node;
            return node;
        }

//释放锁
final long fullyRelease(Node node) {
        boolean failed = true;
        try {
            long savedState = getState();
            if (release(savedState)) {
                // 释放锁
                failed = false;
                return savedState;
            } else {
                throw new IllegalMonitorStateException();
            }
        } finally {
            if (failed)
                node.waitStatus = Node.CANCELLED;
        }
    }

//
public static void parkNanos(Object blocker, long nanos) {
        if (nanos > 0) {
            Thread t = Thread.currentThread();
            setBlocker(t, blocker);
            UNSAFE.park(false, nanos);
            setBlocker(t, null);
        }
    }


// 不需要中断， 不会抛异常  和await()方法差不多
public final void awaitUninterruptibly() {
            Node node = addConditionWaiter();
            long savedState = fullyRelease(node);
            boolean interrupted = false;
            while (!isOnSyncQueue(node)) {
                LockSupport.park(this);
                if (Thread.interrupted())
                    interrupted = true;
            }
            if (acquireQueued(node, savedState) || interrupted)
                selfInterrupt();
        }
```



### signal() signalAll()



> 调用Condition的signal()方法，将会唤醒在等待队列中等待时间最长的节点（首节点），在 唤醒节点之前，会将节点移到同步队列中。 

![image-20201126190722844](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201126190722844.png)



> 节点从等待队列中 放入同步队列中

![image-20201126204423990](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201126204423990.png)

```java
 
public final void signal() {
    //  是否获取锁， 方法名的意思是是否独占地  
            if (!isHeldExclusively())
                throw new IllegalMonitorStateException();
    // 等待队列中 的第一个节点 first
            Node first = firstWaiter;
            if (first != null)
                // 通知队列中第一个
                doSignal(first);
        }
        
        private void doSignal(Node first) {
            do {
                if ( (firstWaiter = first.nextWaiter) == null)
                    lastWaiter = null;
                // 从等待队列中删除头节点
                first.nextWaiter = null;
            } while (!transferForSignal(first) &&
                     (first = firstWaiter) != null);
        }
        
 final boolean transferForSignal(Node node) {
     	// 更新状态为0  将头结点的状态更改为CONDITION
        if (!compareAndSetWaitStatus(node, Node.CONDITION, 0))
            return false;
     
     // 将节点放入同步队列中
     //调用同步器的enq(Node node)方法，等待队列中的头节点线程安全地移动到同步队 列。当节点移动到同步队列后，当前线程再使用LockSupport唤醒该节点的线程。
        Node p = enq(node);
        int ws = p.waitStatus;
        if (ws > 0 || !compareAndSetWaitStatus(p, ws, Node.SIGNAL))
            LockSupport.unpark(node.thread);
        return true;
    }
        
         public final void signalAll() {
            if (!isHeldExclusively())
                throw new IllegalMonitorStateException();
            Node first = firstWaiter;
            if (first != null)
                doSignalAll(first);
        }
        // 把等待队列中的每一个节点都放入同步队列中 “通知”当前调用condition.await()方法的每一个线程。相当于对等待队列中的每个节点均执行一次signal()方法，效 果就是将等待队列中所有节点全部移动到同步队列中，并唤醒每个节点的线程
         private void doSignalAll(Node first) {
            lastWaiter = firstWaiter = null;
            do {
                Node next = first.nextWaiter;
                first.nextWaiter = null;
                transferForSignal(first);
                first = next;
            } while (first != null);
        }
```



##  特性和对比



> 对比object类的方法

- 1.object类中的wait()方法 相当于 condition的 await()
- 2.......wait(long timeout) .........await(long time,TimeUnit unit)
- 3......notify() .............signal()
- 4......notifyAll().....signalAll()

![image-20201126183300014](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201126183300014.png)