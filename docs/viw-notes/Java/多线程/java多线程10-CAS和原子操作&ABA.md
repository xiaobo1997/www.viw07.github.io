[TOC]



![image-20201122222637302](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201122222637302.png)

# CAS

## 概念



> CAS 全称 比较并交换（compare and swap）是原子操作的一种 可用于在多线程编程中实现不被打断的数据交换操作，从而避免多线程同时改写某一数据时由于执行顺序不确定性以及中断的不可预知性产生的数据不一致问题。 
>
> ==CAS是“⽆锁”的基础，它允许更新失败。==

CAS中有三个值：

- 1.V：主内存值(要更新的变量)
- 2.E：预期值（旧值）
- 3.N：新值

如果 V 和E 相等 则更新V 把N 赋V；如果不等，什么都不做，说明其他线程更新了V 当前线程放弃更新V

如：

多个线程共享变量i = 1;现在线程A要修改i这个值 为 2，

 先把 i 和 1 比较，如果相等 就把 2 赋给 i， 说明没有其他线程修改过 i ,说明这一次CAS是成功

如果 i  不等于5， 那么说明i 被其他线程修改过(比如说被修改为3)， 那就什么都不做，还是为3

i  就是 V, 1 就是E  2 就是N



> CAS是一种原子操作

CAS是⼀种原⼦操作，它是⼀种系统原语，是⼀条CPU的原⼦指令， 

从CPU层⾯保证它的原⼦性 当多个线程同时使⽤**CAS**操作⼀个变量时，只有⼀个会胜出，并成功更新，其余均 会失败，但失败的线程并不会被挂起，仅是被告知失败，并且允许再次尝试，当然 也允许失败的线程放弃操作。 



> java 中的CAS的实现

在java.util.concurrent.atomic包下的原子操作类都是基于CAS实现的，

在Java中，如果⼀个⽅法是native的，那Java就不负责具体实现它，⽽是交给底层的JVM使⽤c或者c++去实现。 

有一个 `Unsafe` 类 里面有关于 支持CAS的方法， 都是 public native

**Unsafe中对CAS的实现是C++写的，它的具体实现和操作系统、CPU都有关系。**

Unsafe 还支持线程挂起 和恢复， park 和unpark ， LockSupport底层调用了这两个方法，还有支持反射的 allocateInstance()方法

#  原子操作



> AtomicInteger的原子操作

Unsafe是关于支持CAS方法的，而 java 是通过 `java.util.concurrent.atomic ` 来具体实现 原子操作的



例 AtomicInteger 的getAndAdd 方法

```java
// JDK8
public class AtomicInteger extends Number implements java.io.Serializable {
    private static final long serialVersionUID = 6214790243416807050L;

    // setup to use Unsafe.compareAndSwapInt for updates
    private static final Unsafe unsafe = Unsafe.getUnsafe();
    private static final long valueOffset;
     static {
        try {
            // 得到对象的一个偏移量
            valueOffset = unsafe.objectFieldOffset
                (AtomicInteger.class.getDeclaredField("value"));
        } catch (Exception ex) { throw new Error(ex); }
    }
    ...
    
     // getAndAdd是调用 Unsafe的方法来 实现的
    public final int getAndAdd(int delta) {
        return unsafe.getAndAddInt(this, valueOffset, delta); //
    }
    ...
    
    }
```



在看 Unsafe的 getAndAdd 源码



```java
// JDK8
...
      // var1 指 AtomicInteger对象， var2  是 常量是一个对象字段偏移量 var4
  public final int getAndAddInt(Object var1, long var2, int var4) {
        int var5;
      // do while循环  的⽬的是保证循环体内的语句⾄ 少会被执⾏⼀遍。这样才能保证return 的值 v 是我们期望的值。
        do {
            var5 = this.getIntVolatile(var1, var2); // 要返回的值（原来的值）
        } while(!this.compareAndSwapInt(var1, var2, var5, var5 + var4));// var5 + var4 新的值

        return var5;
    }    
    ...
```

do..while循环体的CAS方法

```java
// JDK 9
public final boolean weakCompareAndSetInt(Object o, long offset,
 int expected,
 int x) {
 return compareAndSetInt(o, offset, expected, x);
}
public final native boolean compareAndSetInt(Object o, long offset,
 int expected,
 int x);
```

关于 weakCompareAndSetInt 的方法中 调用compareAndSetInt的说明

参考  `深入浅出多线程这里的解释`

![image-20201122221441844](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201122221441844.png)

# CAS实现 原子操作的 三个问题



## ABA

> 谓ABA问题，就是⼀个值原来是A，变成了B，⼜变回了A。这个时候使⽤CAS是 
>
> 检查不出变化的，但实际上却被更新了两次。



> 解决办法

ABA问题的解决思路是在变量前⾯追加上版本号或者时间戳。

使用JDK的atomic包⾥提供了⼀个类 AtomicStampedReference 类来解决ABA问题。 

**compareAndSet作用：**

​		⾸先检查当前引⽤是否等于预期引⽤，并且 检查当前标志是否等于预期标志，如果⼆者都相等，才使⽤CAS设置为新的值和标 志。

```java
// AtomicStampedReference  源码
public boolean compareAndSet(V   expectedReference,
                                 V   newReference,
                                 int expectedStamp,
                                 int newStamp) {
        Pair<V> current = pair;
        return
            expectedReference == current.reference &&
            expectedStamp == current.stamp &&
            ((newReference == current.reference &&
              newStamp == current.stamp) ||
             casPair(current, Pair.of(newReference, newStamp)));
    }
```



## 循环时间开销大

> 当CAS和自旋锁相结合， 如果 不成功，会占⽤⼤量的CPU资源。



> 解决办法

解决思路是让JVM⽀持处理器提供的**pause**指令。pause指令能让⾃旋失败时cpu睡眠⼀⼩段时间再继续⾃旋。



## 只能保证一个共享变量的操作



> - . 使⽤JDK 1.5开始就提供的 AtomicReference 类保证对象之间的原⼦性，把多个 变量放到⼀个对象⾥⾯进⾏CAS操作； 
>
> - 使⽤锁。锁内的临界区代码可以保证只有当前线程能操作。 







