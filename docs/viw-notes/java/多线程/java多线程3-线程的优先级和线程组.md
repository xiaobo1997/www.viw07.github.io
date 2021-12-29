[TOC]

# 1.线程的优先级

> 每个线程都有一个优先级。 优先级由1到10之间的数字表示(但是并不是所有的操作系统都支持10级优先级的划分（比如有些操作系统只支持3级划分：低，中，高），Java只是给操作系统一个优先级的**参考值**)。在大多数情况下，线程计划会根据线程的优先级来调度线程（称为抢先式调度）。 但是不能保证，因为它取决于JVM规范，它选择哪种调度。

**需要注意的是：**

- 优先级高的 并不是 一定先执行，有随机性。
- CPU尽可能的把资源给优先级高的线程
- 优先级高的大概率运行的快

## 使用



> example--01
>
> set thread priority

```java
package java8.multithreading.thread;

/**
 * @Author: xiaobo
 * @Date: 2020/11/16 23:07
 */
public class PriorityA {
    private static class testA extends Thread {
        @Override
        public void run() {
            System.out.println("name:" + Thread.currentThread().getName());
            System.out.println("priority:" + Thread.currentThread().getPriority());
        }
    }

    public static void main(String[] args) {
        testA testA1 = new testA();
        PriorityA.testA testA2 = new testA();
        testA1.setPriority(1);
        System.out.println("设置后的优先级" + testA1.getPriority());
        System.out.println("默认优先级" + testA2.getPriority());
    }
}
//
我是默认线程优先级：5
我是设置过的线程优先级：10
```



> exampl--02

```java
package java8.multithreading.thread;

/**
 * @Author: xiaobo
 * @Date: 2020/11/16 23:23
 */
public class ThreadGroup1 {
    public static void main(String[] args) {
        ThreadGroup threadGroup = new ThreadGroup("A");
        threadGroup.setMaxPriority(6);
        Thread thread = new Thread(threadGroup, "thread-A");
        thread.setPriority(8);
        /**
         * 如果某个线程优先级大于线程所在线程组的最大优先级，
         * 那么该线程的优先级将会失效，取而代之的是线程组的最大优先级。
         */
        System.out.println(threadGroup.getMaxPriority());
        System.out.println(thread.getPriority());

    }
}
//
```



# 2.守护线程



> Java中的守护程序线程是一个服务提供者线程，向用户线程提供服务。 它的寿命取决于用户线程，即当所有用户线程都死掉时，JVM会自动终止该线程。许多Java守护程序线程会自动运行，例如 gc，finalizer等。您可以通过在命令提示符下键入jconsole来查看所有详细信息。 jconsole工具提供有关已加载类，内存使用情况，正在运行的线程等的信息。



**需要注意的是：**

- 它为后台支持任务的用户线程提供服务。 
- 它在生命中没有什么作用要服务于用户线程，它的寿命取决于用户线程，
- 它是低优先级线程。



> 关于没有用户线程， daemon thread 为什么销毁

守护程序线程的唯一目的是为后台支持任务向用户线程提供服务。 如果没有用户线程，那么JVM为什么要继续运行该线程。 这就是为什么JVM在没有用户线程的情况下终止守护程序线程的原因。



> method

| 1)   | public void setDaemon(boolean status) | 用于将当前线程标记为守护程序线程 |
| ---- | ------------------------------------- | -------------------------------- |
| 2)   | public boolean isDaemon()             | 用于检查是否是守护程序。         |



> example--1

```java
package java8.multithreading.thread;

/**
 * @Author: xiaobo
 * @Date: 2020/11/16 23:27
 */
public class DaemonThread01 extends Thread{
    @Override
    public void run() {
        if (Thread.currentThread().isDaemon()) {
            System.out.println("daemon thread");
        }else
        {
            System.out.println("user thread");
        }
    }

    public static void main(String[] args) {
        DaemonThread01 daemonThread01 = new DaemonThread01();
        DaemonThread01 daemonThread02 = new DaemonThread01();
        daemonThread01.setDaemon(true);//设置为 daemon thread
        daemonThread01.start();
        daemonThread02.start();
    }
}
//
daemon thread
user thread
```



**如果要将用户线程设置为守护程序，则不能然后再启动它，否则它将引发IllegalThreadStateException。**

# 3.线程组



> Java提供了一种将多个线程组合到单个对象中的便捷方法。 这样，我们可以通过单个方法调用来挂起，恢复或中断线程组。(那么现在不建议使用suspend（），resume（）和stop（）方法。)

**需要注意的是：**

- ThreadGroup是一个标准的**向下引用**的树状结构，这样设计的原因是**防止"上级"线程被"下级"线程引用而无法有效地被GC回收**。
- ThreadGroup代表一组线程。 一个线程组也可以包括另一个线程组。 线程组类似于创建一棵树，其中除初始线程组外的每个线程组都有一个父级。
- 允许线程访问有关其自己的线程组的信息，但不能访问有关其线程组的父线程组或任何其他线程组的信息。



## 使用







> 构造函数

| 1)   | ThreadGroup(String name)                     | 创建具有给定名称的线程组。       |
| ---- | -------------------------------------------- | -------------------------------- |
| 2)   | ThreadGroup(ThreadGroup parent, String name) | 创建具有给定父组和名称的线程组。 |



> 线程组的成员变量

```java
   private final ThreadGroup parent; // 父亲ThreadGroup

​    String name; // ThreadGroupr 的名称

​    int maxPriority; // 线程最大优先级

​    boolean destroyed; // 是否被销毁

​    boolean daemon; // 是否守护线程

​    boolean vmAllowSuspension; // 是否可以中断

​    int nUnstartedThreads = 0; // 还未启动的线程

​    int nthreads; // ThreadGroup中线程数目

​    Thread threads[]; // ThreadGroup中的线程

​    int ngroups; // 线程组数目

​    ThreadGroup groups[]; // 线程组数组
```



> method

| 1)   | void        | [checkAccess()](https://www.javatpoint.com/java-threadgroup-checkaccess-method) | 确定当前正在运行的线程是否具有修改线程组的权限。这里涉及到`SecurityManager`这个类，它是Java的安全管理器，它允许应用程序在执行一个可能不安全或敏感的操作前确定该操作是什么，以及是否是在允许执行该操作的安全上下文中执行它。应用程序可以允许或不允许该操作。 |
| ---- | ----------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2)   | int         | [activeCount()](https://www.javatpoint.com/java-threadgroup-activecount-method) | 返回线程组及其子组中活动线程数的估计值。                     |
| 3)   | int         | [activeGroupCount()](https://www.javatpoint.com/java-threadgroup-activegroupcount-method) | This method returns an estimate of the number of active groups in the thread group and its subgroups. |
| 4)   | void        | [destroy()](https://www.javatpoint.com/java-threadgroup-destroy-method) | 销毁线程组及其所有子组。                                     |
| 5)   | int         | enumerate(Thread[\] list)                                    | This method copies into the specified array every active thread in the thread group and its subgroups. |
| 6)   | int         | [getMaxPriority()](https://www.javatpoint.com/java-threadgroup-getmaxpriority-method) | 返回线程组的最大优先级。                                     |
| 7)   | String      | [getName()](https://www.javatpoint.com/java-threadgroup-getname-method) | 返回线程组的名称。                                           |
| 8)   | ThreadGroup | [getParent()](https://www.javatpoint.com/java-threadgroup-getparent-method) | 返回线程组的父级。                                           |
| 9)   | void        | [interrupt()](https://www.javatpoint.com/java-threadgroup-interrupt-method) | 中断线程组中的所有线程。                                     |
| 10)  | boolean     | [isDaemon()](https://www.javatpoint.com/java-threadgroup-isdaemon-method) | 测试线程组是否是守护程序线程组                               |
| 11)  | void        | [setDaemon(boolean daemon)](https://www.javatpoint.com/java-threadgroup-setdaemon-method) | 更改线程组的守护程序状态。                                   |
| 12)  | boolean     | [isDestroyed()](https://www.javatpoint.com/java-threadgroup-isdestroyed-method) | 测试此线程组是否已被销毁。                                   |
| 13)  | void        | [list()](https://www.javatpoint.com/java-threadgroup-list-method) | 将有关线程组中的线程信息输出到输出流。                       |
| 14)  | boolean     | [parentOf(ThreadGroup g](https://www.javatpoint.com/java-threadgroup-parentof-method) | 测试线程组是线程组参数还是其祖先线程组之一。                 |
| 15)  | void        | [suspend()](https://www.javatpoint.com/java-threadgroup-suspend-method) | 挂起线程组中的所有线程                                       |
| 16)  | void        | [resume()](https://www.javatpoint.com/java-threadgroup-resume-method) | 恢复使用suspend（）方法挂起的线程组中的所有线程。            |
| 17)  | void        | [setMaxPriority(int pri)](https://www.javatpoint.com/java-threadgroup-setmaxpriority-method) | This method sets the maximum priority of the group.          |
| 18)  | void        | [stop()](https://www.javatpoint.com/java-threadgroup-stop-method) | 用于停止线程组中的所有线程。                                 |
| 19)  | String      | [toString()](https://www.javatpoint.com/java-threadgroup-tostring-method) | This method returns a string representation of the Thread group. |



> exampl--01
>
> use thread grop

```java
package java8.multithreading.thread;

/**
 * @Author: xiaobo
 * @Date: 2020/11/16 23:43
 */
public class DaemonThread02 implements Runnable{
    @Override
    public void run() {
        System.out.print("name:"+Thread.currentThread().getName());
    }

    public static void main(String[] args) {
        DaemonThread02 daemonThread02 = new DaemonThread02();
        ThreadGroup threadGroup = new ThreadGroup("Parent group");
        Thread a = new Thread(threadGroup, daemonThread02,"a");
        Thread b = new Thread(threadGroup,daemonThread02, "b");
        a.start();
        b.start();
        System.out.print("group name:"+threadGroup.getName());
        threadGroup.list();// 返回 线程组 的集合 
    }
}
//
group name:Parent groupjava.lang.ThreadGroup[name=Parent group,maxpri=10]
    Thread[a,5,Parent group]
    Thread[b,5,Parent group]
name:aname:b
```



> exampl--2
>
> 集中处理 线程组异常

```java
package java8.multithreading.thread;

/**
 * @Author: xiaobo
 * @Date: 2020/11/16 23:51
 */
public class ThreadGroup02 {
    public static void main(String[] args) {
        ThreadGroup threadGroup = new ThreadGroup("parent ") {
            // 线程成员抛出 uncaughtException 执行里面方法
            public void uncaughtException(Thread t, Throwable e) {
                System.out.println(t.getClass() + "  " + e.getMessage());
            }
        };

        // a 是线程组的成员
        Thread a = new Thread(threadGroup, new Runnable() {
            @Override
            public void run() {
                // 抛出异常
                throw new RuntimeException("exception test");
            }
        });
        a.start();

    }
}
// 
class java.lang.Thread  exception test
```