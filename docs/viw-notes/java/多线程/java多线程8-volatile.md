[TOC]

![image-20201122132027536](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201122132027536.png)

# 1.volatile关键字

`参考 并发编程的艺术` `多线程编程核心技术`  `并发编程实战` `网上资料`



## 概念



> volatile是一种什么形式的存在？

volatile相当于是一种弱形式的锁，对共享变量的修改会预先告知其他线程。

所以我们知道 volatile是来针对共享变量的。修饰变量。



> volatile的作用：

将对共享变量的每次读取都将从计算机的主内存中读取，而不是从CPU缓存中读取，并且对共享变量的每次写入都将被写入立即写入主存储器中，而不仅是CPU高速缓存中 



> volatile的缺点

类似于 `i--` 不支持原子性，会分解成三步



> volatile的     memory barrier（内存屏障）

被volatile修饰的变量会存在生成相应的**内存屏障** 主要是两个作用

- 1.**内存屏障**的地方 会禁止指令重排序
- 2.**内存屏障**的地方，实现缓存一致性，并实现volatile的内存语义







### 原子性

> **一个操作不能被打断，要么全部执行完毕，要么不执行。**

volatile:对任意单个 volatile 变量的读/写具有原子性



### 内存可见性

> 这里的内存可见性是针对共享变量的。而共享变量是存储在heap中的，根据JMM内存模型可以知道。

​		我们说的内存可见性是 **线程之间的可见性**，当一个线程修改了共享变量时，其他线程可以读取到这个修改后的值。即：【**对一个volatile变量的读，总是可以让看到对这个变量最后的写**】



### 重排序

> 为优化程序性能，对原有的指令执⾏顺序进⾏优化重新排序的一种操作。允许JVM和CPU对程序中的指令进行重新排序，只要指令的语义含义结果保持相同即可。重排序可能发⽣在多 个阶段，⽐如编译重排序、CPU重排序等。
>
> 而**volatile** 阻止重排 **volatile** 变量周围的读写指令

```java
 int a  = 1;
int b = 1;
a++;
b++;
// 重排序不丢语义
int a = 1;
a++;
int b = 1;
b++;
```

```java
//重排序丢语义  导致结果有出入
int a = 1;
b = a;
```

```java
// 读后写
b = a;
b  = 1;
// 写后读
a = 1;
b = a;
// 写后写
a = 1;
a= 2;
```

**如果对同一个变量 的两个操作涉及到写操作，都会影响语义结果，如写后读，读后写，写后写**

​		这也是数据依赖性，两个操作之间存在数据依赖性

![image-20201122143712351](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201122143712351.png)

参考 `《深入浅出多线程》`

### happens-before

> 只要程序员在写代码的时候遵循happens-before规 则，JVM就能保证指令在多线程之间的顺序性符合程序员的预期。
>
> 有点难理解

**volatile** 关键字可以阻止重排 **volatile** 变量周围的读写指令。这种重排规则称为 *happens before* 担保原则 。

```java
//
public class Example01 implements Runnable {
  int one, two, three, four;
  volatile int volaTile;
  @Override
  public void run() {
    one = 1;  // 1
    two = 2; // 2
    volaTile = 92; // 3     内存屏障
    int x = four; // 4
    int y = five;//5
  }
}
//  1 2 写操作发生这3之前可以重排序 不影响结果
 two = 2; // 2
 one = 1;  // 1
    volaTile = 92; // 3 
    int x = four; // 4
    int y = five;//5
// 3  volatile写操作发生在 2 1 4 5之前，2 1 4 5 可以重排，并且他们没有涉及到同一个变量中两个操作中有写操作
 volaTile = 92; // 3
 two = 2; // 2
 one = 1;  // 1
    int x = four; // 4
    int y = five;//5
```

 

![image-20201122121540149](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201122121540149.png)



==volatile的happens-before原则：对一个volatile的写操作先行发生对这个变量的读操作==



## 原理



> 被volatile 修饰的 变量  

```java
instance = new Singleton(); // instance是volatile变量
```

**有volatile变量修饰的共享变量进行写操作的时候会多出第二行汇编代码**

而被LOCK 修饰的作用

- 1.将当前处理器缓存行的数据写回到系统内存。如果对声明了volatile的 变量进行写操作，JVM就会向处理器发送一条Lock前缀的指令，将这个变量所在缓存行的数据 写回到系统内存。
- 2.一个处理器的写回内存的操作会使在其他CPU里缓存了该内存地址的数据无效。
- 3.实现缓存一致性协议避免各个处理器缓存不一致的问题，比如 这个处理器写，缓存也是最新的，而其他处理器是老的，使用他们要实现缓存一致性
  - **缓存一致性协议**：每个处理器通过嗅探在总线上传播的数据来检查自己缓存的值是不是过期了，当处理器发现自己缓存行对应的内存地址被修改，就会将当前处理器的缓存行设置成无效状态，当处理器要对这个数据进行修改操作的时候，会强制重新从系统内存里把数据读到处理器缓存里。

==处理器和内存之间有缓存带==



## 内存语义

> volatile的内存语义

当写一个volatile变量时，JMM会把该线程对应的本地内存中的共享变量值刷新到主内 存

当读一个volatile变量时，JMM会把该线程对应的本地内存置为无效。线程接下来将从主 内存中读取共享变量

![image-20201122125330404](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201122125330404.png)



## 内存语义的实现

> 在被volatile修饰的变量  插入相应的内存屏障在特定地方
>
> ==注意：内存屏障是根据不同处理器之间是有区别的，优化程度也是不一样的==
>
> JSR-133增强了volatile的内存语义：
>
> - 从编译器重排序规则和处理器内存屏障插入策略来看，只要volatile 变量与普通变量之间的重排序可能会破坏volatile的内存语义，这种重排序就会被编译器重排 序规则和处理器内存屏障插入策略禁止。 



- 在每个volatile写操作的前面插入一个StoreStore屏障。 
  - StoreStore：禁止上面的普通写与volatile写重排序

- 在每个volatile写操作的后面插入一个StoreLoad屏障。
  -  StoreLoad：防止上面的volatile写与下面的volatile读写 重排序

- 在每个volatile读操作的后面插入一个LoadLoad屏障。 
  - LoadLoad：禁止下面的普通读操作和上面的volatile读重排序

- 在每个volatile读操作的后面插入一个LoadStore屏障。 
  - LoadStore:禁止下面的 普通写和volatile重排序



volatile与普通变量的重排序规则: 

- 1.如果第⼀个操作是volatile读，那⽆论第⼆个操作是什么，都不能重排序； 
- 2.如果第⼆个操作是volatile写，那⽆论第⼀个操作是什么，都不能重排序； 
- 3.如果第⼀个操作是volatile写，第⼆个操作是volatile读，那不能重排序。 



## 使用

> 使用

```java
package java8.multithreading.vol;

/**
 * @Author: xiaobo
 * @Date: 2020/11/22 12:56
 */
public class Volatile01 {
    private volatile static int i = 0;
    public static class A implements Runnable {
        @Override
        public void run() {
            while (i < 5) {
                i = i + 1;
                System.out.println("A:" + i);
            }
        }
    }

    public static class B implements Runnable {
        @Override
        public void run() {
            while (i < 10) {
                i = i + 1;
                System.out.println("B:" + i);
            }
        }
    }
    public static void main(String[] args) throws InterruptedException {
        new Thread(new A()).start();
        Thread.sleep(1000);
        new Thread(new B()).start();
    }
}

```





## 使用建议

> 使用的场景和建议

- 1.==使用volatile 更应该尝试让一个变量线程安全而不是引起同步== 也就是说尽量少使用，出问题不好排查，而且除非你对并发比较懂了。
- 2.可以使用 ` java.util.concurrent.atomic 里面类之一` ，用 **Atomic** 变量
- 3.如果你正在尝试调试其他人的并发代码，请首先查找使用 **volatile** 的代码并将其替换为**Atomic** 变量。除非你确定程序员对并发性有很高的理解，否则它们很可能会误用 **volatile** 。



## synchronzied 和volatile



volatile与锁具有相同的内存效果，volatile变量的写和锁的释放 具有相同的内存语义，volatile变量的读和锁的获取具有相同的内存语义。 

> 三大特性

原子性：

- synchronozed

内存可见性：

- volatile
- synchronized

有序性：

- volatile
- synchronized



> 两个比较

在功能上，锁⽐**volatile**更强⼤；在性能上，**volatile**更有优势。