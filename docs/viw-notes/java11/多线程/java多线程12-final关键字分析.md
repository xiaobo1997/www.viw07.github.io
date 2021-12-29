[TOC]

![image-20201124000537616](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201124000537616.png)

# final



## 使用



> 命名规则

按照惯例，带有恒定初始值的 **final** **static** 基本变量（即编译时常量）命名全部使用大写，单词之间用下划线分隔。

```java
public static final int VALUE_SIZE = 1;
```



> final常量：
>
> 1.这个常量可以是编译时常量
>
> 2.也可以是运行初始化就不变的值

两种情况

- 如果用一个final 修饰一个基本数据类型 ，那么这个数值是不可变的

  - ![image-20201123222314087](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201123222314087.png)

- 如果是引用类型，那么这个引用是不可变的，但是这个引用对象的值是可改变的

  - ![image-20201123223751843](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201123223751843.png)

  - ```java
    public class Final01 {
    
        private  final static int i = 1;
        private  final static int[]a = {1,2};
    
        public static void main(String[] args) {
            Final01 final01 = new Final01();
            int[] ints = {1};
            a[1] = 3;
            System.out.println(a[1]); // 3
        }
    }
    ```

    

==将 **final** 值定义为 **static** 和非 **static** 的区别。此区别只有当值在运行时被初始化时才会显现，因为编译器对编译时数值一视同仁。（而且编译时数值可能因优化而消失。）当运行程序时就能看到这个区别。==

final修饰的属性表明是一个常数（创建后不能被修改）。static final修饰的属性表示一旦给值，就不可修改，并且可以通过类名访问。

```java
package java8.multithreading.finalEx;

import java.util.Random;

/**
 * @Author: xiaobo
 * @Date: 2020/11/23 22:44
 */
public class final02 {
    private static Random random =  new Random(47);

    private final  int a = random.nextInt(10);
    private static  final int  b = random.nextInt(10);

    public static void main(String[] args) {
        final02 final02 = new final02();
        final02 final03   = new final02();
        System.out.println(final02.a); // 在编译时并不知道它的值  在运行时才知道
        System.out.println(final03.a);
        System.out.println(final02.b); //在加载时已经被初始化，并不是每次创建新对象时都初始化。
        System.out.println(final03.b);// 在加载时已经被初始化，并不是每次创建新对象时都初始化。
    }
}

//
5
3
8  
8
```



> 参数
>
> 在参数列表中，将参数声明为 final 意味着在方法中不能改变参数指向的对象或基本变量：

![image-20201123225852475](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201123225852475.png)



```java
package java8.multithreading.finalEx;

/**
 * @Author: xiaobo
 * @Date: 2020/11/23 22:56
 */
class Gizmo {
    public void spin() {

    }
}
public class final05 {

    public static class FinalArguments {
        void with(final Gizmo g) {
            g = new Gizmo(); // Illegal -- g is final
        }
       ...
```



> 方法
>
> 如果任何方法声明为final，则不能覆盖它。但是可以重写。

 **final** 方法的原因有两个。

- 1.第一个原因是给方法上锁，防止子类通过覆写改变方法的行为
- 2.第二个原因是效率 在早期的 Java 实现中，如果将一个方法指明为 **final**，就是同意编译器把对该方法的调用转化为内嵌调用。当编译器遇到 **final** 方法的调用时，就会很小心地跳过普通的插入代码以执行方法的调用机制（将参数压栈，跳至方法代码处执行，然后跳回并清理栈中的参数，最终处理返回值），而用方法体内实际代码的副本替代方法调用。这消除了方法调用的开销。但是如果一个方法很大代码膨胀，你也许就看不到内嵌带来的性能提升，因为内嵌调用带来的性能提高被花费在方法里的时间抵消了。

参考 `on java 8`

**类中所有的 private 方法都隐式地指定为 finall，如果在子类中定义的方法和基类中的一个 private 方法签名相同，此时子类的方法不是重写父类方法，而是在子类中定义了一个新的方法。**

**原因：**

=="覆写"只发生在方法是基类的接口时。也就是说，必须能将一个对象向上转型为基类并调用相同的方法（这一点在下一章阐明）。如果一个方法是 **private** 的，它就不是基类接口的一部分。它只是隐藏在类内部的代码，且恰好有相同的命名而已。==

> 类
>
> 声明类不允许被继承。

 **final** 类禁止继承，类中所有的方法都被隐式地指定为 **final**，所以没有办法覆写它们



> 总结：

声明一个类为 final 就说明我们不希望它被继承，但是我们很难预见未来，

 **final** 方法过于约束。

引 on java 8:

​		尤其是 Java 1.0/1.1 的 **Vector** 类被广泛地使用，如果它的所有方法没有因为从效率考虑（这近乎是个幻想），而被指定为 **final**，可能会更加有用。



## 重排序规则

```java
 public class FinalExample {
        int i; // 普通变量 
        final int j; // final变量 
        static FinalExample obj;

        public FinalExample() { // 构造函数 
            i = 1; // 写普通域 
            j = 2; // 写final域 
        }

        public static void writer() { // 写线程A执行 
            obj = new FinalExample();
        }

        public static void reader() { // 读线程B执行 
            FinalExample object = obj; // 读对象引用
            int a = object.i; // 读普通域 
            int b = object.j; // 读final域 } 
        }
    }
```



> final域为基本数据类型

**final域禁止写重排序对构造函数外**

否则(下面是可能发生的执行顺序)：

- 1.写普通域（普通域不具有正确初始化过 这个保障 ）的操作被编译器重排序到了构造函数之外，所以导致读线程B错误地读取了 普通变量i初始化之前的值。而写final域的操作，被写final域的重排序规则“限定”在了构造函数 之内，读线程B正确地读取了final变量初始化之后的值。 
- 2.因为读的时候 obj 可能还没有正确的初始化 所以读到0，



**禁止读该对象的引用和读该对象的final域 重排序**

否则(下是可能发生的一种执行顺序)：

![image-20201123233120845](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201123233120845.png)

- 1.读i的时候，i 还没有被写入，这是错误的读取。



> final为引用类型



在构造函数内对一个final引用的对象的成员域 的写入，与随后在构造函数外把这个被构造对象的引用赋值给一个引用变量，这两个操作之 间不能重排序

```java
public class FinalReferenceExample {
        final int[] intArray; // final是引用类型 
        static FinalReferenceExample obj;

        public FinalReferenceExample() { // 构造函数
            intArray = new int[1]; // 1 
            intArray[0] = 1; // 2
        }

        public static void writerOne() { // 写线程A执行 
            obj = new FinalReferenceExample(); // 3 
        }

        public static void writerTwo() { // 写线程B执行 
            obj.intArray[0] = 2; // 4
        }

        public static void reader() { // 读线程C执行 
            if (obj != null) { // 5
                int temp1 = obj.intArray[0]; // 6 
            }
        }
    }
/**
1对 final域写入， 2是对 final域引用对象的 成员 的写入
3 是把 被构造出来的对象 赋给 一个引用变量  obj
1 3 不能重排序  2 3 不能重排序
*/
```

==因为读线程C 和写线程B 有竞争，所以 B 可能看不到2 这个数组元素的写入==



## 原理



> 通过插入 storestore屏障和loadLoad内存屏障
>
> 在 volatile内存语义中有



> final具体实现

写final域的重排序规则会要求编译器在final域的写之后，构造函数return 之前插入一个StoreStore障屏。读final域的重排序规则要求编译器在读final域的操作前面插入 一个LoadLoad屏障。 

##  逸出



> 对 象引用不能在构造函数中“逸出”。
>
> 简单说 在一个类的构造函数中，这个类还没有正确的构造完成 可是就有其他线程拿到了这个对象的引用

![image-20201123234246421](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201123234246421.png)

参考 `https://www.cnblogs.com/whatisjava/archive/2013/05/29/3106336.html`

```java
public class ThisEscape {
　　public ThisEscape(EventSource source) {
　　　　source.registerListener(new EventListener() {
　　　　　　public void onEvent(Event e) {
　　　　　　　　doSomething(e);
　　　　　　}
　　　　});
　　}
/**
当我们实例化ThisEscape对象时，会调用source的registerListener方法，这时便启动了一个线程，而且这个线程持有了ThisEscape对象（调用了对象的doSomething方法），但此时ThisEscape对象却没有实例化完成（还没有返回一个引用），所以我们说，此时造成了一个this引用逸出，即还没有完成的实例化ThisEscape对象的动作，却已经暴露了对象的引用。其他线程访问还没有构造好的对象，
*/
    
    // 正确如下
    //当构造好了SafeListener对象（通过构造器构造）之后，我们才启动了监听线程，也就确保了SafeListener对象是构造完成之后再使用的SafeListener对象。
    public class SafeListener {
　　private final EventListener listener;

　　private SafeListener() {
　　　　listener = new EventListener() {
　　　　　　public void onEvent(Event e) {
　　　　　　　　doSomething(e);
　　　　　　}
　　　　};
　　}
        public static SafeListener newInstance(EventSource source) {
　　　　SafeListener safe = new SafeListener();
　　　　source.registerListener(safe.listener);
　　　　return safe;
　　}
```



> JSR133 final语义的增加

只要对象是正确构造（被构造对象的引用在 构造函数中没有“逸出”），就不需要同步就可以保证任意线程都能看到这个final域在构造函数中被初始化之后的值





