[TOC]

![image-20201125004637115](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201125004637115.png)

# 1.Lock



> **synchronized不足之处：**
>
> 如果临界区是只读操作，其实可以多线程⼀起执⾏，但使⽤synchronized的 话，同⼀时间只能有⼀个线程执⾏。
>
> synchronized⽆法知道线程有没有成功获取到锁 使⽤synchronized，如果临界区因为IO或者sleep⽅法等原因阻塞了，⽽当前 线程⼜没有释放锁，就会导致所有线程等待。 



> Java在 java.util.concurrent.locks 包下，还为我们提供了⼏个关于锁的类和接⼝。它们有更强⼤的功能或更⾼的性能。 

### JUC包依赖树



![Packageconcurrent](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/Packageconcurrent.png)

### Lock结构

![image-20201125002435401](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201125002435401.png)



#### AQS AQLS  AOS

> AQS:**AQS**（AbstractQueuedSynchronizer）⽽AQS⾥⾯的 
>
> “资源”是⽤⼀个 int 类型的数据来表示的，有时候我们的业务需求资源的数量超出 
>
> 了 int 的范围，所以在JDK 1.6 中，多了⼀个
>
> **AQLS**（AbstractQueuedLongSynchronizer）。它的代码跟AQS⼏乎⼀样，只是把 
>
> 资源的类型变成了 long 类型。



> AOS:⽤于表示锁与持有者之间的关系（独占模式）。

####  Condition/Lock/ReadWriteLock



> juc.locks包下共有三个接⼝： Condition 、 Lock 、 ReadWriteLock 。

> Condition接⼝也提供了类似Object监视器的⽅法，通过与**Lock**配合来 实现等待/通知模式。 
>
> 任意一个Java对象，都拥有一组监视器方法（定义在java.lang.Object上），主要包括wait()、 wait(long timeout)、notify()以及notifyAll()方法，这些方法与synchronized同步关键字配合，可以 实现等待/通知模式。**Condition接口**也提供了类似Object的监视器方法，与Lock配合可以实现等 待/通知模式，但是这两者在使用方式以及功能特性上还是有差别的
>
> 他们的区别如下，大佬书中的对比很详细了

![image-20201125003122995](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201125003122995.png)



> condition方法
>
> - await():
>   - 当前线程进⼊等待状态直到被通知（signal）或者 中断；当前线程进⼊运⾏状态并从await()⽅法返回 的场景包括：（1）其他线程调⽤相同Condition对 象的signal/signalAll⽅法，并且当前线程被唤醒； （2）其他线程调⽤interrupt⽅法中断当前线程；
> - awaitUninterruptibly() 
>   - 当前线程进⼊等待状态直到被通知，在此过程中对 中断信号不敏感，不⽀持中断当前线程
> - awaitNanos(long) 
>   - 当前线程进⼊等待状态，直到被通知、中断或者超 时。如果返回值⼩于等于0，可以认定就是超时了
> - awaitUntil(Date)
>   - 当前线程进⼊等待状态，直到被通知、中断或者超 时。如果没到指定时间被通知，则返回true，否则 返回false
> - signal()
>   - 唤醒⼀个等待在Condition上的线程，被唤醒的线 程在⽅法返回前必须获得与Condition对象关联的 锁
> - signalAll()
>   - 唤醒所有等待在Condition上的线程，能够从await()等⽅法返回的线程必须先获得与Condition对象关联的锁

参考 `<深入浅出多线程>` github有在线 



### Atomic结构

![image-20201125002520404](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201125002520404.png)



## LOCK和Condition



> Lock

![image-20201124235413289](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201124235413289.png)



> Condition

![image-20201124235435359](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201124235435359.png)

## 1.重入锁和非重入



> 所谓重⼊锁，顾名思义。就是⽀持重新进⼊的锁，也就是说这个锁⽀持⼀个线程对 资源重复加锁。
>
> synchronized关键字就是使⽤的重⼊锁。⽐如说，你在⼀个synchronized实例⽅法 
>
> ⾥⾯调⽤另⼀个本实例的synchronized实例⽅法，它可以重新进⼊这个锁，不会出 现任何异常。 
>
> 如果我们⾃⼰在继承AQS实现同步器的时候，没有考虑到占有锁的线程再次获取锁 
>
> 的场景，可能就会导致线程阻塞，那这个就是⼀个“⾮可重⼊锁”。**ReentrantLock 的中⽂意思就是可重⼊锁。**





## 2.读写锁和排它锁



> 我们前⾯讲到的synchronized⽤的锁和ReentrantLock，其实都是“排它锁”。也就是 说，这些锁在同⼀时刻只允许⼀个线程进⾏访问。 ⽽读写锁可以再同⼀时刻允许多个读线程访问。Java提供了**ReentrantReadWriteLock类作为读写锁的默认实现，**内部维护了两个锁：⼀个读 锁，⼀个写锁。通过分离读锁和写锁，使得在“读多写少”的环境下，⼤⼤地提⾼了 性能。
>
> ==即使⽤读写锁，在写线程访问时，所有的读线程和其它写线程均被阻 塞。== 



## 3公平和非公平锁

> 这⾥的“公平”，其实通俗意义来说就是“先来后到”，也就是**FIFO**。如果对⼀个锁来 
>
> 说，先对锁获取请求的线程⼀定会先被满⾜，后对锁获取请求的线程后被满⾜，那 
>
> 这个锁就是公平的。反之，那就是不公平的。 
>
> ⼀般情况下，⾮公平锁能提升⼀定的效率。但是⾮公平锁可能会发⽣线程饥饿（有 
>
> ⼀些线程⻓时间得不到锁）的情况。所以要根据实际的需求来选择⾮公平锁和公平 
>
> 锁。**ReentrantLock⽀持⾮公平锁和公平锁两种**。



## 4.LockSupport工具

> 当需要阻塞或唤醒一个线程的时候，都会使用LockSupport工具类来完成相应 
>
> 工作。LockSupport定义了一组的公共静态方法，这些方法提供了最基本的线程阻塞和唤醒功 
>
> 能，而LockSupport也成为构建同步组件的基础工具。 



