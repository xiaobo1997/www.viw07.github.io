[TOC]





#  lambda-01







##  概念



lambda表达式相当于懒人表达式，如下一段代码：

```java

    /**
     * 匿名匿名类和 lambda
     */
    @Test
    public void test1() {
        new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("原匿名内部类");
            }
        }).start();
        //=============
        new Thread(() -> {
            System.out.println("lambda");//方法体
        }).start();
        // 进一步简化
        new Thread(() -> System.out.println("lambda----")).start();

    }
```





```java
 /**
     * Comparable   lambda 表达式
     */
    @Test
    public void test2() {
        Comparator<Integer> comparator1 = new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return o1-o2;
            }
        };
        System.out.println(comparator1.compare(1,2));
        // ==================  
        Comparator<Integer> comparator2 = (Integer o1, Integer o2) ->o1-o2;// 形参Integer也可以省
        System.out.println(comparator2.compare(2,1));
        // ================== 方法引用
        Comparator<Integer> comparator3 = Integer::compareTo;
        System.out.println(comparator3.compare(3,3));
    }
//printout
-1
1
0
```



## 前提条件



1.满足函数式接口，接口只有一个抽象方法，方法参数和局部变量是接口



## 基本表达式



> 总结
>
> `()->{}` 
>
> 有类型推断，可以省略参数类型，如果只有一个参数，可以实例  `()`
>
> 如果 方法体只有一条执行语句，可以实例 `{}`和 `return`



```java
/**
     * 无返回值无参
     */
    @Test
    public void test1() {
        new Thread(() -> {
            System.out.println("无参 无返回值");
        }).start();
    }


    /**
     * 一个入参，无返回值
     */
    @Test
    public void test2() {
        Consumer<String> consumer1 = new Consumer<String>() {
            @Override
            public void accept(String s) {
                System.out.println(s);
            }
        };
        consumer1.accept("aaa");
        // ================
        Consumer<String> consumer2 = (String s) -> {
            System.out.println(s);
        };
        // Consumer<String> consumer2 = (s) -> System.out.println(s);
        consumer2.accept("bbb");
    }


    /**
     * 类型推断
     * 类似于 List<String> list = new ArrayList<>();
     */
    @Test
    public void test3() {
        Consumer<String> consumer1 = new Consumer<String>() {
            @Override
            public void accept(String s) {
                System.out.println(s);
            }
        };
        consumer1.accept("aaa");
        // ================
        Consumer<String> consumer2 = (s) -> System.out.println(s);
        consumer2.accept("bbb");
    }

    /**
     * 如果只需要一个参数，可以省略参数的小括号
     */
    @Test
    public void test4() {
        Consumer<String> consumer1 = new Consumer<String>() {
            @Override
            public void accept(String s) {
                System.out.println(s);
            }
        };
        consumer1.accept("aaa");
        // ================
        Consumer<String> consumer2 = s -> System.out.println(s);
        consumer2.accept("bbb");
    }


    /**
     * 如果两个及以上参数，并且多条执行语句，还有返回值的情况下
     */
    @Test
    public void test5() {
        Comparator<Integer> comparator1 = new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return o1 - o2;
            }
        };
        System.out.println(comparator1.compare(12, 13));
        // ===========
        Comparator<Integer> comparator2 = (Integer o1, Integer o2) -> {
            System.out.println(o1);
            System.out.println(o2);
            return o1 - o2;
        };
        System.out.println(comparator2.compare(12, 13));
    }

    /**
     * 如果 只有一条执行语句， return 和 {} 都可以省略
     */
    @Test
    public void test6() {
        Comparator<Integer> comparator1 = new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return o1 - o2;
            }
        };
        // ===========
        Comparator<Integer> comparator2 = (o1,o2) -> o2.compareTo(o1);
        System.out.println(comparator2.compare(1, 2));
    }
```



## 自定义实现标准表达式

### 测试1-无参无返回

![image-20210523181229109](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210523181229109.png)



### 测试2-有参有返回值

![image-20210523182112327](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210523182112327.png)

```java
public interface Book {
    /**
     */
    public abstract int name(String name);
}


// ===================

public class BookPremiseTest1 {

    static void test(Book book) {
        int a = book.name("西游记");
        System.out.println("返回值是"+a);
    }

    public static void main(String[] args) {
        test((a) -> {
            System.out.println("名字是"+a);
            return 1;
        });

        System.out.println("=================");
        test(new Book() {
            @Override
            public int name(String name) {
                System.out.println("书名"+name);
                return 0;
            }
        });
    }
}


// print out 
名字是西游记
返回值是1
=================
书名西游记
返回值是0

```



### 测试3-匿名内部类 和lambda排序比较

```java
public class LambdaTest03 {

    public static void main(String[] args) {
        List<Book> list = new ArrayList<>();
        list.add(new Book("aaa",2));
        list.add(new Book("bbb",1));
        list.add(new Book("ccc",3));
        //匿名内部类排序
//        Collections.sort(list, new Comparator<Book>() {
//            @Override
//            public int compare(Book o1, Book o2) {
////                return o1.price-o2.price;
//                return Integer.compare(o1.getPrice(),o2.getPrice());
//            }
//        });

        // lambda
        Collections.sort(list, (o1,o2)->
                o1.getPrice()-o2.getPrice()
        );

        list.stream().forEach((a)->{
            System.out.println(a);
        });
    }
}

@Data
@AllArgsConstructor
class Book{
    String name;
    int price;
}

// print out

Book(name=bbb, price=1)
Book(name=aaa, price=2)
Book(name=ccc, price=3)
```



## lambda和匿名内部类的比较



1.类型不一样

- 匿名内部类的方法形参可以是类 接口 抽象类
- lambda 必须是接口

2.抽象方法数量不一样

- lambda只能一个，匿名内部类可以多个

3.实现原理不一样

- lambda动态生成class；匿名内部类在编译后形成class文件，如 Book$1.class



**如果接口中只有一个方法，可以使用lambda，如果多个匿名内部类**

