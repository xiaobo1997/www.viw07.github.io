[TOC]



# 线程池







## 线程池体系



>  Java中的线程池顶层接⼝是 Executor 接⼝， ThreadPoolExecutor 是这个接⼝的实 现类。 
>
> 继承树

![image-20201128002146425](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201128002146425.png)





## 概念



> 为什么使用线程池

- 1.降低资源消耗，因为重复创建销毁线程是没必要的
- 2.通过响应速度，任务调度执行时，线程已经创建好，拿来即用
- 3.可以管理线程，生命周期等，使用线程池可以进行统一分配、调优和监控



> 线程池是什么

线程池是为每一个任务创建线程的替代方法，线程池可以重与线程来执行任务







## 原理



### 线程池处理流程



![image-20201128005821059](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201128005821059.png)





## 使用



## 



# Executor



## 概念

> Executor 框架是一个根据一组执行策略调用，调度，执行和控制的异步任务的框架，目的是提供一种将”任务提交”与”任务如何运行”分离开来的机制。
>
> Java线程的创建与销毁需要一定的开销，如果我们 为每一个任务创建一个新线程来执行，这些线程的创建与销毁将消耗大量的计算资源。同时， 为每一个任务创建一个新线程来执行，这种策略可能会使处于高负荷状态的应用最终崩溃。 Java的线程既是工作单元，也是执行机制。从JDK 5开始，把工作单元与执行机制分离开 来。工作单元包括Runnable和Callable，而执行机制由Executor框架提供

计算机线程和JVM线程

> 在HotSpot VM的线程模型中，Java线程（java.lang.Thread）被一对一映射为本地操作系统线 程。Java线程启动时会创建一个本地操作系统线程；当该Java线程终止时，这个操作系统线程 也会被回收。操作系统会调度所有线程并将它们分配给可用的CPU。 

## ThreadPoolExecutor



### 构造方法

> ThreadPoolExecutor 有四个构造方法

```java
// 五个参数的构造函数
public ThreadPoolExecutor(int corePoolSize,
 int maximumPoolSize,
 long keepAliveTime,
 TimeUnit unit,
 BlockingQueue<Runnable> workQueue)
// 六个参数的构造函数-1
public ThreadPoolExecutor(int corePoolSize,
 int maximumPoolSize,
 long keepAliveTime,
 TimeUnit unit,
 BlockingQueue<Runnable> workQueue,
 ThreadFactory threadFactory)
// 六个参数的构造函数-2
public ThreadPoolExecutor(int corePoolSize,
 int maximumPoolSize,
 long keepAliveTime,
 TimeUnit unit,
 BlockingQueue<Runnable> workQueue,
 RejectedExecutionHandler handler)
// 七个参数的构造函数
public ThreadPoolExecutor(int corePoolSize,
 int maximumPoolSize,
 long keepAliveTime,
 TimeUnit unit,
 BlockingQueue<Runnable> workQueue,
 ThreadFactory threadFactory,
 RejectedExecutionHandler handler)
```

### 7个参数

涉及到的七个参数：

- 1.**int corePoolSize**：该线程池中核⼼线程数最⼤值 

  - 核⼼线程：线程池中有两类线程，核⼼线程和⾮核⼼线程。核⼼线程默 

    认情况下会⼀直存在于线程池中，即使这个核⼼线程什么都不⼲（铁饭 

    碗），⽽⾮核⼼线程如果⻓时间的闲置，就会被销毁（临时⼯）。

- 2.**int maximumPoolSize**：该线程池中线程总数最⼤值 。

  - 该值等于核⼼线程数量 + ⾮核⼼线程数量

- 3.**long keepAliveTime**：⾮核⼼线程闲置超时时⻓

  - ⾮核⼼线程如果处于闲置状态超过该值，就会被销毁。如果设置

    allowCoreThreadTimeOut(true)，则会也作⽤于核⼼线程。 

- 4.**TimeUnit unit**：keepAliveTime的单位

  - TimeUnit是⼀个枚举类型 ，包括以下属性：

    NANOSECONDS ： 1微毫秒 = 1微秒 / 1000 MICROSECONDS ： 1微秒 =

    1毫秒 / 1000 MILLISECONDS ： 1毫秒 = 1秒 /1000 SECONDS ： 秒

    MINUTES ： 分 HOURS ： ⼩时 DAYS ： 天

- 5.**BlockingQueue workQueue**：阻塞队列，维护着等待执⾏的**Runnable**任务 

  对象。 

  - 常⽤的⼏个阻塞队列

  - \1. **LinkedBlockingQueue**

    链式阻塞队列，底层数据结构是链表，默认⼤⼩是 Integer.MAX_VALUE ， 

    也可以指定⼤⼩。 

    \2. **ArrayBlockingQueue**

    数组阻塞队列，底层数据结构是数组，需要指定队列的⼤⼩。 

    \3. **SynchronousQueue**

    同步队列，内部容量为0，每个put操作必须等待⼀个take操作，反之亦

    然。 

    \4. **DelayQueue**

    延迟队列，该队列中的元素只有当其指定的延迟时间到了，才能够从队列 

    中获取到该元素 。 

- 6.**ThreadFactory threadFactory**创建线程的⼯⼚ ，⽤于批量创建线程，统⼀在创建线程时设置⼀些参数，如是 否守护线程、线程的优先级等。如果不指定，会新建⼀个默认的线程⼯⼚。

  - ```java
    static class DefaultThreadFactory implements ThreadFactory {
          ........
            DefaultThreadFactory() {
                SecurityManager s = System.getSecurityManager();
                group = (s != null) ? s.getThreadGroup() :
                                      Thread.currentThread().getThreadGroup();
                namePrefix = "pool-" +
                              poolNumber.getAndIncrement() +
                             "-thread-";
            }
    
            public Thread newThread(Runnable r) {
                Thread t = new Thread(group, r,
                                      namePrefix + threadNumber.getAndIncrement(),
                                      0);
                if (t.isDaemon())
                    t.setDaemon(false);
                if (t.getPriority() != Thread.NORM_PRIORITY)
                    t.setPriority(Thread.NORM_PRIORITY);
                return t;
            }
        }
    ```

- 7.**RejectedExecutionHandler handler**拒绝处理策略，线程数量⼤于最⼤线程数就会采⽤拒绝处理策略，

  - ![image-20201128020556622](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201128020556622.png)

  - ThreadPoolExecutor 默认的拒绝策略为 AbortPolicy，

  - 可以自定义拒绝策略（实现 RejectedExecutionHandler 接口）。

  - ```java
    public static class AbortPolicy implements RejectedExecutionHandler {
    
        public AbortPolicy() { }
    
        public void rejectedExecution(Runnable r, ThreadPoolExecutor e) {
            // 抛出异常
            throw new RejectedExecutionException("Task " + r.toString() +
                                                 " rejected from " +
                                                 e.toString());
        }
    }
    ```

  - 四种拒绝处 理的策略为 ： 

  \1. **ThreadPoolExecutor.AbortPolicy**：默认拒绝处理策略，丢弃任务并抛 

  出RejectedExecutionException异常。 

  \2. **ThreadPoolExecutor.DiscardPolicy**：丢弃新来的任务，但是不抛出异 

  常。 

  \3. **ThreadPoolExecutor.DiscardOldestPolicy**：丢弃队列头部（最旧的） 

  的任务，然后重新尝试执⾏程序（如果再次失败，重复此过程）。 

  \4. **ThreadPoolExecutor.CallerRunsPolicy**：由调⽤线程处理该任务。



### execute()方法

![image-20201128015611905](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201128015611905.png)

> 执行任务的execute()方法的源码

```java
public void execute(Runnable command) {
        if (command == null)
            throw new NullPointerException();
    	// 获取当前线程池的状态和数量
        int c = ctl.get();
    // 如果工作线程数量 <  核心线程数量 创建新线程执行当前任务
        if (workerCountOf(c) < corePoolSize) {
            if (addWorker(command, true))
                return;
            // 再次获取 
            c = ctl.get();
        }
    
    // 若执行到这里，表示池中线程数量 >= corePoolSize，或者上面 addWorker 失败
    // 若线程池处于 RUNNING 状态，并且该任务（command）成功添加到任务队列
        if (isRunning(c) && workQueue.offer(command)) {
            //  // 再次获取 ctl 值
            int recheck = ctl.get();
            //  / 若线程池不是运行状态，则要把上面添加的任务从队列中移除并执行拒绝策略
            if (! isRunning(recheck) && remove(command))
                // // 执行拒绝策略     
                reject(command);
            // 若此时池中没有线程而线程池是running，则新建一个
            else if (workerCountOf(recheck) == 0)
                // 防止任务提交后，池中没有存活的线程
                addWorker(null, false);
        }
    // .如果放⼊workQueue失败，则创建⾮核⼼线程执⾏任务，
 // 如果这时创建⾮核⼼线程失败(当前线程总数不⼩于maximumPoolSize时)，就会执⾏拒绝策略。
        else if (!addWorker(command, false))
            reject(command);
    }
```

### 状态

> 主要字段和状态

![image-20201128021621282](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201128021621282.png)

```java
/**
ctl - 用于控制线程池的运行状态和线程池中的有效线程数量。它包含两部分的信息：
线程池的运行状态 (runState)
线程池内有效线程的数量 (workerCount)
可以看到，ctl 使用了 Integer 类型来保存，高 3 位保存 runState，低 29 位保存 workerCount。COUNT_BITS 就是 29，CAPACITY 就是 1 左移 29 位减 1（29 个 1），这个常量表示 workerCount 的上限值，大约是 5 亿。
*/
private final AtomicInteger ctl = new AtomicInteger(ctlOf(RUNNING, 0));
private static final int COUNT_BITS = Integer.SIZE - 3;
private static final int CAPACITY   = (1 << COUNT_BITS) - 1;
// runState is stored in the high-order bits
// RUNNING - 运行状态。接受新任务，并且也能处理阻塞队列中的任务。
private static final int RUNNING    = -1 << COUNT_BITS;
/**SHUTDOWN - 关闭状态。不接受新任务，但可以处理阻塞队列中的任务。
在线程池处于 RUNNING 状态时，调用 shutdown 方法会使线程池进入到该状态。
finalize 方法在执行过程中也会调用 shutdown 方法进入该状态。
*/
private static final int SHUTDOWN   =  0 << COUNT_BITS;
/**
停止状态。不接受新任务，也不处理队列中的任务。会中断正在处理任务的线程。在线程池处于 RUNNING 或 SHUTDOWN 状态时，调用 shutdownNow 方法会使线程池进入到该状态。
*/
private static final int STOP       =  1 << COUNT_BITS;
/**
TIDYING - 整理状态。如果所有的任务都已终止了，workerCount (有效线程数) 为 0，线程池进入该状态后会调用 terminated 方法进入 TERMINATED 状态。
*/
private static final int TIDYING    =  2 << COUNT_BITS;
/**TERMINATED - 已终止状态。在 terminated 方法执行完后进入该状态。默认 terminated 方法中什么也没有做。进入 TERMINATED 的条件如下：
线程池不是 RUNNING 状态；
线程池状态不是 TIDYING 状态或 TERMINATED 状态；
如果线程池状态是 SHUTDOWN 并且 workerQueue 为空；
workerCount 为 0；
设置 TIDYING 状态成功。
*/
private static final int TERMINATED =  3 << COUNT_BITS;
```

### 策略

> ThreadPoolExecutor策略

线程池本身有⼀个调度线程，这个线程就是⽤于管理布控整个线程池⾥的各种任务 

和事务，例如创建线程、销毁线程、任务队列管理、线程队列管理等等。 

故线程池也有⾃⼰的状态。 ThreadPoolExecutor 类中定义了⼀个 volatile int 变 

量**runState**来表示线程池的状态 ，分别为RUNNING、SHURDOWN、STOP、

TIDYING 、TERMINATED。 

```java
 // runState存储在高位
	//线程池创建后处于RUNNING状态。
    private static final int RUNNING    = -1 << COUNT_BITS;
//	调⽤shutdown()⽅法后处于SHUTDOWN状态，线程池不能接受新的任务，清 除⼀些空闲worker,会等待阻塞队列的任务完成。
    private static final int SHUTDOWN   =  0 << COUNT_BITS;
// 调⽤shutdownNow()⽅法后处于STOP状态，线程池不能接受新的任务，中断 所有线程，阻塞队列中没有被执⾏的任务全部丢弃。此时，poolsize=0,阻塞队 列的size也为0
    private static final int STOP       =  1 << COUNT_BITS;
//当所有的任务已终⽌，ctl记录的”任务数量”为0，线程池会变为TIDYING状态。 接着会执⾏terminated()函数。ThreadPoolExecutor中有⼀个控制状态的属性叫ctl，它是⼀个
//AtomicInteger类型的变量。
    private static final int TIDYING    =  2 << COUNT_BITS;
//线程池处在TIDYING状态时，执⾏完terminated()⽅法之后，就会由 TIDYING
-//> TERMINATED， 线程池被设置为TERMINATED状态。
    private static final int TERMINATED =  3 << COUNT_BITS;
```

### 关闭

> 线程池的关闭

关闭线程池，可以通过`shutdown`和`shutdownNow`这两个方法。它们的原理都是遍历线程池中所有的线程，然后依次中断线程。

- `shutdownNow`首先将线程池的状态设置为**STOP**,然后尝试**停止所有的正在执行和未执行任务**的线程，并返回等待执行任务的列表；
- `shutdown`只是将线程池的状态设置为**SHUTDOWN**状态，然后中断所有没有正在执行任务的线程

### 参数配置

> 配置线程池参数

1. 任务的性质：CPU密集型任务，IO密集型任务和混合型任务。
2. 任务的优先级：高，中和低。
3. 任务的执行时间：长，中和短。
4. 任务的依赖性：是否依赖其他系统资源，如数据库连接。

在HotSpot VM的线程模型中，Java线程（java.lang.Thread）被一对一映射为本地操作系统线 

程。Java线程启动时会创建一个本地操作系统线程；当该Java线程终止时，这个操作系统线程 

也会被回收。操作系统会调度所有线程并将它们分配给可用的CPU。 

任务性质不同的任务可以用不同规模的线程池分开处理。 CPU密集型任务配置尽可能少的线程数量，如配置Ncpu 1个线程的线程池。IO密集型任务则由于需要等待IO操作，线程并不是一直在执行任务，则配置尽可能多的线程，如2xNcpu。混合型的任务，如果可以拆分，则将其拆分成一个CPU密集型任务和一个IO密集型任务，只要这两个任务执行的时间相差不是太大，那么分解后执行的吞吐率要高于串行执行的吞吐率，如果这两个任务执行时间相差太大，则没必要进行分解。我们可以通过Runtime. getRuntime(). availableProcessors()方法获得当前设备的CPU个数。

优先级不同的任务可以使用优先级队列PriorityBlockingQueue来处理。它可以让优先级高 的任务先执行（如果一直有优先级高的任务提交到队列里，那么优先级低的任务可能永远不能 执行）

执行时间不同的任务可以交给不同规模的线程池来处理，或者可以使用优先级队列，让 执行时间短的任务先执行。 

依赖数据库连接池的任务，因为线程提交SQL后需要等待数据库返回结果，如果等待的时间越长CPU空闲时间就越长，那么线程数应该设置越大，这样才能更好的利用CPU。

并且，阻塞队列**最好是使用有界队列**，如果采用无界队列的话，一旦任务积压在阻塞队列中的话就会占用过多的内存资源，甚至会使得系统崩溃。

## ScheduledThreadPoolExecutor



### 是什么

> ScheduledThreadPoolExecutor可以用来在给定延时后执行异步任务或者周期性执行任务，相对于任务调度的Timer来说，其功能更加强大，Timer只能使用一个后台线程执行任务，而ScheduledThreadPoolExecutor则可以通过构造函数来指定后台线程的个数。

### 继承树

> 继承树

![image-20201128022921069](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201128022921069.png)

### 构造器

> 构造器

```java
/**
 * 构造器一：用给定的触发时间（纳秒），创建一个一次性任务
 */
ScheduledFutureTask(Runnable r, V result, long ns) {
    super(r, result);
    this.time = ns;
    this.period = 0;
    this.sequenceNumber = sequencer.getAndIncrement();
}

/**
 * 构造器二：用给定的触发时间和间隔（纳秒），创建一个周期性任务
 */
ScheduledFutureTask(Runnable r, V result, long ns, long period) {
    super(r, result);
    this.time = ns;
    this.period = period;
    this.sequenceNumber = sequencer.getAndIncrement();
}

/**
 * 构造器三：用给定的触发时间（纳秒），创建一个一次性任务
 */
ScheduledFutureTask(Callable<V> callable, long ns) {
    super(callable);
    this.time = ns;
    this.period = 0;
    this.sequenceNumber = sequencer.getAndIncrement();
}
```

### 主要成员变量

> 主要方法和变量

```java
// 定时任务执行的时间（单位：纳秒）
private long time;

/**
 * 重复执行的任务的时间间隔（单位：纳秒） 
 * 正数表示固定频率（fixed-rate）执行
 * 负数表示固定延迟（fixed-delay）执行
 * 零表示非重复执行的任务
 */
private final long period;

// reExecutePeriodic 方法中重新排队的任务
RunnableScheduledFuture<V> outerTask = this;

// 延迟队列中的索引位置，便于快速取消
int heapIndex;
```

### 任务传递

> ScheduledThreadPoolExecutor的任务传递

![image-20201128023423539](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201128023423539.png)



### 任务执行

> 任务执行流程
>
> ScheduledFutureTask 任务
>
> DelayedWorkQueue 和阻塞队列

![image-20201128023817265](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201128023817265.png)

### 任务获取

> DelayedWorkQueue获取任务

```java
  public RunnableScheduledFuture<?> take() throws InterruptedException {
            final ReentrantLock lock = this.lock;
      //  // 获取锁
            lock.lockInterruptibly();
            try {
                // 直到线程从PriorityQueue获取到一 个元素之后 （获取PriorityQueue的头元素），才会退出无限循环
                for (;;) {
                    RunnableScheduledFuture<?> first = queue[0];
                    if (first == null)
                        // 如果PriorityQueue为空，当前线程到Condition中等待
                        available.await();
                    else {
                        //·如果PriorityQueue的头元素的time时间比当前时间大，到Condition中等待到time时间
                        long delay = first.getDelay(NANOSECONDS);
                        if (delay <= 0)
                         	// 获取PriorityQueue的头元素
                            return finishPoll(first);
                        first = null; // don't retain ref while waiting
                        if (leader != null)
                            available.await();
                        else {
                            Thread thisThread = Thread.currentThread();
                            leader = thisThread;
                            try {
                                available.awaitNanos(delay);
                            } finally {
                                if (leader == thisThread)
                                    leader = null;
                            }
                        }
                    }
                }
            } finally {
                if (leader == null && queue[0] != null)
                    // 唤醒在Condition中等待的线程
                    available.signal();
                //释放Lock。
                lock.unlock();
            }
        }
```

### 任务添加

> 添加任务

```java
public boolean offer(Runnable x) {
    if (x == null)
        throw new NullPointerException();
    RunnableScheduledFuture<?> e = (RunnableScheduledFuture<?>)x;
    final ReentrantLock lock = this.lock;
    lock.lock(); // 获取锁
    try {
        int i = size;
        if (i >= queue.length)
            grow();
        size = i + 1;
        if (i == 0) {
            queue[0] = e;
            setIndex(e, 0);
        } else {
            siftUp(i, e);
        }
        if (queue[0] == e) {
            leader = null;
            available.signal();
        }
    } finally {
        lock.unlock();
    }
    return true;
}
```

### 主要方法分析



#### schedule

> 主要方法schedule方法分析： 作用是延迟指定的时间后执行任务（即执行定时任务），只会执行一次

```java
public ScheduledFuture<?> schedule(Runnable command,
                                       long delay,
                                       TimeUnit unit) {
        if (command == null || unit == null)
            throw new NullPointerException();
    // 把用户提交的 Runnable 对象包装为 RunnableScheduledFuture 对象
    //  decorateTask 方法默认返回第二个参数 
        RunnableScheduledFuture<?> t = decorateTask(command,
            new ScheduledFutureTask<Void>(command, null,
                                          triggerTime(delay, unit)));
    // 执行给定的任务   
    delayedExecute(t);
        return t;
    }

protected <V> RunnableScheduledFuture<V> decorateTask(
        Runnable runnable, RunnableScheduledFuture<V> task) {
        return task;
    }
```

```java
// 方法与前者类似，差别在于这里的参数类型是 Callable，前者是 Runnable 类型，其他操作一样。
public <V> ScheduledFuture<V> schedule(Callable<V> callable,
                                       long delay,
                                       TimeUnit unit) {
    if (callable == null || unit == null)
        throw new NullPointerException();
    RunnableScheduledFuture<V> t = decorateTask(callable,
        new ScheduledFutureTask<V>(callable,
                                   triggerTime(delay, unit)));
    delayedExecute(t);
    return t;
}

// 区别仅在于使用了不同的 ScheduledFutureTask 对象，其他的执行流程几乎一样。
public ScheduledFuture<?> scheduleAtFixedRate(Runnable command,
                                              long initialDelay,
                                              long period,
                                              TimeUnit unit) {
    if (command == null || unit == null)
        throw new NullPointerException();
    if (period <= 0)
        throw new IllegalArgumentException();
    // 将 Runnable 对象包装为 ScheduledFutureTask 对象
    ScheduledFutureTask<Void> sft =
        new ScheduledFutureTask<Void>(command,
                                      null,
                                      triggerTime(initialDelay, unit),
                                      unit.toNanos(period));
    RunnableScheduledFuture<Void> t = decorateTask(command, sft);
    sft.outerTask = t;
    delayedExecute(t);
    return t;
}
```



#### delayedExecute 

> delayedExecute 执行任务方法

```java
/*
 * 延迟或周期性任务的主要执行方法。
 * 若线程池已关闭，则拒绝该任务（执行拒绝策略）；
 * 否则将任务添加到工作队列，若有需要启动一个线程去执行。
 * 若在添加任务时关闭了线程池，则将其从队列移除并取消该任务
 */
private void delayedExecute(RunnableScheduledFuture<?> task) {
        if (isShutdown())
            reject(task);// 如果当前线程池已经关闭，执行拒绝策略
        else {
            // 把任务放人阻塞队列中  即延迟队列
            super.getQueue().add(task);
            // 若当前任务无法执行，则将其从队列移除并且取消执行（类似事务的回滚操作）
            if (isShutdown() &&
                !canRunInCurrentRunState(task.isPeriodic()) &&
                remove(task))
                task.cancel(false);
             // 任务可以执行，若有需要则新增线程以执行该任务，
            else
                ensurePrestart();
        }
    }

// //保证至少有一个线程启动，即使corePoolSize=0
 void ensurePrestart() {
        int wc = workerCountOf(ctl.get());
        if (wc < corePoolSize)
            // 新建Worker类，当执行任务时，就会调用被Worker所重写的run方法，进而会继续执行runWorker方法。
              // 在runWorker方法中会调用getTask方法从阻塞队列中不断的去获取任务进行执行，直到从阻塞队列中获取的任务为null的话，线程结束终止。
            addWorker(null, true);
        else if (wc == 0)
          
            addWorker(null, false);
    }
```

### executor & submit 

```java
public void execute(Runnable command) {
    schedule(command, 0, NANOSECONDS);
}

public Future<?> submit(Runnable task) {
    return schedule(task, 0, NANOSECONDS);
}
```

### 总结



- 提供了执行定时任务的 schedule 方法和周期性任务的 scheduleAtFixedRate/scheduleWithFixedDelay 方法（使用延迟队列实现）。
- 三个要素
  - **执行者**：线程池获取任务执行 ，先将任务移入到阻塞队列中，然后通过addWork方法新建了Work类，并通过runWorker方法启动线程，并不断的从阻塞对列中获取异步任务执行交给Worker执行，直至阻塞队列中无法取到任务为止。
  - **任务**：ThreadPoolExecutor中会将任务转换成FutureTask类，而在ScheduledThreadPoolExecutor中为了实现可延时执行任务和周期性执行任务的特性，任务会被转换成ScheduledFutureTask类，该类继承了FutureTask，并重写了run方法。
  - **任务结果**：在ThreadPoolExecutor中提交任务后，获取任务结果可以通过Future接口的类，在ThreadPoolExecutor中实际上为FutureTask类，而在ScheduledThreadPoolExecutor中则是ScheduledFutureTask类

## FutureTask



### 是什么

> FutureTask用来表示可获取结果的异步任务。
>
> FutureTask提供了启动和取消异步任务，查询异步任务是否计算结束以及获取最终的异步任务的结果的一些常用的方法。
>
> FutureTask除了实现Future接口外，还实现了Runnable接口。因此，FutureTask可以交给 Executor执行，也可以由调用线程直接执行（FutureTask.run()）。



### 继承树

> ![image-20201128225719947](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201128225719947.png)

根据类继承关系知道

FutureTask 的主要功能：

1. 异步执行任务，并且任务只执行一次；
2. 监控任务是否完成、取消任务；
3. 获取任务执行结果。



### 状态



> 7种状态值

```java
 private volatile int state;
    private static final int NEW          = 0;
    private static final int COMPLETING   = 1;
    private static final int NORMAL       = 2;
    private static final int EXCEPTIONAL  = 3;
    private static final int CANCELLED    = 4;
    private static final int INTERRUPTING = 5;
    private static final int INTERRUPTED  = 6;
```

> 4种状态转换

1. 任务执行正常：NEW -> COMPLETING -> NORMAL
2. 任务执行异常：NEW -> COMPLETING -> EXCEPTIONAL
3. 任务取消：NEW -> CANCELLED
4. 任务中断：NEW -> INTERRUPTING -> INTERRUPTED

![image-20201128230444885](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201128230444885.png)

> 三种run方法执行状态

- 1.未启动：未创建FutureTask。创建了FutureTask但是还没有执行run方法
- 2.已启动：run执行时 是已启动
- 3.已完成，FutureTask.run()方法执行完后正常结束，或被取消（FutureTask.cancel（…）），或 执行FutureTask.run()方法时抛出异常而异常结束，FutureTask处于已完成状态。 

![image-20201128230535535](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201128230535535.png)



### 内部类 WaitNode

> WaitNode单链表组成的线程安全的栈 ,用来在栈记录等待线程中的状态信息

> 声明

```java
static final class WaitNode {
    volatile Thread thread;
    volatile WaitNode next;
    WaitNode() { thread = Thread.currentThread(); }
}
```

> 成员变量

```java
/** The underlying callable; nulled out after running */
// 提交的任务
private Callable<V> callable;

/** The result to return or exception to throw from get() */
// get() 方法返回的结果（或者异常）
private Object outcome; // non-volatile, protected by state reads/writes

/** The thread running the callable; CASed during run() */
// 执行任务的线程
private volatile Thread runner;

/** Treiber stack of waiting threads */
// 等待线程的 Treiber 栈
private volatile WaitNode waiters;
```



### 主要方法

```java
/*
 * 尝试取消执行任务。若任务已完成、已取消，或者由于其他某些原因无法取消，则尝试失败。
 * 若成功，且调用该方法时任务未启动，则此任务不会再运行；
 * 若任务已启动，则根据参数 mayInterruptIfRunning 决定是否中断该任务。
 */
boolean cancel(boolean mayInterruptIfRunning);

// 若该任务正常结束之前被取消，则返回 true
boolean isCancelled();

/*
 * 若该任务已完成，则返回 true
 * 这里的“完成”，可能是由于正常终止、异常，或者取消，这些情况都返回 true
 */
boolean isDone();

// 等待计算完成（如果需要），然后获取结果
V get() throws InterruptedException, ExecutionException;

// 如果需要，最多等待计算完成的给定时间，然后检索其结果（如果可用）
// PS: 该方法与前者的区别在于加了超时等待
V get(long timeout, TimeUnit unit)
    throws InterruptedException, ExecutionException, TimeoutException;

// 等待任务执行完成
private int awaitDone(boolean timed, long nanos)
```



> get  和cancel方法  执行流程

![image-20201128230755159](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201128230755159.png)



> run方法

```java
public void run() {
    // 使用CAS 并发安全空中，防止任务重复执行
    if (state != NEW ||
        !UNSAFE.compareAndSwapObject(this, runnerOffset,
                                     null, Thread.currentThread()))
        return;
    try {
        Callable<V> c = callable;
        if (c != null && state == NEW) {
            V result;
            boolean ran;
            try {
                // 调用call方法执行任务
                result = c.call();
                ran = true;
            } catch (Throwable ex) {
                // 处理异常
                result = null;
                ran = false;
                setException(ex);
            }
            if (ran)
                set(result);
        }
    } finally {
        // runner must be non-null until state is settled to
        // prevent concurrent calls to run()
        runner = null;
        // state must be re-read after nulling runner to prevent
        // leaked interrupts
        int s = state;
        // 线程中断
        if (s >= INTERRUPTING)
            handlePossibleCancellationInterrupt(s);
    }
}
```

> set 和setException方法

```java
 protected void set(V v) {
 // // CAS 将 state 修改为 COMPLETING，该状态是一个中间状态
        if (UNSAFE.compareAndSwapInt(this, stateOffset, NEW, COMPLETING)) {
            outcome = v;
            //  // 将 state 更新为 NORMAL
            UNSAFE.putOrderedInt(this, stateOffset, NORMAL); // final state
            finishCompletion();
        }
    }
    
     protected void setException(Throwable t) {
         //  // CAS 将 state 修改为 COMPLETING，该状态是一个中间状态    
        if (UNSAFE.compareAndSwapInt(this, stateOffset, NEW, COMPLETING)) {
            outcome = t;
             // 将 state 更新为 EXCEPTIONAL        
            UNSAFE.putOrderedInt(this, stateOffset, EXCEPTIONAL); // final state
            finishCompletion();
        }
    }
```



> finshCompletion方法 执行 结束操作 ，唤醒栈中所有等待的线程，并清空栈

```java
private void finishCompletion() {
    // assert state > COMPLETING;
    for (WaitNode q; (q = waiters) != null;) {
        // 将 waiters 置空
        if (UNSAFE.compareAndSwapObject(this, waitersOffset, q, null)) {
            for (;;) {
                Thread t = q.thread;
                if (t != null) {
                    q.thread = null;
                    // 唤醒 WaitNode 封装的线程
                    LockSupport.unpark(t);
                }
                WaitNode next = q.next;
                if (next == null)
                    break;
                q.next = null; // unlink to help gc
                q = next;
            }
            break;
        }
    }

    done();

    callable = null;        // to reduce footprint
}
```



> get方法: 获取执行结果

```java
public V get() throws InterruptedException, ExecutionException {
    int s = state;
    if (s <= COMPLETING)
        s = awaitDone(false, 0L);
    return report(s);
}


public V get(long timeout, TimeUnit unit)
    throws InterruptedException, ExecutionException, TimeoutException {
    if (unit == null)
        throw new NullPointerException();
    int s = state;
    if (s <= COMPLETING &&
        (s = awaitDone(true, unit.toNanos(timeout))) <= COMPLETING)
        throw new TimeoutException();
    return report(s);
}
```



> awaitDone: 等待任务执行完成
>
> 1. 若线程被中断，则响应中断；
> 2. 若任务已完成，则返回状态值；
> 3. 若任务正在执行，则让出 CPU 时间片；
> 4. 若任务未执行，则将当前线程封装为 WaitNode 节点；
> 5. 若 WaitNode 未入栈，则执行入栈；
> 6. 若已入栈，则将线程挂起。

```java
private int awaitDone(boolean timed, long nanos)
    throws InterruptedException {
    final long deadline = timed ? System.nanoTime() + nanos : 0L;
    WaitNode q = null;
    boolean queued = false;
    for (;;) {
        // 响应线程中断
        if (Thread.interrupted()) {
            removeWaiter(q);
            throw new InterruptedException();
        }

        int s = state;
        // s > COMPLETING 表示任务已执行完成（包括正常执行、异常等状态）
        // 则返回对应的状态值
        if (s > COMPLETING) {
            if (q != null)
                q.thread = null;
            return s;
        }
        // s == COMPLETING 是一个中间状态，表示任务尚未完成
        // 这里让出 CPU 时间片
        else if (s == COMPLETING) // cannot time out yet
            Thread.yield();
        // 执行到这里，表示 s == NEW，将当前线程封装为一个 WaitNode 节点
        else if (q == null)
            q = new WaitNode();
        // 这里表示 q 并未入栈，CAS 方式将当 WaitNode 入栈
        else if (!queued)
            queued = UNSAFE.compareAndSwapObject(this, waitersOffset,
                                                 q.next = waiters, q);
        // 有超时的情况
        else if (timed) {
            nanos = deadline - System.nanoTime();
            if (nanos <= 0L) {
                removeWaiter(q);
                return state;
            }
            LockSupport.parkNanos(this, nanos);
        }
        // 将当前线程挂起
        else
            LockSupport.park(this);
    }
}
```

> cancel: 取消任务
>
> 未启动状态调用：该任务永远不会再执行
>
> 已启动状态： 根据参数是否中断当前执行任务的线程
>
> 已结束状态： 调用方法世返回false

```java
public boolean cancel(boolean mayInterruptIfRunning) {
    if (!(state == NEW &&
          UNSAFE.compareAndSwapInt(this, stateOffset, NEW,
              mayInterruptIfRunning ? INTERRUPTING : CANCELLED)))
        return false;
    try {    // in case call to interrupt throws exception
        if (mayInterruptIfRunning) {
            try {
                // 若允许中断，则尝试中断线程
                Thread t = runner;
                if (t != null)
                    t.interrupt();
            } finally { // final state
                UNSAFE.putOrderedInt(this, stateOffset, INTERRUPTED);
            }
        }
    } finally {
        finishCompletion();
    }
    return true;
}
```

> report 方法：封装返回结果

```java
private V report(int s) throws ExecutionException {
    Object x = outcome; // 输出结果赋值
    // 正常结束
    if (s == NORMAL)
        return (V)x;
    // 取消
    if (s >= CANCELLED)
        throw new CancellationException();
    // 执行异常
    throw new ExecutionException((Throwable)x);
}
```



### 使用

```java
public class Pool1 {

    public static void main(String[] args) throws Exception {
        ExecutorService service = Executors.newFixedThreadPool(5);
        ArrayList<FutureTask<Integer>> taskList = new ArrayList<>();
        for (int i = 0; i < 6; i++) {
            int finali = i;
            FutureTask<Integer> futureTask = new FutureTask<>(() -> {
                TimeUnit.SECONDS.sleep(3);
                return finali;
            });
            taskList.add(futureTask);
            service.submit(futureTask); //提交到线程池
        }
        System.out.println("已全部提交任务，执行操作");
        for (FutureTask<Integer> futureTask1 : taskList) {
            Integer result = futureTask1.get();
            System.out.println("====" + result);
        }
        // 关闭线程池
        service.shutdown();
    }
}
//
已全部提交任务，执行操作
====0
====1
====2
====3
====4            ---- 4  和 5 之间 隔了三秒 因为线程池是5
====5              
```

### 场景



FutureTask 是一个封装任务（Runnable 或 Callable）的类，可以异步执行任务，并获取执行结果，适用于耗时操作场景。

## Executors



### 是什么

> JDK 的 `Executors` 类中提供了几种具有代表性的线程池，这些线程池 **都是基于 `ThreadPoolExecutor` 的定制化实现**。
>
> 在实际使用线程池的场景中，我们往往不是直接使用 `ThreadPoolExecutor` ，而是使用 JDK 中提供的具有代表性的线程池实例。
>
> Executors 类中提供的⼏个静态⽅法来创建线程池。

### 1.**newCachedThreadPool**

> **创建一个可缓存的线程池**。
>
> - 如果线程池大小超过处理任务所需要的线程数，就会回收部分空闲的线程；
> - 如果长时间没有往线程池中提交任务，即如果工作线程空闲了指定的时间（默认为 1 分钟），则该工作线程将自动终止。终止后，如果你又提交了新的任务，则线程池重新创建一个工作线程。
> - 此线程池不会对线程池大小做限制，线程池大小完全依赖于操作系统（或者说 JVM）能够创建的最大线程大小。 因此，使用 `CachedThreadPool` 时，一定要注意控制任务的数量，否则，由于大量线程同时运行，很有会造成系统瘫痪。



```java
public static ExecutorService newCachedThreadPool() {
 return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
 60L, TimeUnit.SECONDS,
 new SynchronousQueue<Runnable>());
}
```



> 使用

```java
public class Poo2 {
    public static void main(String[] args) {
        ExecutorService newCachedThreadPool = Executors.newCachedThreadPool();
        for (int i =0;i <10;i++){
            newCachedThreadPool.execute(new Runnable() {
                @Override
                public void run() {
                    System.out.println(Thread.currentThread().getName()+"+++时间:"+System.currentTimeMillis());
                }
            });
        }
        newCachedThreadPool.shutdown();
    }
}
//
pool-1-thread-2+++时间:1606579527589
pool-1-thread-1+++时间:1606579527590
pool-1-thread-2+++时间:1606579527590
pool-1-thread-3+++时间:1606579527590
pool-1-thread-4+++时间:1606579527590
pool-1-thread-5+++时间:1606579527591
pool-1-thread-9+++时间:1606579527591
pool-1-thread-8+++时间:1606579527591
pool-1-thread-7+++时间:1606579527591
pool-1-thread-6+++时间:1606579527591
```



> 执行流程

CacheThreadPool 的运⾏流程如下：

\1. 提交任务进线程池。 

\2. 因为**corePoolSize**为0的关系，不创建核⼼线程，线程池最⼤为

Integer.MAX_VALUE。 

\3. 尝试将任务添加到**SynchronousQueue**队列。 

\4. 如果SynchronousQueue⼊列成功，等待被当前运⾏的线程空闲后拉取执⾏。 

如果当前没有空闲线程，那么就创建⼀个⾮核⼼线程，然后从

SynchronousQueue拉取任务并在当前线程执⾏。 

\5. 如果SynchronousQueue已有任务在等待，⼊列操作将会阻塞。 

当需要执⾏很多短时间的任务时，CacheThreadPool的线程复⽤率⽐较⾼， 会显 

著的提⾼性能。⽽且线程60s后会回收，意味着即使没有任务进来，

CacheThreadPool并不会占⽤很多资源。



### 2.**newFixedThreadPool**



> **创建一个固定大小的线程池**。
>
> **每次提交一个任务就会新创建一个工作线程，如果工作线程数量达到线程池最大线程数，则将提交的任务存入到阻塞队列中**。
>
> `FixedThreadPool` 是一个典型且优秀的线程池，它具有线程池提高程序效率和节省创建线程时所耗的开销的优点。但是，在线程池空闲时，即线程池中没有可运行任务时，它不会释放工作线程，还会占用一定的系统资源。

```java
public static ExecutorService newFixedThreadPool(int nThreads) {
 return new ThreadPoolExecutor(nThreads, nThreads,
 0L, TimeUnit.MILLISECONDS,
 new LinkedBlockingQueue<Runnable>());
}
```

> 核⼼线程数量和总线程数量相等，都是传⼊的参数nThreads，所以只能创建核⼼线 程，不能创建⾮核⼼线程。因为LinkedBlockingQueue的默认⼤⼩是Integer.MAX_VALUE，故如果核⼼线程空闲，则交给核⼼线程处理；如果核⼼线程 不空闲，则⼊列等待，直到核⼼线程空闲。 

> 使用

```java
public class Pool3 {
    public static void main(String[] args) {
        ExecutorService service = Executors.newFixedThreadPool(3);
        for(int i = 0; i<10;i++) {
            service.execute(new Runnable() {
                @Override
                public void run() {
                    System.out.println(Thread.currentThread().getName() + "+name:" + System.currentTimeMillis());
                }
            });
        }
            service.shutdown();
    }
}
//
pool-1-thread-1+name:1606579968767
pool-1-thread-2+name:1606579968767
pool-1-thread-3+name:1606579968767
pool-1-thread-2+name:1606579968767
pool-1-thread-1+name:1606579968767
pool-1-thread-3+name:1606579968767
pool-1-thread-2+name:1606579968767
pool-1-thread-1+name:1606579968768
pool-1-thread-3+name:1606579968768
pool-1-thread-2+name:1606579968768
```



### 缓存和固定的区别



- 因为 corePoolSize == maximumPoolSize ，所以FixedThreadPool只会创建核 ⼼线程。 ⽽CachedThreadPool因为corePoolSize=0，所以只会创建⾮核⼼线 程。

- 在 getTask() ⽅法，如果队列⾥没有任务可取，线程会⼀直阻塞在LinkedBlockingQueue.take() ，线程不会被回收。 CachedThreadPool会在60s后收回（默认一分钟）

- 由于线程不会被回收，会⼀直卡在阻塞，所以没有任务的情况下，**FixedThreadPool**占⽤资源更多。 

- 都⼏乎不会触发拒绝策略，但是原理不同。FixedThreadPool是因为阻塞队列 可以很⼤（最⼤为Integer最⼤值），故⼏乎不会触发拒绝策略；CachedThreadPool是因为线程池很⼤（最⼤为Integer最⼤值），⼏乎不会导 致线程数量⼤于最⼤线程数，故⼏乎不会触发拒绝策略。

### 3. **newSingleThreadExecutor**



> **创建一个单线程的线程池**。
>
> 只会创建唯一的工作线程来执行任务，保证所有任务按照指定顺序(FIFO, LIFO, 优先级)执行。 **如果这个唯一的线程因为异常结束，那么会有一个新的线程来替代它** 。
>
> 单工作线程最大的特点是：**可保证顺序地执行各个任务**。

```java
public static ExecutorService newSingleThreadExecutor() {
 return new FinalizableDelegatedExecutorService
 (new ThreadPoolExecutor(1, 1,
 0L, TimeUnit.MILLISECONDS,
 new LinkedBlockingQueue<Runnable>()));
}
```

有且仅有⼀个核⼼线程（ corePoolSize == maximumPoolSize=1），使⽤了LinkedBlockingQueue（容量很⼤），所以，不会创建⾮核⼼线程。所有任务按照 先来先执⾏的顺序执⾏。如果这个唯⼀的线程不空闲，那么新来的任务会存储在任务队列⾥等待执⾏。

> 使用

```java

    public static void main(String[] args) {
        ExecutorService executorService = Executors.newSingleThreadExecutor();
        for (int i = 0; i < 100; i++) {
            executorService.execute(new Runnable() {
                @Override
                public void run() {
                    System.out.println(Thread.currentThread().getName() + " 执行");
                }
            });
        }
        executorService.shutdown();
    }


```



### 4.newScheduleThreadPool

> 创建⼀个定⻓线程池，⽀持定时及周期性任务执⾏

```java
public static ScheduledExecutorService newScheduledThreadPool(int corePoolSize
 return new ScheduledThreadPoolExecutor(corePoolSize);
}
//ScheduledThreadPoolExecutor():
public ScheduledThreadPoolExecutor(int corePoolSize) {
 super(corePoolSize, Integer.MAX_VALUE,
 DEFAULT_KEEPALIVE_MILLIS, MILLISECONDS,
 new DelayedWorkQueue());
}
```



> 使用

```java
     private static void schedule () {
        ScheduledExecutorService executorService = Executors.newScheduledThreadPool(5);
        for (int i = 0; i < 100; i++) {
            executorService.schedule(new Runnable() {
                @Override
                public void run() {
                    System.out.println(Thread.currentThread().getName() + " 执行");
                }
            }, 1, TimeUnit.SECONDS);
        }
        executorService.shutdown();
    }
```



### 5. newWorkStealingPool

其内部会构建 `ForkJoinPool`，利用 [Work-Stealing](https://en.wikipedia.org/wiki/Work_stealing) 算法，并行地处理任务，不保证处理顺序。



### 建议



四种常⻅的线程池基本够我们使⽤了，但是《阿⾥把把开发⼿册》不建议我们直接 

使⽤Executors类中的线程池，⽽是通过 ThreadPoolExecutor 的⽅式，这样的处理 

⽅式让写的同学需要更加明确线程池的运⾏规则，规避资源耗尽的⻛险。 



