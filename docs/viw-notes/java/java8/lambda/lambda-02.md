


<!-- TOC -->

- [函数式接口](#函数式接口)
  - [概念](#概念)
    - [lambda的前提](#lambda的前提)
  - [接口方法和默认方法](#接口方法和默认方法)
    - [接口默认方法](#接口默认方法)
    - [接口静态方法](#接口静态方法)
    - [区别](#区别)
- [内置函数式接口](#内置函数式接口)
  - [四个内置函数接口](#四个内置函数接口)
    - [Consumer-消费者](#consumer-消费者)
    - [Supplier-供应商](#supplier-供应商)
    - [Function](#function)
      - [代码片段1](#代码片段1)
      - [代码片段2-函数](#代码片段2-函数)
      - [代码片段3-addThen](#代码片段3-addthen)
      - [代码片段4-多参数to Map](#代码片段4-多参数to-map)
      - [代码片段5](#代码片段5)
    - [Predicate](#predicate)
  - [其它函数接口](#其它函数接口)
- [方法引用和构造器引用](#方法引用和构造器引用)
  - [方法引用](#方法引用)
  - [构造器引用](#构造器引用)

<!-- /TOC -->


# 函数式接口





## 概念





在java8对接口进行了增强，可以有默认方法，也可以有静态方法



> 函数式接口是什么

1.接口中只有一个抽象方法就是函数式接口，然后就可以通过lambad来创建函数接口中的对象，

> @FunctionInterface注解

可以使用  `@FunctionInterface` 来检测是否是函数式接口





### lambda的前提

> 使用lambda，满足函数式接口，接口中有且只有一个**接口方法**。
>
> 1.方法的参数或者 局部变量 是接口
>
> 2.这个接口中只有一个抽象方法

```java
    public static void main(String[] args) {
        // 方法的参数写法
        test(() -> {
            System.out.println("a");
        });
        // ===================================
        P p = new P() {
            @Override
            public void name() {
                System.out.println("===========");
            }
        };
        // ======================================
        //局部变量写法  对抽象方法name重写
        P p1 = () -> {
            System.out.println("b");
        };
        p1.name();
    }


    public static void test(P p) {
        p.name();
    }
}

/**
 * 接口有且只能有一个抽象方法
 */
@FunctionalInterface //检测 是否是函数接口
interface P {
    abstract void name();
//    abstract void name1();
}


// print out 
a
b
```





## 接口方法和默认方法



> jdk8对接口增强，接口中可放 静态变量，抽象方法，默认方法，静态方法。
>
> 方便利于扩展接口
>
> 比如Map接口，的foreach方法，有很多实现类，如果要扩展，所有的实现类都要去修改，这个时侯就可以使用默认方法，接口中的默认方法 实现类就不需要重写了，可以直接使用
>
> ```java
> interface xxx{
> 	xxx  default void xxx(){
> 		xxxx;
> 	}	
> }
> ```

### 接口默认方法



> 接口默认方法 实现类不需要重写，直接用，也可以重写 如  `Map#foreach()`
>
> 使用方法：
>
> 1.直接使用
>
> 2.重写默认方法

```

default void forEach(BiConsumer<? super K, ? super V> action) {
    	xxxxx
    }
```

> 使用

```java

public class LambdaTest05 {

    @Test
    public  void test1(){
        new A().test();
        new B().test();
    }

   
}

interface DefaultFunction{
    public default void test(){
        System.out.println("使用了默认方法");
    };
}

class A implements DefaultFunction{

}

class B implements DefaultFunction{
    @Override
    public void test() {
        System.out.println("B 重写DefaultFunction接口的默认方法了 ");
    }
}

// print out 
使用了默认方法
B 重写DefaultFunction接口的默认方法了 
```



### 接口静态方法



>`接口名.方法名`

```java
{
  @Test
    public  void test2() {
        StaticFunction.test1();
    }
 }
 
 interface StaticFunction{
    public static void test1(){
        System.out.println("接口静态方法");
    }
}

class C implements StaticFunction{
}
// print out    
接口静态方法    
```



### 区别



![image-20210525005207838](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210525005207838.png)





# 内置函数式接口











## 四个内置函数接口



> juc包下为我们提供了默认的函数式接口使用
>
> consumer  supplier  function  predicate

![image-20210523184240341](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210523184240341.png)



### Consumer-消费者



> 消费型接口，形参是T，没有返回值。表明消费

```java
  /**
     * consumer
     */
    @Test
    public void test1() {
        //原
        conMoney(0.1, new Consumer<Double>() {
            @Override
            public void accept(Double aDouble) {
                System.out.println("accept:" + aDouble);
            }
        });
        // lambda
        conMoney(0.1, aDouble ->
                System.out.println("accept:" + aDouble)
        );

    }

    public void conMoney(double a, Consumer<Double> b) {
        b.accept(a);
    }
//
accept:0.1
accept:0.1

```



### Supplier-供应商

https://javabydeveloper.com/java-8-supplier-functional-interface-with-examples/

> Supplier没有接收参数，返回了一个泛型T的结果

```

public static String supp(Supplier<String> supplier){
        return supplier.get();
    }

```





### Function

> 这个函数式接口 接收一个T 返回一个R

![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20220708161912.png)


#### 代码片段1

```

 Function<String,Integer> function = x->x.length();
        Integer aa = function.apply("aa");
        System.out.println("aa");

```

#### 代码片段2-函数

```
  public static <T,R> R function(T t, Function<T,R> function){
        return function.apply(t);
    }

    public static void main(String[] args) {
        function(1, new Function<Integer, String>() {
            @Override
            public String apply(Integer integer) {
                return integer.toString()+"2";
            }
        });
    }
```

#### 代码片段3-addThen

```

        Function<String,Integer> function = x->x.length();
        Integer aa = function.apply("aa");
        System.out.println("aa");
        
        Function<Integer,Integer> function1 = x->x*2;
        Integer hello = function.andThen(function1).apply("hello");
        System.out.println(hello);

```

#### 代码片段4-多参数to Map

```

        public static <T,R> Map<T,R> functionmap(List<T> list,Function<T,R> function){
        Map<T,R> map= new HashMap<>();
        for (T s : list) {
            map.put(s,function.apply(s));
        }
        return map;
    }


        Map<String, Integer> functionmap = functionmap(Arrays.asList("a", "b"), new Function<String, Integer>() {
            @Override
            public Integer apply(String s) {
                return s.length();
            }
        });

```

#### 代码片段5

```

public static <T,X extends Person> X  function11(T t, Function<T, ? extends X> function){
        return function.apply(t);
    }

```


### Predicate

> 实现抽象方法来判定

```java

    /**
     * predicate    boolean test(T t);
     */
    @Test
    public void test2() {
        List<String> list = Arrays.asList("a", "b", "c");
        // 匿名内部类
        List<String> a = predicateTest(list, new Predicate<String>() {
            @Override
            public boolean test(String s) {
                if (s.equals("a")) return false;
                return true;
            }
        });
        a.stream().forEach(a1 -> System.out.println(a1));
        System.out.println("===============================");
        //lambda
        List<String> b = predicateTest(list, s -> {
            if (s.equals("b")) return false;
            return true;
        });
        b.stream().forEach(b1 -> System.out.println(b1));
    }

    // Predicate来过滤  规则由Predicate抽象方法来实现
    public List<String> predicateTest(List<String> list, Predicate<String> predicate) {
        ArrayList<String> integers = new ArrayList<>();
        for (String s : list) {
            if (predicate.test(s)) {
                integers.add(s);
            }
        }
        return integers;
    }

// print out
b
c
===============================
a
c
```



## 其它函数接口



> 其它接口



![image-20210524225909629](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210524225909629.png)





# 方法引用和构造器引用



## 方法引用



> 是什么

方法引用可以理解为进阶的lambda表达式，当lambda方法体已经有实现方法时 就可以用方法引用

例子如下

```java
  public static void sum2(int[] arr) {
        //return  Arrays.stream(arr).sum();
        int res = 0;
        for (int a : arr) {
            res += a;
        }
        System.out.println(res);
    }


    public void testMethod1(int[] arr, Consumer<int[]> consumer) {
        consumer.accept(arr);
    }
    
      @Test
    public void test1() {
        int[] arr = {1, 2, 3};
        testMethod1(arr, a -> {
            int res = 0;
            for (int b : arr) {
                res += b;
            }
            System.out.println(res);
        });
        System.out.println("=================");
        testMethod1(arr, a -> {
            sum2(a);
        });
        System.out.println("================");
        testMethod1(arr, LambdaTest06::sum2);
    }
// print out
6
=================
6
================
6
```



> 怎么使用和使用场景
>
> 使用 `::` 运算符
>
> 注意事项：引用方法的参数和返回值( `void sum2(int[] arr)` ) 需要和  所实现的方法 参数和返回值一致 ( `Consumer<int[]>` )

**如果使用lambda中的方法体已经有实现方法了，就可以使用方法引用运算符简化代码，如上面的 类::方法**



> 常见的引用方式
>
> 1.**对象实例::实例方法**
>
> 2.**类名::静态方法**
>
> 3.**类名::普通方法**
>
> 4.类名::new调用的构造器 
>
> 5.TypeName[]::new  String[]::new 调用数组的构造器

![image-20210525012102813](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210525012102813.png)





## 构造器引用

