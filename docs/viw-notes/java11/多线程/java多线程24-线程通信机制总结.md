[TOC]



# 1.线程通信方法总结



|             |                                                              |                                                              |
| ----------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| wait()      | public final native void wait()                              | object()方法，释放锁，线程等待，直到被唤醒 才继续执行。所以需要先得到锁。让当前线程 (Thread.concurrentThread() 方法所返回的线程) 释放对象锁并进入等待（阻塞）状态 |
| notify      | public final native void notify()                            | object()方法唤醒等待的线程，并不是唤醒了就一定能得到锁       |
| notifyAll() | public final native void notifyAll()                         | object()方法，唤醒等待的所有线程，并不是唤醒了就一定能得到锁。唤醒所有正在等待相应对象锁的线程，使它们进入就绪队列，以便在当前线程释放锁后竞争锁，进而得到CPU的执行。 |
| await()     | boolean await(long time,               TimeUnit unit)               throws InterruptedException | Condition的方法。释放锁，唤醒后进入就绪态。造成当前线程在接到信号、被中断或到达指定等待时间之前一直处于等待状态。`InterruptedException` - 如果当前线程被中断抛出这个异常或者超时 |
| signal()    | void signal()                                                | Condition的方法。唤醒一个等待线程。如果所有的线程都在等待此条件，则选择其中的一个唤醒。 |
| signalAll() | void signalAll()                                             | Condition的方法。唤醒所有等待线程。如果所有的线程都在等待此条件，则唤醒所有线程。 |

