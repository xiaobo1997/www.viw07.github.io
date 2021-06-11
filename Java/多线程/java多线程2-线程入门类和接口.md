[TOC]

# 1. thread in java



##  thread 类和Runnable接口



**需要注意的是:**

​	我们通过JDK通过的Thread类和Runnable接口来实现多线程

- 继承 `Thread类` 重写 `run`方法(实质上也是实现了 Runable接口)

  - ```java
    public class Thread implements Runnable
    ```

- 实现 `Runnable`接口 `run`方法

  - ```java
    @FunctionalInterface
    public interface Runnable
    ```

    





### thread

> Java提供Thread类来实现线程编程。 Thread类提供了构造函数和方法来在线程上创建和执行操作。 线程类扩展了Object类并实现了Runnable接口。

> 类的声明和继承树
>
> - Runnable
>   - implements->Thread

```java
public class Thread implements Runnable
```



> 构造函数

- Thread()
- Thread(String name)--- 创建一个新thread对象，name为thread 名字
- Thread(Runnable r)--创建一个新thread对象，启动时调用r的run方法 执行
- Thread(Runnable r,String name)--- 创建一个新thread对象，name为thread 名字， 启动时调用r的run方法 执行



> method
>
> 当然用不了这么多，会经常用的就行了，需要再翻一翻

| 1)   | void                                   | [start()](https://www.javatpoint.com/java-thread-start-method) | 用于启动线程的执行。                                         |
| ---- | -------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2)   | void                                   | [run()](https://www.javatpoint.com/java-thread-run-method)   | 用于对线程执行操作，也就是执行体                             |
| 3)   | static void                            | [sleep()](https://www.javatpoint.com/java-thread-sleep-method) | 使线程休眠指定的时间。                                       |
| 4)   | static Thread                          | [currentThread()](https://www.javatpoint.com/java-thread-currentthread-method) | 返回对当前正在执行的线程对象的引用。                         |
| 5)   | void                                   | [join()](https://www.javatpoint.com/java-thread-join-method) | 等待线程死亡。内部调用的是Object类的wait方法实现的；         |
| 6)   | int                                    | [getPriority()](https://www.javatpoint.com/java-thread-getpriority-method) | 返回线程的优先级。                                           |
| 7)   | void                                   | [setPriority()](https://www.javatpoint.com/java-thread-setpriority-method) | 更改线程的优先级。                                           |
| 8)   | String                                 | [getName()](https://www.javatpoint.com/java-thread-getname-method) | 返回线程的名称。                                             |
| 9)   | void                                   | [setName()](https://www.javatpoint.com/java-thread-setname-method) | 改变线程的名称。                                             |
| 10)  | long                                   | [getId()](https://www.javatpoint.com/java-thread-getid-method) | 返回线程的ID                                                 |
| 11)  | boolean                                | [isAlive()](https://www.javatpoint.com/java-thread-isalive-method) | 测试线程是否还活着。                                         |
| 12)  | static void                            | [yield()](https://www.javatpoint.com/java-thread-yield-method) | 使当前正在执行的线程对象暂停，并允许其他线程临时执行。       |
| 13)  | void                                   | [suspend()](https://www.javatpoint.com/java-thread-suspend-method) | 用于挂起线程。                                               |
| 14)  | void                                   | [resume()](https://www.javatpoint.com/java-thread-resume-method) | 恢复挂起的线程。                                             |
| 15)  | void                                   | [stop()](https://www.javatpoint.com/java-thread-stop-method) | 停止线程。                                                   |
| 16)  | void                                   | [destroy()](https://www.javatpoint.com/java-thread-destroy-method) | 销毁线程组及其所有子组。                                     |
| 17)  | boolean                                | [isDaemon()](https://www.javatpoint.com/java-thread-isdaemon-method) | 测试线程是否是守护线程。                                     |
| 18)  | void                                   | [setDaemon()](https://www.javatpoint.com/java-thread-setdaemon-method) | 将线程标记为守护程序或用户线程。                             |
| 19)  | void                                   | [interrupt()](https://www.javatpoint.com/java-thread-interrupt-method) | 中断线程                                                     |
| 20)  | boolean                                | [isinterrupted()](https://www.javatpoint.com/java-thread-isinterrupted-method) | 测试线程是否已被中断。                                       |
| 21)  | static boolean                         | [interrupted()](https://www.javatpoint.com/java-thread-interrupted-method) | 测试是否已中断当前线程。                                     |
| 22)  | static int                             | [activeCount()](https://www.javatpoint.com/java-thread-activecount-method) | 返回当前线程的线程组中活动线程的数量。                       |
| 23)  | void                                   | [checkAccess()](https://www.javatpoint.com/java-thread-checkaccess-method) | 确定当前正在运行的线程是否有权修改该线程。                   |
| 24)  | static boolean                         | [holdLock()](https://www.javatpoint.com/java-thread-holdlock-method) | 当且仅当当前线程在指定对象上持有监视器锁时，它才返回true。   |
| 25)  | static void                            | [dumpStack()](https://www.javatpoint.com/java-thread-dumpstack-method) | 用于将当前线程的堆栈跟踪记录打印到标准错误流。               |
| 26)  | StackTraceElement[]                    | [getStackTrace()](https://www.javatpoint.com/java-thread-getstacktrace-method) | 返回一个表示线程堆栈转储的堆栈跟踪元素数组。                 |
| 27)  | static int                             | [enumerate()](https://www.javatpoint.com/java-thread-enumerate-method) | 用于将每个活动线程的线程组及其子组复制到指定的数组中。       |
| 28)  | Thread.State                           | [getState()](https://www.javatpoint.com/java-thread-getstate-method) | 用于返回线程的状态。                                         |
| 29)  | ThreadGroup                            | [getThreadGroup()](https://www.javatpoint.com/java-thread-getthreadgroup-method) | 用于返回该线程所属的线程组                                   |
| 30)  | String                                 | [toString()](https://www.javatpoint.com/java-thread-tostring-method) | 用于返回此线程的字符串表示形式，包括线程的名称，优先级和线程组。 |
| 31)  | void                                   | [notify()](https://www.javatpoint.com/java-thread-notify-method) | 仅用于通知正在等待特定对象的一个线程。                       |
| 32)  | void                                   | [notifyAll()](https://www.javatpoint.com/java-thread-notifyall-method) | 用于向特定对象的所有等待线程发出通知。                       |
| 33)  | void                                   | [setContextClassLoader()](https://www.javatpoint.com/java-thread-setcontextclassloader-method) | 设置线程的上下文类加载器。                                   |
| 34)  | ClassLoader                            | [getContextClassLoader()](https://www.javatpoint.com/java-thread-getcontextclassloader-method) | 返回线程的上下文类加载器。                                   |
| 35)  | static Thread.UncaughtExceptionHandler | [getDefaultUncaughtExceptionHandler()](https://www.javatpoint.com/java-thread-getdefaultuncaughtexceptionhandler-method) | 当线程由于未捕获的异常突然终止时，它将返回调用的默认处理程序。 |
| 36)  | static void                            | [setDefaultUncaughtExceptionHandler()](https://www.javatpoint.com/java-thread-setdefaultuncaughtexceptionhandler-method) | 设置当线程由于未捕获的异常突然终止时调用的默认处理程序。     |



> 常用的

1. **public void run():** 执行线程
2. **public void start():** 开始执行线程。JVM调用线程run（）方法。
3. **public void sleep(long miliseconds):**使当前正在执行的线程进入休眠状态（暂时停止执行）达指定的毫秒数。
4. **public void join():** 等待线程死亡。
5. **public void join(long miliseconds):** 等待线程死亡并指定毫秒数。
6. **public int getPriority():** returns the priority of the thread.
7. **public int setPriority(int priority):** changes the priority of the thread.
8. **public String getName():** returns the name of the thread.
9. **public void setName(String name):** changes the name of the thread.
10. **public Thread currentThread():** 返回当前正在执行的线程的引用。
11. **public int getId():** returns the id of the thread.
12. **public Thread.State getState():** 返回线程的状态。
13. **public boolean isAlive():** tests if the thread is alive.
14. **public void yield():** 使当前正在执行的线程对象暂时暂停并允许其他线程执行。
15. **public void suspend():** 是用来中止线程
16. **public void resume():** 是用来恢复挂起的线程
17. **public void stop():** 停止线程
18. **public boolean isDaemon():** tests if the thread is a daemon thread.
19. **public void setDaemon(boolean b):** marks the thread as daemon or user thread.
20. **public void interrupt():** 中断线程
21. **public boolean isInterrupted():** 测试线程是否已被中断。
22. **public static boolean interrupted():** 测试当前线程是否已被中断。



> example--01
>
> first demo create thread

```java
package java8.multithreading.thread;

/**
 * @Author: xiaobo
 * @Date: 2020/11/16 0:04
 */
public class thread01 {
    private static class MyThread01 extends Thread {
        @Override
        public void run() {
            System.out.println("执行线程run方法。。。");
        }
    }
    public static void main(String[] args) {
        MyThread01 myThread01 = new MyThread01();
        myThread01.start();
    }
}
//
执行线程run方法。。。
```

==总结：需要调用start方法启动线程。虚拟机为我们创建一个线程，得到该线程得到了时间分片再调用run()方法==



#### 思考

- 如何通过两个线程执行两个任务？
- 如何通过匿名类执行多线程？
- 线程调度程序是什么？
- 抢占式调度和时间分片之间的区别是什么？
- 如果两次启动一个线程会发生什么？
  - 会发生异常
- 如果我们调用run（）方法而不是start（）方法会怎样？
- join方法的目的是什么？
- 如果没有剩余用户线程，为什么JVM终止守护程序线程？
- 关闭钩子(shutdown hook)是什么？
- 垃圾回收是什么？
- finalize（）的目的是什么 
- 什么是gc（）方法？
- 什么是同步？
- 为什么要使用同步？
- 同步方法和同步块有什么区别？
- 执行静态同步的两种方法是什么？
- 什么是死锁以及何时会发生？ 
- 线程间通信还是合作？







### Runnable

> Runnable接口 的作用  旨在由线程执行的任何类实现。
>
>  Runnable接口只有一个名为run（）的方法。
>
> `public void run（）`：用于执行线程的操作。



> example--01
>
> 实现 Runnable接口

```java
package java8.multithreading.thread;

/**
 * @Author: xiaobo
 * @Date: 2020/11/16 1:08
 */
public class Runnable01 implements  Runnable {
    @Override
    public void run() {
        System.out.println("实现runnable接口。。");
    }

    public static void main(String[] args) {
        Runnable run = ()->{
          Runnable01 runnable01 = new   Runnable01();
          runnable01.run();
        };

        new Thread(run).start();
    }
}

```



### Thread类和Runnable接口的比较



- 由于Java“单继承，多实现”的特性，Runnable接口使用起来比Thread更灵活。

- Runnable接口出现更符合面向对象，将线程单独进行对象的封装。

- Runnable接口出现，降低了线程对象和线程任务的耦合性。

- 如果使用线程时不需要使用Thread类的诸多方法，显然使用Runnable接口更为轻量。





## Callable&Future& FutureTask

> 使用`Runnable`和`Thread`来创建一个新的线程。但是它们有一个弊端，就是`run`方法是没有返回值的。而有时候我们希望开启一个线程去执行一个任务，并且这个任务执行完成后有一个返回值。



> JDK中提供 callable 接口和 Future类解决这个问题

### Callable使用



> example--01

```java
package java8.multithreading.thread;

import java.util.concurrent.Callable;
import java.util.concurrent.FutureTask;

/**
 * @Author: xiaobo
 * @Date: 2020/11/16 14:39
 */
public class Callable01 implements Callable<String> {
    @Override
    public String call() throws Exception {
        return "callable--call---......";
    }

    public static void main(String[] args) throws Exception {
        Callable01 callable01 = new Callable01();
        FutureTask<String> stringFutureTask = new FutureTask<String>(callable01);
        Thread thread = new Thread(stringFutureTask);
        thread.start();
        System.out.println(stringFutureTask.get());

    }
}
//
callable--call---......
```





### Future接口

> future接口：

Future接口提供方法来检测任务是否被执行完，等待任务执行完获得结果，也可以设置任务执行的超时时间。

> 类声明或继承树

```java
public interface Future<V> 
```



> method

| boolean | cancel(boolean mayInterruptIfRunning )                       | `cancel`方法是试图取消一个线程的执行。                       |
| ------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| boolean | isCancelled()                                                | 任务是否已经取消，任务正常完成前将其取消，则返回 `true`      |
| boolean | isDone()                                                     | 任务是否已经完成。需要注意的是如果任务正常终止、异常或取消，都将返回true |
| V       | get()throws InterruptedException, ExecutionException         | 等待任务执行结束，然后获得V类型的结果。InterruptedException 线程被中断异常， ExecutionException任务执行异常，如果任务被取消，还会抛出`CancellationException` |
| V       | get()(long timeout, TimeUnit unit) throws InterruptedException, ExecutionException, TimeoutException | 同上面的get功能一样，多了设置超时时间。参数timeout指定超时时间，uint指定时间的单位，在枚举类TimeUnit中有相关的定义。如果计算超时，将抛出TimeoutException |





### FutureTask类

> FutureTask类：

`FutureTask`是实现的`RunnableFuture`接口的，而`RunnableFuture`接口同时继承了`Runnable`接口和`Future`接口。

FutureTask包装类：可将 Callable转换成 Future 和 Runnable, 它同时实现二者的接口。

> 类声明或继承树

```java
public class FutureTask<V> implements RunnableFuture<V> 
    //
```



> 构造函数

| public FutureTask(Callable<V> callable)        | 装了一个Callable异步执行对象，构造的时候初始化任务对象并且设置初始任务状态 |
| ---------------------------------------------- | ------------------------------------------------------------ |
| public FutureTask(Runnable runnable, V result) | 使用Runnable对象构造，那么就会包装成一个Callable对象,result是返回的结果。构造一个既是 Future<V> 又是 Runnable 的对象。 |



> 方法

protected void set(V v):run成功执行完毕之后，会调用set方法将结果保存到outcome，并且通过CAS修改任务状态

 public V get()：用于获取异步执行结果

public V get(long timeout, TimeUnit unit)：超时模式，就是会判断awaitDone结束之后任务的状态，如果状态还是未完成的就抛出超时异常

private V report(int s) ：report用于输出结果,如果状态是NORMAL就输出结果outcome，

public boolean cancel(boolean mayInterruptIfRunning) ：取消任务

private int awaitDone(boolean timed, long nanos)：阻塞等待获取结果

private void finishCompletion() ：在取消任务、set设置完结果或者setException后会调用该方法，他会唤醒等待的线程并调用钩子方法done()；

private void removeWaiter(WaitNode node)：removeWaiter会尝试移除超时和已经中断的Waiter对象，防止垃圾累积

> example--01

```java
package java8.multithreading.thread;

import java.util.concurrent.*;

/**
 * @Author: xiaobo
 * @Date: 2020/11/16 21:55
 */
public class Fature01 {
    private static class testA implements Callable<String> {
        @Override
        public String call() throws Exception {
            Thread.sleep(2000);
            return "等待两秒...";
        }
    }

    public static void main(String[] args) throws Exception{
        ExecutorService executorService = Executors.newCachedThreadPool();
        FutureTask<String> futureTask = new FutureTask<>(new testA());
        executorService.submit(futureTask);
        // 获取异步执行结果
        System.out.println(futureTask.get());
    }
}
//
等待两秒...
```

==在很多高并发的环境下，有可能Callable和FutureTask会创建多次。FutureTask能够在高并发环境下**确保任务只执行一次**。==





#### FutureTask的状态

> 状态
>
> state是任务的运行状态
>
> 运行状态会在set、setException、cancel方法中终止。
>
> COMPLETING、INTERRUPTING是任务完成后的瞬时状态。

```java
/**
  *
  * state可能的状态转变路径如下：
  * NEW -> COMPLETING -> NORMAL
  * NEW -> COMPLETING -> EXCEPTIONAL
  * NEW -> CANCELLED
  * NEW -> INTERRUPTING -> INTERRUPTED
  */
private volatile int state;
private static final int NEW          = 0; // 初始状态
private static final int COMPLETING   = 1;
private static final int NORMAL       = 2;
private static final int EXCEPTIONAL  = 3;
private static final int CANCELLED    = 4;
private static final int INTERRUPTING = 5;
private static final int INTERRUPTED  = 6;
```

