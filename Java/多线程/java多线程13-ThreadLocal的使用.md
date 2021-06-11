[TOC]

![image-20201124234526977](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201124234526977.png)

# ThreadLocal



## 概念



> threadlocal初识

ThreadLocal是⼀个本地线程副本变量⼯具类。内部是⼀个弱引⽤的Map来维护。 

threadlocal让每个线程有⾃⼰”独⽴“的变量，线程之间互不 影响（线程隔离）。它为每个线程都创建⼀个副本，每个线程可以访问⾃⼰内部的副本变量。

> 作用

Java中的ThreadLocal类可以让你创建的变量只被同一个线程进行读和写操作。因此，尽管有两个线程同时执行一段相同的代码，而且这段代码又有一个指向同一个ThreadLocal变量的引用，但是这两个线程依然不能看到彼此的ThreadLocal变量域。



> 思想

空间换时间



## 使用







> 创建  get set

```java
package java8.multithreading.threadlocal;

/**
 * @Author: xiaobo
 * @Date: 2020/11/24 0:19
 */

public class ThreadLocal01 {

    private ThreadLocal<String> threadLoca2 = new ThreadLocal<>();

    public static void main(String[] args) {
        ThreadLocal01 local01 = new ThreadLocal01();
        /**
         * 每个线程仅需要实例化一次即可。虽然不同的线程执行同一段代码时，访问同一个ThreadLocal变量，
         * 但是每个线程只能看到私有的ThreadLocal实例。所以不同的线程在给ThreadLocal对象设置不同的值时，
         * 他们也不能看到彼此的修改。
         */
        // 创建threadlocal对象
        ThreadLocal<Object> threadLocal = new ThreadLocal<>();
        // 创建了一个ThreadLocal对象，存储此对象的值：
        threadLocal.set("第一个 threadlocal");
        //直接读取一个ThreadLocal对象的值：
        System.out.println(threadLocal.get());
		threadLocal.remove();
        //创建一个泛型化的ThreadLocal对象。 使get()方法返回值不用做强制类型转换
        local01.threadLoca2.set("泛型 防止强转");

    }

}
//
第一个 threadlocal
```



> 初始化  threadlocal 

```java
package java8.multithreading.threadlocal;

/**
 * @Author: xiaobo
 * @Date: 2020/11/24 0:28
 */
public class ThreadLocal02 {

    // 通过ThreadLocal子类的实现，并覆写initialValue()方法，就可以为ThreadLocal对象指定一个初始化
    public static void main(String[] args) {
        ThreadLocal<String> threadLocal = new ThreadLocal<String>(){
            @Override
            protected String initialValue() {
                return "现在所有线程都可以看到这个值了，除非你在get之前调用了set";
            }
        };
        System.out.println( threadLocal.get());
    }
}

//
现在所有线程都可以看到这个值了，除非你在get之前调用了set
```



> exampl--01
>
> use threadlocal

```java
package java8.multithreading.threadlocal;

/**
 * @Author: xiaobo
 * @Date: 2020/11/24 0:32
 */
public class ThreadLocal03 {


    public static class ThreadA implements Runnable {

        private ThreadLocal<Integer> threadLocal = new ThreadLocal<Integer>();

        @Override
        public void run() {
            threadLocal.set((int) (Math.random() * 100));
            for (int i = 0; i <2;i++){
                try {
                    Thread.sleep(2000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(threadLocal.get());
            }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        ThreadA threadA = new ThreadA();
        Thread thread1 = new Thread(threadA);
        Thread thread2 = new Thread(threadA);
        thread1.start();thread2.start();
        thread1.join();
        thread2.join();
    }
}
//
80
76
80
76
```

==如果调用set()方法的时候用synchronized关键字同步，而且不是一个ThreadLocal对象实例，那么第二个线程将会覆盖第一个线程所设置的值。==







## 原理实现



### set



```java
   public void set(T value) {
       // 获取当前执行线程引用
        Thread t = Thread.currentThread();
       // 获取 ThreadLocalMap 
        ThreadLocalMap  map = getMap(t);
        if (map != null)
            // 如果不为null 就把当前线程作为key  值为value 存入
            map.set(this, value);
        else
            // 否则就创建一个新的 ThreadLocalMap并存储  t:当前线程 并且作为key value：初始值
            createMap(t, value);
    }

  ThreadLocalMap getMap(Thread t) {
        return t.threadLocals;
    }
    void createMap(Thread t, T firstValue) {
        t.threadLocals = new ThreadLocalMap(this, firstValue);
    }
	
 	ThreadLocal.ThreadLocalMap threadLocals = null;
```

==ThreadLocal的值是放入了当前线程的一个ThreadLocalMap实例中，所以只能在本线程中访问，其他线程无法访问。==



### get



```java
 public T get() {
     // 获取当前执行线程的引用
        Thread t = Thread.currentThread();
     // 实例化得到这个线程的 map
        ThreadLocalMap map = getMap(t);
        if (map != null) {
            // 获取当前map中  key为当前线程的 entry
            ThreadLocalMap.Entry e = map.getEntry(this);
            if (e != null) {
                @SuppressWarnings("unchecked")
                // 不为null  取出来 直接返回结果
                T result = (T)e.value;
                return result;
            }
        }
     // 为null 
        return setInitialValue();
    }
 // 为null 就把当前线程 作为key  value为null 返回
 private T setInitialValue() {
        T value = initialValue();
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null)
            map.set(this, value);
        else
            createMap(t, value);
        return value;
    }

  protected T initialValue() {
        return null;
    }
```

==可以重新initialValue 实现其他的初始值==



### remove

```java
  public void remove() {
      // 获取当前执行线程的引用
         ThreadLocalMap m = getMap(Thread.currentThread());
         if (m != null)
             // 如果不为null 就从这个map中 移出去
             m.remove(this);
     }
```



**总结：**

​		set get remove都是通过内部维护的map 来实现的， 让当前执行线程作为引用，来实现唯一映射

### ThreadLocalMap



> threadlocalmap是threadlocal的一个内部静态类
>
> key是threadlocal实例，value是entry[] 数组



> threadlocalmap采用的是开放定址法，就是一旦发生了冲突，就去寻找下一个空的散列地址，只要散列表足够大，空的散列地址总能找到，并将记录存入。



> 初始容量和扩容

初始16，加载因子 2/3，可用 容量10

超过容量时通过resize()方法扩容

**扩容时如果有key为null  value为object的键值对 会让 value为null 让 GC回收解决隐藏的内存泄漏的问题**

> threadlocalhashcode

ThreadLocal 有个 threadLocalHashCode 变量，每次创建 ThreadLocal 对象时，这个变量都会增加一个固定的值 `HASH_INCREMENT`，即 0x61c88647



#### entry

> ThreadLocalMap是threadLocal一个静态内部类，和大多数容器一样内部维护了一个数组，同样的threadLocalMap内部维护了一个Entry类型的table数组
>
> table数组的长度为2的幂次方
>
> entry是一个以threadlocal为key object为value的键值对，
>
> Entry继承了WeakReference 是一个弱引用

```java
        static class Entry extends WeakReference<ThreadLocal<?>> {
            /** The value associated with this ThreadLocal. */
            Object value;

            Entry(ThreadLocal<?> k, Object v) {
                super(k);
                value = v;
            }
        }

```

**Entry类里面的key值就是ThreadLocal，并且是弱引用，value是threadlocalmap中set的value**

thread,threadLocal,threadLocalMap，Entry之间的关系:

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201124123631932.png" alt="image-20201124123631932" style="zoom:67%;" />





实线是强引用，虚线表示弱引用。



#### set

>根据key更新value
>

```java
 private void set(ThreadLocal<?> key, Object value) {

            // We don't use a fast path as with get() because it is at
            // least as common to use set() to create new entries as
            // it is to replace existing ones, in which case, a fast
            // path would fail more often than not.
			
     		//内部一个 entry数组 
            Entry[] tab = table;
            int len = tab.length;
     		//确定entry的存放位置  下标
            int i = key.threadLocalHashCode & (len-1);
			// 判断i位置entry是否为空，不为null就调用父类Reference的get方法得到 ThreadLocal对象 ，向后 指定步长遍历
            for (Entry e = tab[i];e != null;e = tab[i = nextIndex(i, len)]) {
                ThreadLocal<?> k = e.get();
			// i位置的元素 就是我们遍历寻找的元素，新值覆盖旧值
                //如果是同一个对象，k==key。就替换Entry里面的value值
                if (k == key) {
                    e.value = value;
                    return;
                }
//下标的对象k为null。就放入改位置，如果有其他的，就往下一个i+1位置上找
                /**
                从 staleSlot 向前遍历数组，直到 Entry 为空时停止遍历。这一步的主要目的是查找 staleSlot 前面过期的 Entry 的数组下标 slotToExpunge。
从 staleSlot 向后遍历数组
若 Entry 的 key 与给定的 key 相等，将该 Entry 与 staleSlot 下标的 Entry 互换位置。目的是为了让新增的 Entry 放到它「应该」在的位置(hash冲突解决后的位置)。
若找不到相等的 key，说明该 key 对应的 Entry 不在数组中，将新值放到 staleSlot 位置。该操作其实就是处理哈希冲突的「线性探测」方法：当某个位置已被占用，向后探测下一个位置。
若 staleSlot 前面存在过期的 Entry，则执行清理操作。
                */
                if (k == null) {
                    replaceStaleEntry(key, value, i);
                    return;
                }
            }
			/**
			如果计算后的坐标获取到的entry为null，就new一个Entry对象并保存进去，
			调用cleanSomeSlots()对table进行清理，
			如果没有任何Entry被清理，并且表的size超过了阈值，就会调用rehash()方法。 
			cleanSomeSlots()会调用expungeStaleEntry清理陈旧过时的Entry。
			
			*/
            tab[i] = new Entry(key, value);
            int sz = ++size;
            if (!cleanSomeSlots(i, sz) && sz >= threshold)
                /**
                rehash 清理数组中过期的 Entry
若清理后 Entry 的数量大于等于 threshold 的 3/4，则执行 resize 方法进行扩容
resize 方法：Entry 数组扩容
                */
                rehash();
        }

		//rehash则会调用expungeStaleEntries()方法清理所有的陈旧的Entry，然后在size大于阈值的3/4时调用resize()方法进行扩容。
		private void rehash() {
            expungeStaleEntries();
            if (size >= threshold - threshold / 4)
                /**
                创建一个新数组，长度为原数组的 2 倍；
从下标 0 开始遍历旧数组的所有元素
若元素已过期（key 为空），则将 value 也置空
将未过期的元素移到新数组  最后一个设置新的阈值
                */
                resize();
        }
```



>  threadlocalmap的 hashcode是通过AtomicInteger加上0x61c88647来实现的。保证每一个桶都可以均匀分布，而不会出堆积，因为冲突比较少 又均匀分布 所以采用来 **线性探测的方式**来 解决hash冲突 

```java
//每个对象都有一个HashCode来标示自己的唯一性
    private static final int HASH_INCREMENT = 0x61c88647;
//原子类保证线程安全，保证每个对象的hashcode唯一，并且是静态的
    private static int nextHashCode() {
        return nextHashCode.getAndAdd(HASH_INCREMENT);
    }

```



> 解决 key为null 而value不为null的 数据

**replaceStaleEntry**方法和**cleanSomeSlots**方法



#### getEntry

>

```java
  private Entry getEntry(ThreadLocal<?> key) {
      //	通过计算出的下标从table中取出entry
            int i = key.threadLocalHashCode & (table.length - 1);
            // 获取entry 根据下标 i 从table中
      		Entry e = table[i];
      		// 当前计算出来的key 和 查找的key 相等 就直接返回 
            if (e != null && e.get() == key)
                return e;
            else
                return getEntryAfterMiss(key, i, e);
        }

	 private Entry getEntryAfterMiss(ThreadLocal<?> key, int i, Entry e) {
            Entry[] tab = table;
            int len = tab.length;

            while (e != null) {
                ThreadLocal<?> k = e.get();
                if (k == key)
                    // 找到了 就返回
                    return e;
                if (k == null)
                    // 解决 脏entry的问题
                    expungeStaleEntry(i);
                else
                    // 继续向后遍历 环形查找
                    i = nextIndex(i, len);
                e = tab[i];
            }
            return null;
        }
```

==通过nextIndex往后环形查找，如果找到和查询的key相同的entry的话就直接返回，如果在查找过程中遇到脏entry的话使用expungeStaleEntry方法进行处理。==



#### remove

>移除 ThreadLocal 对应的 Entry
>
>流程：
>
>1. 获取当前线程的 ThreadLocalMap
>2. 以当前 ThreadLocal 做为 key，从 Map 中查找相应的 Entry，将 Entry 的 key 置空
>3. 将该 ThreadLocal 对应的 Entry 置空，并向后遍历清理 Entry 数组，也就是 expungeStaleEntry 方法的操作

```java
 private void remove(ThreadLocal<?> key) {
            Entry[] tab = table;
            int len = tab.length;
            int i = key.threadLocalHashCode & (len-1);
            for (Entry e = tab[i];
                 e != null;
                 e = tab[i = nextIndex(i, len)]) {
                if (e.get() == key) {
                    // 把 entry中的 key 赋null
                    e.clear();
                    // 通过expungeStaleEntry（清理过期的 Entry） 解决脏entry   让下一次系统GC回收
                    expungeStaleEntry(i);
                    return;
                }
            }
        }

/**
清空给定位置的 Entry
从给定位置的下一个开始向后遍历数组
若遇到 Entry 为 null，结束遍历
若遇到 key 为空的 Entry（即过期的），就将该 Entry 置空
若遇到 key 不为空的 Entry，而且经过计算，该 Entry 并不在它「应该」在的位置，则将其移动到它「应该」在的位置
返回 staleSlot 后面的、Entry 为 null 的索引下标
cleanSomeSlots：清理一些槽（Slot）
*/
// staleSlot 表示过期的槽位（即 Entry 数组的下标）
private int expungeStaleEntry(int staleSlot) {
    Entry[] tab = table;
    int len = tab.length;

    // 1. 将给定位置的 Entry 置为 null
    tab[staleSlot].value = null;
    tab[staleSlot] = null;
    size--;

    // Rehash until we encounter null
    Entry e;
    int i;
    // 遍历数组
    for (i = nextIndex(staleSlot, len);
         (e = tab[i]) != null;
         i = nextIndex(i, len)) {
        // 获取 Entry 的 key
        ThreadLocal<?> k = e.get();
        if (k == null) {
            // 若 key 为 null，表示 Entry 过期，将 Entry 置空
            e.value = null;
            tab[i] = null;
            size--;
        } else {
            // key 不为空，表示 Entry 未过期
            // 计算 key 的位置，若 Entry 不在它「应该」在的位置，把它移到「应该」在的位置
            int h = k.threadLocalHashCode & (len - 1);
            if (h != i) {
                tab[i] = null;
               //扫描直到null出现  因为有些entry已经可能过期
                while (tab[h] != null)
                    h = nextIndex(h, len);
                tab[h] = e;
            }
        }
    }
    return i;
}
```





### 总结

- set:
  - 以当前的threadlocal为key，要添加的object为value 组成一个entry，放入 threadlocalmap中的entry数组
  - 计算entry的位置，如果这个位置为空就 直接放在这里，并清理脏entry 然后满足条件扩容，
  - 如果有hash冲突，线性探测向后查找数组中为空的、或者已经过期的槽，用新值替换。
- get:
  - 当前threadlocal为key，从entry数组中查找对应的entry的value
  - 若 ThreadLocalMap 未初始化，则用给定初始值将其初始化
  - 若 ThreadLocalMap 已初始化，从 Entry 数组查找 key
- remove:
  - 以当前 ThreadLocal 为 key，从 Entry 数组清理掉对应的 Entry，并且再清理该位置后面的、过期的 Entry
- 每一步都在为防止内存泄漏做努力



## 内存泄漏



> entry 继承了 WeakReference  它是一个**弱引用**类型，这表示当系统GC时 就会被回收（弱引用，生命周期只能存活到下次GC前）
>
> 而这不是我们想要得到的结果，因为这可能导致 threadlocalmap中的key为null，就无法访问这些key为null的value
>
> key是弱引用，value并不是弱引用



<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201124123631932.png" alt="image-20201124123631932" style="zoom: 80%;" />

上面图解释：

- 1.ThreadLocal Ref 在栈中持有 Thread Local的一个强引用， ThreadLocalMap有一个 ThreadLocal的弱引用
- 2.Thread 持有一个 ThreadLocalMap的一个强引用



情况1：方法执行完毕，线程正常死亡， 强引用断开，弱引用在GC时断开 回收 ，不存在内存泄漏

情况2：如果使用线程池，线程一直存活，需要我们去处理脏entry 比如结束前调用remove方法等



在threadlocalmap源码中 有很多方法都会去处理脏entry，如 cleanSomeSlots(), expungeStaleEntry(),replaceStaleEntry，还有 Thread的方法 Thread.exit()



- 被废弃了的ThreadLocal所绑定对象的引用，会在以下4情况被清理。
  1. Thread执行结束时。
  2. 当Thread的ThreadLocalMap的threshold超过最大值时。rehash
  3. 向Thread的ThreadLocalMap中存放一个ThreadLocal，hash算法没有命中既有Entry,而需要新建一个Entry时。
  4. 手工通过ThreadLocal的remove()方法或set(null)。
  5. 在使用线程池的情况下，没有及时清理ThreadLocal，不仅是内存泄漏的问题，更严重的是可能导致业务逻辑出现问题。所以，使用ThreadLocal就跟加锁完要解锁一样，用完就清理。



==为什么使用弱引用，使用弱引用会尽可能在生命周期中防止内存泄漏，如果强引用就会出现 GC时ThreadLocalMap保存ThreadLocal的强引用==

- key 使用弱引用：引用的ThreadLocal的对象被回收了，由于ThreadLocalMap持有ThreadLocal的弱引用，即使没有手动删除，ThreadLocal也会被回收。value在下一次ThreadLocalMap调用set,get，remove的时候会被清除。



##  解决hash冲突



> 为了解决散列冲突，主要采用下面两种方式： **分离链表法**（separate chaining）和**开放定址法**（open addressing）

`http://www.nowamagic.net/academy/detail/3008050`

`http://www.nowamagic.net/academy/detail/3008060`

> **`ThreadLocalMap` 采用线性探测的方式来解决 Hash 冲突**。所谓线性探测，就是根据初始 key 的 hashcode 值确定元素在 table 数组中的位置，如果发现这个位置上已经被其他的 key 值占用，则利用固定的算法寻找一定步长的下个位置，依次判断，直至找到能够存放的位置。
>
> 如下标 i 有值 就寻找i+1 的位置，依次往下
>
> hashmap和threadlocalmap 一个实现map接口 一个没有实现

```java
    /**
         * Increment i modulo len.
         */
        private static int nextIndex(int i, int len) {
            return ((i + 1 < len) ? i + 1 : 0);
        }

        /**
         * Decrement i modulo len.
         */
        private static int prevIndex(int i, int len) {
            return ((i - 1 >= 0) ? i - 1 : len - 1);
        }

```

==ThreadLocalMap解决Hash冲突的方式就是简单的步长加1或减1，寻找下一个相邻的位置。==



> 解决建议：一个线程存一个threadlocal变量



## 使用场景

**ThreadLocal 不是用来解决共享对象的多线程访问问题的**，数据实质上是放在每个thread实例引用的threadLocalMap,也就是说**每个不同的线程都拥有专属于自己的数据容器**

threadLocal适用于 **共享对象会造成线程安全** 的业务场景。

如果开发者希望将类的某个静态变量（user ID或者transaction ID）与线程状态关 联，则可以考虑使⽤ThreadLocal。 

最常⻅的ThreadLocal使⽤场景为⽤来解决数据库连接、Session管理等。数据库连 接和Session管理涉及多个复杂对象的初始化和关闭。如果在每个线程中声明⼀些 私有变量来进⾏操作，那这个线程就变得不那么“轻量”了，需要频繁的创建和关闭 连接。



