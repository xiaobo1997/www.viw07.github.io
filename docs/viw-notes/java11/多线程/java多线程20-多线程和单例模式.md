[TOC]

# 1.单例模式下的多线程 



## 1.饿汉模式(安全问题)

> 调用方法之前就加载好，立即加载

```java
public class Demo1 {
    private static Demo1 demo1 = new Demo1();
    public Demo1(){}
    public static Demo1 getInstance(){
        return demo1;
    }
    public static void main(String[] args) {
    }
}
```



## 2.DCL (双检查锁)实现延迟加载单例模式 线程安全问题



```java
public class Demo2 {
    private static volatile Demo2 demo2;

    public Demo2() {
    }

    public static Demo2 method() {

        try {
            Thread.sleep(1000);
            synchronized (Demo2.class) {
                if (demo2 == null) {
                    demo2 = new Demo2();
                }
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return demo2;
    }
}
```

## 3.静态内部类实现单例模式

```java
public class Demo3 {
    public static class  A{
        private static  A a = new A();
        public A() {
        }
        public static A getInstance(){
            return a;
        }
    }
}

```

## 4.静态代码块实现单例模式

```java
class A{
static A a = new A();
private A(){}
static{
a = new A();
}
public static A getInstance(){
return a;
}
}
```

## 5.ENUM实现

![image-20201127004715998](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201127004715998.png)