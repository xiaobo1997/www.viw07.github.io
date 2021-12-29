[TOC]



# 阻塞队列



## 概念

> 以生产者消费者为例

⽣产者⼀直⽣产资源，消费者⼀直消费资源，资源存储在⼀个缓冲池中，⽣产者将⽣产的资源存进缓冲池中，消费者从缓冲池中拿到资源进⾏消 费，这就是⼤名鼎鼎的⽣产者**-**消费者模式。 



> 阻塞队列

BlockingQueue是Java util.concurrent包下重要的数据结构，区别于普通的队 列，BlockingQueue提供了线程安全的队列访问⽅式，并发包下很多⾼级同 步类的实现都是基于BlockingQueue实现的。

而生产者是往容器中添加添加元素的线程，消费者是往容器中消费元素的线程

而BlockingQueue就是存放元素 的容器



> 从阻塞和非阻塞的角度

**阻塞与非阻塞**，所谓阻塞指的是：**当队列已满时，入队操作阻塞；当队列已空时，出队操作阻塞**。

> 从单端与双端的角度

单端指的是只能队尾入队，队首出队；而双端指的是队首队尾皆可入队出队。Java 并发包里**阻塞队列都用 Blocking 关键字标识，单端队列使用 Queue 标识，双端队列使用 Deque 标识**。



> 类继承关系

![preview](https://pic1.zhimg.com/v2-7528fb31b2fa3a5b3f8ff086427d7e98_r.jpg)



## 基本操作方法



![image-20201129160157343](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201129160157343.png)



> 阻塞队列的四阻不同方法

![image-20201129160307155](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201129160307155.png)



- 抛出异常：如果试图的操作⽆法⽴即执⾏，抛异常。当阻塞队列满时候，再往 队列⾥插⼊元素，会抛出IllegalStateException(“Queue full”)异常。当队列为空 时，从队列⾥获取元素时会抛出NoSuchElementException异常 。 

- 返回特殊值：如果试图的操作⽆法⽴即执⾏，返回⼀个特殊值，通常是true /false。 

- ⼀直阻塞：如果试图的操作⽆法⽴即执⾏，则⼀直阻塞或者响应中断。 

- 超时退出：如果试图的操作⽆法⽴即执⾏，该⽅法调⽤将会发⽣阻塞，直到能 够执⾏，但等待时间不会超过给定值。返回⼀个特定值以告知该操作是否成 功，通常是 true / false。 



**注意：**

不能往阻塞队列中插⼊null,会抛出空指针异常。 

可以访问阻塞队列中的任意元素，调⽤remove(o)可以将队列之中的特定对象 移除，但并不⾼效，尽量避免使⽤。 







## 分类-常用的BlockingQueue



> 实现BlockingQueue接口的有ArrayBlockingQueue, DelayQueue, LinkedBlockingDeque, LinkedBlockingQueue, LinkedTransferQueue, PriorityBlockingQueue, SynchronousQueue，

###  **ArrayBlockingQueue**

> 构造方法

```java
public ArrayBlockingQueue(int capacity, boolean fair){
 //..省略代码
}
```

**ArrayBlockingQueue**是由数组实现的有界阻塞队列。该队列命令元素FIFO（先进先出）。

可以初始化队列⼤⼩， 且⼀旦初始化不能改变。构造⽅法中的**fair**表示控制对象的 内部锁是否采⽤公平锁，**默认是⾮公平锁。**

**如果保证公平性，通常会降低吞吐量**。



#### 原理

> 构造器，除了初始化队列的大小和是否是公平锁之外，还对同一个锁（lock）初始化了两个监视器，分别是notEmpty和notFull。这两个监视器的作用目前可以简单理解为标记分组，当该线程是put操作时，给他加上监视器notFull,标记这个线程是一个生产者；当线程是take操作时，给他加上监视器notEmpty，标记这个线程是消费者。

![image-20201129164231000](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201129164231000.png)

> put方法

```java
 public void put(E e) throws InterruptedException {
        checkNotNull(e);
        final ReentrantLock lock = this.lock;
        // 拿锁
        lock.lockInterruptibly();
        try {
        // 判断队类是否满了
            while (count == items.length)
            // 如果满了 阻塞线程 标记为 notFull
                notFull.await();
                //没满 进入队列
            enqueue(e);
        } finally {
            lock.unlock();
        }
    }


 private void enqueue(E x) {
        // assert lock.getHoldCount() == 1;
        // assert items[putIndex] == null;
        final Object[] items = this.items;
        items[putIndex] = x;
        if (++putIndex == items.length)
            putIndex = 0;
        count++;
        // 唤醒一个等待的线程
        notEmpty.signal();
    }
```

**1.所有执行put操作的线程先拿锁，得到的下一步，没有的自旋。2.判断队列是否满了。满了等待，并标记为notFull线程，释放锁，等待消费者唤醒。3.如果没满，调用enqueue方法将元素put进阻塞队列，并通知唤醒一个 被标记为notEmpty的线程**



> take方法

```java
public E take() throws InterruptedException {
        final ReentrantLock lock = this.lock;
    // 拿锁
        lock.lockInterruptibly();
        try {
            // 判断是否空
            while (count == 0)
                // 如果空，notEmpty消费者线程等待
                notEmpty.await();
            // 如果没空，调用dequeue方法，
            return dequeue();
        } finally {
            lock.unlock();
        }
    }
    
 private E dequeue() {
       
        final Object[] items = this.items;
        @SuppressWarnings("unchecked")
        E x = (E) items[takeIndex];
        items[takeIndex] = null;
        if (++takeIndex == items.length)
            takeIndex = 0;
        count--;
        if (itrs != null)
            itrs.elementDequeued();
     // 唤醒一个notFull线程（生产者）
        notFull.signal();
        return x;
    }


```

**1.先所有执行take的线程去获取锁。2.拿到锁判断队列是否可用，不可用阻塞，并释放锁。3.被阻塞的线程被唤醒，然后被唤醒的线程需要去拿锁，拿到再判断是否可用，所以这里用while**



### LinkedBlockingQueue



>  由链表结构组成的有界阻塞队列。内部结构是链表，具有链表的特性。默认队列的 ⼤⼩是 Integer.MAX_VALUE ，也可以指定⼤⼩。此队列按照先进先出的原则对元素 进⾏排序。



> 具有更高的吞吐量，为了防止LinkedBlockingQueue容量迅速增，损耗大量内存。一般会指定大小

```java
 public LinkedBlockingQueue()  // 默认
     
  public LinkedBlockingQueue(int capacity) // 指定大小
     
public LinkedBlockingQueue(Collection<? extends E> c) // 指定集合 

```



### PriorityBlockingQueue

> 基于优先级的⽆界阻塞队列（优先级的判断通过构造函数传⼊的Compator对象来决定），内部控制线程同步,默认情况下元素采用自然顺序进行排序，也可以通过自定义类实现compareTo()方法来指定元素排序规则，或者初始化时通过构造器参数Comparator来指定排序规则。



> 类声明

```java
public class PriorityBlockingQueue<E> extends AbstractQueue<E>
    implements BlockingQueue<E>, java.io.Serializable 
```



> 构造方法

```java
// 创建一个具有默认初始容量（11）的PriorityBlockingQueue ，该默认容量根据其自然顺序对元素进行排序。

	public PriorityBlockingQueue() 
     public PriorityBlockingQueue(int initialCapacity)
        // 创建一个具有指定初始容量的PriorityBlockingQueue ，该容量根据指定的比较器对其元素进行排序。
        public PriorityBlockingQueue(int initialCapacity,
                                 Comparator<? super E> comparator) 
         public PriorityBlockingQueue(Collection<? extends E> c) 
    

```



### SynchronousQueue



> 这个队列⽐较特殊，没有任何内部容量，甚⾄连⼀个队列的容量都没有。只有线程在删除数据时，其他线程才能插入数据，同样的，如果当前有线程在插入数据时，线程才能删除数据。



> 类声明

```java
public class SynchronousQueue<E> extends AbstractQueue<E>
    implements BlockingQueue<E>, java.io.Serializable 
```



### LinkedTransferQueue



> LinkedTransferQueue是一个由链表数据结构构成的无界阻塞队列，由于该队列实现了TransferQueue接口，



> 类声明

```java
public class LinkedTransferQueue<E> extends AbstractQueue<E>
    implements TransferQueue<E>, java.io.Serializable
```



> 特性方法

```java
//transfer(E e)如果当前有线程（消费者）正在调用take()方法或者可延时的poll()方法进行消费数据时，生产者线程可以调用transfer方法将数据传递给消费者线程。如果当前没有消费者线程的话，生产者线程就会将数据插入到队尾，直到有消费者能够进行消费才能退出；
      public void transfer(E e)
          // 如果当前有消费者线程（调用take方法或者具有超时特性的poll方法）正在消费数据的话，该方法可以将数据立即传送给消费者线程，如果当前没有消费者线程消费数据的话，就立即返回false。
           public boolean tryTransfer(E e, long timeout, TimeUnit unit)
          // 增加了超时特性，如果数据规定的超时时间内没有消费者进行消费的话，就返回false。
           public boolean tryTransfer(E e, long timeout, TimeUnit unit)
```



### LinkedBlockingDeque



> LinkedBlockingDeque是基于链表数据结构的有界阻塞双端队列，如果在创建对象时为指定大小时，其默认大小为Integer.MAX_VALUE。



> 类声明

```java
public class LinkedBlockingDeque<E>
    extends AbstractQueue<E>
    implements BlockingDeque<E>, java.io.Serializable 
```



### **DelayQueue**

> 该队列中的元素只有当其指定的延迟时间到了，才能够从队列中获取到该元素 。注入其中的元素必须实现 java.util.concurrent.Delayed 接口。 
>
> DelayQueue是一个没有大小限制的队列，因此往队列中插入数据的操作（生产者）永远不会被阻塞，而只有获取数据的操作（消费者）才会被阻塞。 

>  DelayQueue是一个存放实现Delayed接口的数据的无界阻塞队列，只有当数据对象的延时时间达到时才能插入到队列进行存储。如果当前所有的数据都还没有达到创建时所指定的延时期，则队列没有队头，并且线程通过poll等方法获取数据元素则返回null。所谓数据延时期满时，则是通过Delayed接口的getDelay(TimeUnit. NANOSECONDS)来进行判定，如果该方法返回的是小于等于0则说明该数据元素的延时期已满。



>  DelayQueue是一个支持延时获取元素的无界阻塞队列。队列使用PriorityQueue来实现。队 列中的元素必须实现Delayed接口，在创建元素时可以指定多久才能从队列中获取当前元素。 只有在延迟期满时才能从队列中提取元素。 

## 场景

