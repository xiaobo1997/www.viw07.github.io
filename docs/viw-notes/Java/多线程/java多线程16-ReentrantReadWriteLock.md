[TOC]

![image-20201126215642299](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201126215642299.png)

# ReenreantReadWriteLock



## 概念



> 读写锁在同一时刻可以允许多个读线程访问，但是在写线程访问时，所有的读 线程和其他写线程均被阻塞。读写锁维护了一对锁，一个读锁和一个写锁，通过分离读锁和写 锁，使得并发性相比一般的排他锁有了很大提升。 

> 读锁不互相排斥，写锁和读锁互相排斥。写和写之间互相排斥

> 继承树和声明

```java
public class ReentrantReadWriteLock
        implements ReadWriteLock, java.io.Serializable {

// 内部结构
private final ReentrantReadWriteLock.ReadLock readerLock;
private final ReentrantReadWriteLock.WriteLock writerLock;
final Sync sync;
abstract static class Sync extends AbstractQueuedSynchronizer {
 // 具体实现 }
static final class NonfairSync extends Sync {
 // 具体实现 }
static final class FairSync extends Sync {
 // 具体实现 }
public static class ReadLock implements Lock, java.io.Serializable {
 private final Sync sync;
 protected ReadLock(ReentrantReadWriteLock lock) {
 sync = lock.sync;
 }
 // 具体实现 }
public static class WriteLock implements Lock, java.io.Serializable {
 private final Sync sync;
 protected WriteLock(ReentrantReadWriteLock lock) {
 sync = lock.sync;
 }
 // 具体实现 }
// 构造⽅法，初始化两个锁
public ReentrantReadWriteLock(boolean fair) {
 sync = fair ? new FairSync() : new NonfairSync();
 readerLock = new ReadLock(this);
 writerLock = new WriteLock(this);
}
// 获取读锁和写锁的⽅法
public ReentrantReadWriteLock.WriteLock writeLock() { return writerLock; }
public ReentrantReadWriteLock.ReadLock readLock() { return readerLock; }
```





## 使用

> 读
>
> 几乎同时进入

```java
package java8.juc.lock;

import java.util.concurrent.locks.ReentrantReadWriteLock;

/**
 * @Author: xiaobo
 * @Date: 2020/11/25 23:53
 */
public class RWLock1 {
    private static ReentrantReadWriteLock rw = new ReentrantReadWriteLock();
    private String name = "abc";

    public  static  void  method(){
        rw.readLock().lock();
        System.out.println("进入"+System.currentTimeMillis());
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }finally {
            rw.readLock().unlock();
        }
    }

    public static class A implements Runnable{
        @Override
        public void run() {
            method();
        }
    }
    public  static  class B implements Runnable{
        @Override
        public void run() {
            method();
        }
    }

    public static void main(String[] args) {
        new Thread(new A()).start();
        new Thread(new B()).start();
    }
}
//
进入1606321370757
进入1606321370757
```



> 写锁互相排斥

```java
public class RWLock1 {
    private static ReentrantReadWriteLock rw = new ReentrantReadWriteLock();
    private String name = "abc";

    public  static  void  method(){
        rw.writeLock().lock();
        System.out.println("进入"+System.currentTimeMillis());
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }finally {
            rw.writeLock().unlock();
        }
    }
    
   。。。。
   
   }
   //
   
进入1606321577184
进入1606321578184
```

> 读写  写读互相排斥

```java
package java8.juc.lock;

import java.util.concurrent.locks.ReentrantReadWriteLock;

/**
 * @Author: xiaobo
 * @Date: 2020/11/25 23:53
 */
public class RWLock1 {
    private static ReentrantReadWriteLock rw = new ReentrantReadWriteLock();
    private String name = "abc";

    public  static  void  method(){
        rw.writeLock().lock();
        System.out.println("进入"+System.currentTimeMillis());
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }finally {
            rw.writeLock().unlock();
        }
    }
    public  static  void  methodA(){
        rw.readLock().lock();
        System.out.println("进入"+System.currentTimeMillis());
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }finally {
            rw.readLock().unlock();
        }
    }

    public static class A implements Runnable{
        @Override
        public void run() {
            method();
            methodA();
        }
    }
    public  static  class B implements Runnable{
        @Override
        public void run() {
            method();
            methodA();
        }
    }

    public static void main(String[] args) {
        new Thread(new A()).start();
        new Thread(new B()).start();
    }
}
//
进入1606321815765
进入1606321816767
进入1606321817767
进入1606321817767
```





## 原理

> 重写了同步器
>
> 读写锁同样依赖**自定义同步器**来实现同步功能，而读写状态就是其同步器的同步状态。 回想ReentrantLock中自定义同步器的实现，同步状态表示锁被一个线程重复获取的次数，而读 写锁的自定义同步器需要在同步状态（一个整型变量）上维护多个读线程和一个写线程的状 态，使得该状态的设计成为读写锁实现的关键。 如果**在一个整型变量上维护多种状态**，就一定需要“按位切割使用”这个变量，读写锁将 变量切分成了两个部分，**高16位表示读**，**低16位表示写**



### 写锁的读取和释放

```java
// 获取写锁  ReentrantReadWriteLock内部类Sync的方法 重写了同步器
    protected final boolean tryAcquire(int acquires) {
        /*
         * 流程：
         * 1. 若其他线程持有读锁或写锁（计数不为零），返回 false；
         * 2. 若持有数量饱和（超出上限），返回 false；
         * 3.如果该线程是可重入获取或队列策略允许的话 该线程有资格获取锁，更新 state 并设置为(当前线程) owner。
         */
        Thread current = Thread.currentThread();
        int c = getState();
        int w = exclusiveCount(c);
        if (c != 0) {
            // (Note: if c != 0 and w == 0 then shared count != 0)
            if (w == 0 || current != getExclusiveOwnerThread())
                return false;
            if (w + exclusiveCount(acquires) > MAX_COUNT)
                throw new Error("Maximum lock count exceeded");
            // Reentrant acquire
            setState(c + acquires);
            return true;
        }
        // 若获取写锁则阻塞，或者更新 state 失败，返回 false
        if (writerShouldBlock() ||
            !compareAndSetState(c, c + acquires))
            return false;
        setExclusiveOwnerThread(current);
        return true;
    }
```

```java
    // 释放写锁 重写了 AQS的tryRelease
    protected final boolean tryRelease(int releases) {
        if (!isHeldExclusively())
            throw new IllegalMonitorStateException();
        //当前同步状态直接减去写状态的原因是写状态是由同步状态的低16位表示的
        int nextc = getState() - releases;
        boolean free = exclusiveCount(nextc) == 0;
        if (free)
            setExclusiveOwnerThread(null);
        setState(nextc);
        return free;
    }
```



### 读锁的读取和释放

```java
// 释放读锁
    protected final boolean tryReleaseShared(int unused) {
        Thread current = Thread.currentThread();
        // 若当前线程是第一个持有读锁的线程
        if (firstReader == current) {
            // assert firstReaderHoldCount > 0;
            if (firstReaderHoldCount == 1)
                firstReader = null;
            else
                firstReaderHoldCount--;
        } else {
            // 更新缓存
            HoldCounter rh = cachedHoldCounter;
            if (rh == null || rh.tid != getThreadId(current))
                rh = readHolds.get();
            int count = rh.count;
            if (count <= 1) {
                readHolds.remove();
                if (count <= 0)
                    throw unmatchedUnlockException();
            }
            --rh.count;
        }
        // 更新 state
        for (;;) {
            int c = getState();
            int nextc = c - SHARED_UNIT;
            if (compareAndSetState(c, nextc))
                // Releasing the read lock has no effect on readers,
                // but it may allow waiting writers to proceed if
                // both read and write locks are now free.
                return nextc == 0;
        }
    }
```

```java
  // 获取读锁
    protected final int tryAcquireShared(int unused) {
        /*
         * 流程：
         * 1. 如果其他线程持有写锁，获取失败；
         * 2. 否则，该线程有资格获取，因此请询问它是否由于队列策略而阻塞；
         *    若不阻塞，尝试通过 CAS 更新状态计数。
         *    注意：这一步没有检查可重入的获取，推迟到完整版本，
         *         以避免在明显不可重入的情况下检查持有计数。
         * 3. 如果第二步失败，要么是因为线程明显不符合条件、CAS 失败或计数饱和，
         *    则进行完整重试版本。
         */
        Thread current = Thread.currentThread();
        int c = getState();
        // step1. 若写锁被其他线程占用，则获取失败
        //        exclusiveCount(c) != 0表示写锁被占用
        // 当前写锁已经被其他线程获取 当前线程获取读锁失败 返回-1
        if (exclusiveCount(c) != 0 &&
            getExclusiveOwnerThread() != current)
            return -1;
        // step2. 获取读锁数量
        int r = sharedCount(c);
        if (!readerShouldBlock() &&
            r < MAX_COUNT &&
            // 当前线程获取读锁
            compareAndSetState(c, c + SHARED_UNIT)) {
            // 读锁未被占用，设置该线程是第一个持有读锁的线程
            if (r == 0) {
                firstReader = current;
                firstReaderHoldCount = 1;
            // 该线程已持有读锁，计数加1
            } else if (firstReader == current) {
                firstReaderHoldCount++;
            // 其他线程已持有读锁
            } else {
                // 取缓存
                HoldCounter rh = cachedHoldCounter;
                // 若未初始化，或者拿到的不是当前线程的计数，则从 ThreadLocal 中获取
                if (rh == null || rh.tid != getThreadId(current))
                    cachedHoldCounter = rh = readHolds.get();
                else if (rh.count == 0)
                    readHolds.set(rh);
                // 增加计数
                rh.count++;
            }
            // 获取成功
            return 1;
        }
        // step3. 若step2获取失败，则执行该步骤
        return fullTryAcquireShared(current);
    }

```

==当写锁被其他线程获取后读锁获取失败否则获取成功利用CAS更新同步状态==

当前同步状态需要加上SHARED_UNIT（`(1 << SHARED_SHIFT)`即0x00010000）的原因这是我们在上面所说的同步状态的高16位用来表示读锁被获取的次数。



### ReadLock

> 通过AQS关系模式实现

```java
public static class ReadLock implements Lock, java.io.Serializable {
    private final Sync sync;

    protected ReadLock(ReentrantReadWriteLock lock) {
        sync = lock.sync;
    }

    /**
     * 获取读锁（不可中断）：
     *  1. 若其他线程未持有写锁，则获取读锁并立即返回；
     *  2. 若其他线程持有写锁，则由于线程调度，当前线程被禁用并休眠，直到获取读锁。
     */
    public void lock() {
        sync.acquireShared(1);
    }

    /**
     * 以中断方式获取读锁：
     *  1. 若其他线程未持有写锁，则获取读锁并立即返回；
     *  2. 若其他线程持有写锁，则由于线程调度，当前线程被禁用并休眠，
     *       直到当前获取到读锁，或者被其他线程中断。
     */
    public void lockInterruptibly() throws InterruptedException {
        sync.acquireSharedInterruptibly(1);
    }

    /**
     * 仅当另一个线程未持有写锁时才能获取读锁。
     * 若另一个线程持有写锁，则立即返回 false。
     */
    public boolean tryLock() {
        return sync.tryReadLock();
    }

    /**
     * 获取读锁（与 tryLock 方法类似，多了超时等待）。
     */
    public boolean tryLock(long timeout, TimeUnit unit)
            throws InterruptedException {
        return sync.tryAcquireSharedNanos(1, unit.toNanos(timeout));
    }

    /**
     * 尝试释放该锁。
     * 若读线程的数量为零，则该锁可用于尝试获取写锁。
     */
    public void unlock() {
        sync.releaseShared(1);
    }

    public Condition newCondition() {
        throw new UnsupportedOperationException();
    }
}
```



### WriteLock

> WriteLock 的大部分方法都是通过 AQS 独占模式的方法实现的。

```java
public static class WriteLock implements Lock, java.io.Serializable {
    private final Sync sync;

    protected WriteLock(ReentrantReadWriteLock lock) {
        sync = lock.sync;
    }

    /**
     * 获取写锁。
     * 1. 若无其他线程持有读锁或写锁，则获取写锁并立即放回，并将写锁计数设为1；
     * 2. 若当前线程已经持有写锁，则将其计数加1，并立即返回（可重入）；
     * 3. 若锁被其他线程持有，当前线程被禁用并处于休眠状态，直到获取写锁（计数设为1）。
     */
    public void lock() {
        sync.acquire(1);
    }

    /**
     * 获取写锁（可被中断）。
     * 1. 若无其他线程持有读锁或写锁，则获取并立即返回写锁，并将计数设为1；
     * 2. 若当前线程已经持有写锁，则将其计数加1，并立即返回（可重入）；
     * 3. 若锁被其他线程持有，当前线程被禁用并处于休眠状态，
     *      直到当前线程获取写锁（计数设为1）或被其他线程中断。
     */
    public void lockInterruptibly() throws InterruptedException {
        sync.acquireInterruptibly(1);
    }

    /**
     * 仅当调用时其他线程未持有该写锁时，才获取该写锁。
     */
    public boolean tryLock( ) {
        return sync.tryWriteLock();
    }

    /**
     * 尝试获取写锁（响应中断，有超时等待）。
     */
    public boolean tryLock(long timeout, TimeUnit unit)
            throws InterruptedException {
        return sync.tryAcquireNanos(1, unit.toNanos(timeout));
    }

    /**
     * 尝试释放锁。
     * 若当前线程是锁的持有者，则持有计数将减少；若当前持有计数为零则释放锁。
     * 若当前线程不是锁的持有者，则抛出异常IllegalMonitorStateException
     */
    public void unlock() {
        sync.release(1);
    }

    public Condition newCondition() {
        return sync.newCondition();
    }

    /**
     * 查询此写锁是否由当前线程持有。
     */
    public boolean isHeldByCurrentThread() {
        return sync.isHeldExclusively();
    }

    /**
     * 查询当前线程对该写锁的持有计数。
     */
    public int getHoldCount() {
        return sync.getWriteHoldCount();
    }
}
```



### Sync

>  使用 AQS 中的 state 变量（int 类型）来记录读写锁的占用情况
>
> 其中高 16 位记录读锁的持有次数；
>
> 低 16 位记录写锁的重入次数



![image-20201126013050069](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201126013050069.png)

**如上图 同步状态为 一个线程已经获取了写锁且重入了两次，并且连续获取了两次读锁**

```java
  abstract static class Sync extends AbstractQueuedSynchronizer {
        private static final long serialVersionUID = 6317671515068378041L;

        static final int SHARED_SHIFT   = 16;
        static final int SHARED_UNIT    = (1 << SHARED_SHIFT);
        static final int MAX_COUNT      = (1 << SHARED_SHIFT) - 1;
        static final int EXCLUSIVE_MASK = (1 << SHARED_SHIFT) - 1;

```



## 特性

> 读写锁的特性

![image-20201125235901981](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201125235901981.png)

> 在读操作get(String key)方法中，需要获取读锁，这使 得并发访问该方法时不会被阻塞。写操作put(String key,Object value)方法和clear()方法，在更新 HashMap时必须提前获取写锁，当获取写锁后，其他线程对于读锁和写锁的获取均被阻塞，而 只有写锁被释放之后，其他读写操作才能继续。
>
> 所以在写操作时，其他线程不可以读不可以写





### 锁降级



> 锁降级指的是写锁降级成为读锁。如果当前线程拥有写锁，然后将其释放，最后再获取读 锁，这种分段完成的过程不能称之为锁降级。锁降级是指把持住（当前拥有的）写锁，再获取到读锁，随后释放（先前拥有的）写锁的过程。 

==支持降级目的：==

**主要是为了保证数据的可见性，如果 当前线程不获取读锁而是直接释放写锁，假设此刻另一个线程（记作线程T）获取了写锁并修 改了数据，那么当前线程无法感知线程T的数据更新。如果当前线程获取读锁，即遵循锁降级 的步骤，则线程T将会被阻塞，直到当前线程使用数据并释放读锁之后，线程T才能获取写锁进 行数据更新。** 



**RentrantReadWriteLock不支持锁升级** 因为如果读锁已被多个线程获取，其中任意线程成功获取了写锁并更新了 数据，则其更新对其他获取到读锁的线程是不可见的。

```java
// 这是一个代码草图，显示了在更新缓存后如何执行锁降级（以非嵌套方式处理多个锁时，异常处理特别棘手）：
// RentrantReadWriteLock源码
class CachedData {
   Object data;
   volatile boolean cacheValid;
   final ReentrantReadWriteLock rwl = new ReentrantReadWriteLock();

   void processCachedData() {
     rwl.readLock().lock();
     if (!cacheValid) {
       // 获取写锁之前必须释放读锁
       rwl.readLock().unlock();
       rwl.writeLock().lock();
       try {
         // 重新检查状态，因为另一个线程可能具有在执行之前获得了写锁定并更改了状态。
         if (!cacheValid) {
           data = ...
           cacheValid = true;
         }
         // 通过在释放写锁之前获取读锁来降级
         rwl.readLock().lock();
       } finally {
         rwl.writeLock().unlock(); // 解写锁，仍保持读锁
       }
     }

     try {
       use(data);
     } finally {
       rwl.readLock().unlock();
     }
   }
 }
```



## 场景



> **`ReentrantReadWriteLock` 适用于读多写少的场景**

