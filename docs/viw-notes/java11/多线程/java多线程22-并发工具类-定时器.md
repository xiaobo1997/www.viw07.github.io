[TOC]



# 1.定时器



## 概念

> 用timer类库主要来实现 延迟执行任务，周期执行任务；
>
> 一般执行任务 的代码会法入TimerTask的子类中



> 类声明

```java
public class Timer {
```



## 原理



> timer类可以周期执行任务是因为内部有一个 TimerThread的内部类
>
> 这个内部类有一个对timer的whiler循环方法，直到队列里为空才退出

```java
 private void mainLoop() {
        while (true) {
            try {
                TimerTask task;
                boolean taskFired;
                // queue是一个TimerTask的等待队列，按照优先级排列，内部是heap，维持add log(n)的性能
                synchronized(queue) {
                    // 队列不为空
                    while (queue.isEmpty() && newTasksMayBeScheduled)
                        // 等待
                        queue.wait();
                    // 队列为空并且 newTasksMayBeScheduled为false  就退出循环 
                    if (queue.isEmpty())
                        break; 

                    // 队列不为空 锁队列中第一个TimerTask
                    long currentTime, executionTime;
                    task = queue.getMin();
                    synchronized(task.lock) {
                        // 判断状态是否是 CANCELLED，即任务已被取消 内部调用 cancel 方法
                        if (task.state == TimerTask.CANCELLED) {
                            queue.removeMin();
                            continue;  // No action required, poll queue again
                        }
                        currentTime = System.currentTimeMillis();
                        executionTime = task.nextExecutionTime;
                        if (taskFired = (executionTime<=currentTime)) {
                            if (task.period == 0) { // Non-repeating, remove
                                queue.removeMin();
                                task.state = TimerTask.EXECUTED;
                            } else { // Repeating task, reschedule
                                queue.rescheduleMin(
                                  task.period<0 ? currentTime   - task.period
                                                : executionTime + task.period);
                            }
                        }
                    }
                    if (!taskFired) // Task hasn't yet fired; wait
                        queue.wait(executionTime - currentTime);
                }
                if (taskFired)  // Task fired; run it, holding no locks
                    task.run();
            } catch(InterruptedException e) {
            }
        }
    }

// 重写的cancel方法
// 取消正在计划中的任务，正在执行的不影响会执行完毕，然后正常终止
// 执行cancel就会把 newTasksMayBeScheduled设置为false
// 不执行newTasksMayBeScheduled就是为true,mainLoop死循环 让线程不销毁。
public void cancel() {
        synchronized(queue) {
            // 设置为false 通知不再对Timer对象引用了，
            thread.newTasksMayBeScheduled = false;
            queue.clear();
            queue.notify();  // In case queue was already empty.
        }
    }
```



> TimerTask的cancel方法

```java
   public boolean cancel() {
        synchronized(lock) {
            boolean result = (state == SCHEDULED);
            state = CANCELLED;
            return result;
        }
    }
```



## 使用

### 取消线程

> 不影响其他任务，只影响自己

```java

public class TimeTask1 {

    static class A extends TimerTask{
        @Override
        public void run() {
            System.out.println("任务执行了"+System.currentTimeMillis()+".."+Thread.currentThread().getName());
        }
    }

    public static void main(String[] args)  throws  Exception{
      long nowDate =  System.currentTimeMillis();
        System.out.println("现在时间是"+nowDate);
        long nowDate2 = (nowDate+14000);
        System.out.println("计划执行时间"+nowDate2);
        A a = new A();
        Timer timer = new Timer();
        timer.schedule(a,new Date(nowDate2));

        Thread.sleep(15000);
        a.cancel();
        Thread.sleep(Integer.MAX_VALUE);
    }
}
//
现在时间是1606621739408
计划执行时间1606621753408
任务执行了1606621753408..Timer-0
```

![image-20201129115132993](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201129115132993.png)

### 立即运行或多个执行

> 计划时间早于当前时间
>
> 多个执行 相当于 创建两个 任务，然后分别执行即可

```java
public class TimerTask2 {
    static class A extends TimerTask {
        @Override
        public void run() {
            System.out.println("任务执行了"+System.currentTimeMillis()+".."+Thread.currentThread().getName());
        }
    }
    public static void main(String[] args)  throws  Exception{
        long nowDate =  System.currentTimeMillis();
        System.out.println("现在时间是"+nowDate);
        long nowDate2 = (nowDate-5000);
        System.out.println("计划执行时间"+nowDate2);
        TimerTask2.A a = new TimerTask2.A();
        Timer timer = new Timer();
        timer.schedule(a,new Date(nowDate2));
		 a.cancel();
    }
}
现在时间是1606622014370
计划执行时间1606622009370
任务执行了1606622014373..Timer-0
```

### 延迟执行

> 当前时间 nowTime
>
> 线程A 的 方法会睡眠20s 再执行,在nowTime执行
>
> 线程B是 在A  执行后 5s执行，也就是 nowTime 后面5s执行
>
> 但是因为A睡眠了20s ，
>
> 所以实际上 B开始的时间是 nowTime+20s



### 周期执行

> 延迟周期执行

```java
public class TimerTask3 {
    static class A extends TimerTask {
        @Override
        public void run() {
            System.out.println("任务执行了"+System.currentTimeMillis()+".."+Thread.currentThread().getName());
        }
    }
    public static void main(String[] args) {
        long nowDate =  System.currentTimeMillis();
        System.out.println("现在时间是"+nowDate);
        long nowDate2 = (nowDate+6000);
        System.out.println("计划执行时间"+nowDate2);
        Timer timer =new  Timer();
        A a = new A();
        timer.schedule(a,new Date(nowDate2),4000);
    }
}
//
现在时间是1606622785767
计划执行时间1606622791767
任务执行了1606622791768..Timer-0
任务执行了1606622795768..Timer-0
任务执行了1606622799768..Timer-0
任务执行了1606622803769..Timer-0
```

> 立即周期执行

```
public class TimerTask3 {
    static class A extends TimerTask {
        @Override
        public void run() {
            System.out.println("任务执行了"+System.currentTimeMillis()+".."+Thread.currentThread().getName());
        }
    }
    public static void main(String[] args) {
        long nowDate =  System.currentTimeMillis();
        System.out.println("现在时间是"+nowDate);
        long nowDate2 = (nowDate-500);
        System.out.println("计划执行时间"+nowDate2);
        Timer timer =new  Timer();
        A a = new A();
        timer.schedule(a,new Date(nowDate2),4000);
    }
}
```

### 周期执行算法的任务顺序

> ABC 三个任务
>
> ABC--->CAB--->BCA-->ABC--> ....循环

```java

public class TimerTask3 {
    static class A extends TimerTask {
        @Override
        public void run() {
            System.out.println("A任务执行了"+System.currentTimeMillis()+".."+Thread.currentThread().getName());
        }
    }
    static class B extends TimerTask {
        @Override
        public void run() {
            System.out.println("B任务执行了"+System.currentTimeMillis()+".."+Thread.currentThread().getName());
        }
    }
    static class C extends TimerTask {
        @Override
        public void run() {
            System.out.println("C任务执行了"+System.currentTimeMillis()+".."+Thread.currentThread().getName());
        }
    }
    public static void main(String[] args) {
        long nowDate =  System.currentTimeMillis();
        System.out.println("现在时间是"+nowDate);
        long nowDate2 = (nowDate+6000);
        System.out.println("计划执行时间"+nowDate2);
        Timer timer =new  Timer();
        A a = new A();
        B b = new B();
        C c = new C();
        timer.schedule(a,new Date(nowDate2),2000);
        timer.schedule(b,new Date(nowDate2),2000);
        timer.schedule(c,new Date(nowDate2),2000);
    }
}
//
现在时间是1606623059876
计划执行时间1606623065876
A任务执行了1606623065877..Timer-0
B任务执行了1606623065877..Timer-0
C任务执行了1606623065877..Timer-0
C任务执行了1606623067878..Timer-0
A任务执行了1606623067878..Timer-0
B任务执行了1606623067878..Timer-0
B任务执行了1606623069878..Timer-0
C任务执行了1606623069878..Timer-0
A任务执行了1606623069878..Timer-0
```

### 其他的一些方法

```java

// 当前时间为基点+delay， 间隔period一直执行
public void schedule(TimerTask task, long delay, long period)
    
// task任务，第一次执行时间为 firstTime，后续执行在不超过period 之前执行
// 下一次执行的时间是在 上一次结束时间来参考的
public void scheduleAtFixedRate(TimerTask task, Date firstTime,
                                    long period)
    
//
```



## 场景

> 可以用来执行一些轮询，动画等