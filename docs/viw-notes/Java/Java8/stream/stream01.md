




# stream



## 概念和基础



> stream不存储数据，只计算数据



## 使用



> 使用stream的三个步骤
>
> 1.创建，一个数据源，或者一个流
>
> 2.中间操作 (具体操作)
>
> 3.终止操作 (停止，比如输出等)



### 创建 stream流

```java

/**
 * @Author: xhb
 * @email xiaobo97@163.com
 * gitee: https://gitee.com/xiaobo97
 * @Date: 2021/5/22 10:47
 * @description: stream流基础实例化
 */
public class StreamCollection01 {
    public static void main(String[] args) {

    }

    /**
     * 通过集合
     */
    @Test
    public void test1() {
        ArrayList<Book> books = new ArrayList<>();
        books.add(new Book("aaa"));
        books.add(new Book("bbb"));
        //  get 顺序 stream
        Stream<Book> stream = books.stream();
        // get 并行 流
        Stream<Book> bookStream = books.parallelStream();

    }

    /**
     * 通过数组
     */
    @Test
    public void test2() {
        int[] ints = {1, 3, 2};
        IntStream stream = Arrays.stream(ints);
    }

    /**
     * 通过 stream of
     */
    @Test
    public void test3() {
        Stream<Integer> stream = Stream.of(1, 3, 2);
    }

    /**
     * 创建 无限 流
     * iterate    generate
     */
    @Test
    public void test4() {
        // 迭代  iterate(final T seed, final UnaryOperator<T> f)
        Stream.iterate(1, t -> t + 2).limit(5).forEach(System.out::println); //使用limit 终止  否则一直遍历下去

        // 生成iterate(final T seed, final UnaryOperator<T> f)
        Stream.generate(Math::random).limit(5).forEach(System.out::println);
    }
}

@AllArgsConstructor
@Data
class Book {
    String name;
}
```



### 筛选和分片

```java
@AllArgsConstructor
@Data
public class Emp {

    Integer id;
    String name;

    static List<Emp> get() {
        List<Emp> list = new ArrayList<>();
        list.add(new Emp(1, "aa"));
        list.add(new Emp(4, "cc"));
        list.add(new Emp(4, "ff"));
        list.add(new Emp(2, "bb"));
        list.add(new Emp(2, "ee"));
        list.add(new Emp(2, "dd"));
        return list;
    }
}
```



#### filter

>

```java

    /**
     * filter(Predicate p) 过滤
     */
    @Test
    public  void test1() {

        list.stream().filter(t->
            t.getId()>2
        ).forEach(System.out::println);
        // ============
        list.stream().filter(t->
                t.getId()>2
        ).forEach(t-> System.out.println(t.getName()));
    }
```



#### distinct

>

```java


    /**
     * limit(n) 截断
     */
    @Test
    public void test2() {
        list.stream().limit(2).forEach(System.out::println);

    }
```



#### limit

>

```java
 /**
     * skip(n) 跳过前n个元素，不足抛异常
     */
    @Test
    public void test3() {
        list.stream().skip(2).forEach(t-> System.out.println(t.getName()));
    }

```





#### skip



>

```java

    /**
     * 筛选
     */
    @Test
    public void test4() {
        list.stream().distinct().forEach(System.out::println);
    }

```







###  java8-map映射



![image-20210530012241235](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210530012241235.png)



#### map

>把一个流转换成另外一个流
>
>如 类型的转变
>
>![image-20210530134615216](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210530134615216.png)

接收一个function接口的参数。`<R> Stream<R> map(Function<? super T, ? extends R> mapper);`

点进去function可以看到，入参一个T  返回一个R  类型是不一样的

![image-20210530134849068](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210530134849068.png)

```java
   /**
     * map
     */
    @Test
    public void test1(){
        List<String> res = new ArrayList<>();
        List<Emp> list = Emp.get();
//        list.stream().map(value->value.getName()+"_hello").limit(2).forEach(a->res.add(a));

        Stream<String> stream = list.stream().map(value -> value.getName() + "_hello");
        stream.limit(2).forEach(a->res.add(a));

        res.forEach(System.out::println);
        // string  stream  =>  integer  stream
        Stream<Integer> integerStream = Arrays.asList("1", "2", "3").stream().map(Integer::new);
        integerStream.forEach(System.out::println);//终止操作

        //如果是一个对象，对象某一个值作为key，对象作为value
        
    }


```






#### flatmap

> use flatmap    merge  stream

```java
 // string => char
    public static Stream<Character> stringToCharStream(String s) {
        List<Character> res = new ArrayList<>();
        for (char c : s.toCharArray()) {
            res.add(c);
        }
        return res.stream();
    }

    /**
     * flatMap(function f)  把流合并连接成一个大流 如  1 2 3 的流  和 4 5 6的流   合成  1 2 3 4 5 6 的 大流
     */
    @Test
    public void test2() {
        List<String> strings = Arrays.asList("ab", "cd");

        // map   user  twice  foreah
        strings.stream().map(StreamTest03::stringToCharStream).forEach(i->{
            i.forEach(System.out::println);
        });
        System.out.println("===============");
        // user  flatmap  user once  foreach
        strings.stream().flatMap(StreamTest03::stringToCharStream).forEach(System.out::println);
    }


// print out
a
b
c
d
===============
a
b
c
d
```



### 排序



#### sorted

> 自然排序

```java

    /**
     * sorted  自然排序
     */
    @Test
    public void test1() {
        /**
         *  Integer id;
         *  String name;
         */
        List<Emp> emps = Emp.get();
        emps.stream().map(i -> i.getId()).sorted().forEach(System.out::println);
//        emps.stream().sorted().forEach(System.out::println);//抛异常， 因为 emp 没有实现 Comparable接口
    }
// print out
1
2
2
2
4
```



#### sorted(Comparator c)

> 自定义规则排序

```java
    /**
     * 定制排序 sorted(Comparator c)
     */
    @Test
    public void test2() {
        List<Emp> emps = Emp.get();
//        emps.stream().sorted((a,b)->b.getId()-a.getId()).forEach(System.out::println);
        emps.stream().sorted((a,b)->{
            int compare = Integer.compare(b.getId(), a.getId());// Integer.compare(b.getId(), a.getId()); = -Integer.compare(a.getId(), b.getId());
            if(compare!=0) return compare;
            else return a.getName().compareTo(b.getName());
        }).forEach(System.out::println);
    }

// print out
Emp(id=4, name=cc)
Emp(id=4, name=ff)
Emp(id=2, name=bb)
Emp(id=2, name=dd)
Emp(id=2, name=ee)
Emp(id=1, name=aa)
```



### 匹配和查找--终止操作



![image-20210530145810672](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210530145810672.png)



#### allMatch

```java

    /**
     * allMatch(Predicate p) 检查是否匹配所有元素
     */
    @Test
    public void test1(){
        List<Emp> emps = Emp.get();
      //  boolean allMatch = emps.stream().allMatch(emp -> emp.getId() == 2);// false
        boolean allMatch = emps.stream().allMatch(emp -> emp.getId() >=1); //true
        System.out.println(allMatch);
    }
```



#### anyMatch

```java

    /**
     * anyMatch(Predicate p)  至少有一个匹配的
     */
    @Test
    public void test2(){
        boolean b = emps.stream().anyMatch(emp -> emp.getName() == "aa");
        System.out.println(b);// true
    }
```



#### noneMatch

```java
  /**
     * noneMatch(Predicate p)  没有匹配的元素
     */
    @Test
    public void test3(){
        System.out.println(emps.stream().noneMatch(emp -> emp.getId() <= 0)); // true

    }
```



#### findFirst



```java

    /**
     * findFirst    return first element
     */
    @Test
    public void test4(){
        emps.stream().forEach(System.out::println);
        Optional<Emp> first = emps.stream().findFirst();
        System.out.println(first);// Optional[Emp(id=1, name=aa)]
    }
// print out
Emp(id=1, name=aa)
Emp(id=4, name=cc)
Emp(id=4, name=ff)
Emp(id=2, name=bb)
Emp(id=2, name=ee)
Emp(id=2, name=dd)
Optional[Emp(id=1, name=aa)]
```

#### findAny

```java

    /**
     * findAny  return anyone element
     */
    @Test
    public void test5(){
        Optional<Emp> any = emps.parallelStream().findAny();
        System.out.println(any); //   Optional[Emp(id=2, name=bb)]
       
    }
```



#### count

>统计流中元素数量

```java
 /**
     * count 统计
     */
    @Test
    public void test5(){
        System.out.println(list.stream().count());
    }
```

#### max

> 返回排序规则下的最大值

```java

    /**
     * max(Comparator c)  return max element
     */
    @Test
    public void test6(){
        Stream<Integer> max = emps.stream().map(a->a.getId());
        System.out.println(max.max(Integer::compare));
    }
//print out
Optional[4]
```



#### min

> 返回排序规则下的最小   值

```java
 /**
     * min(Comparator)
     */
    @Test
    public void test7(){
        Stream<Integer> max = emps.stream().map(a->a.getId());
        System.out.println(max.min(Integer::compare));
    }
}
//print out
Optional[1]
```



#### foreach

> 内部迭代

```

```



### 归约



![image-20210602001137069](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210602001137069.png)

#### reduce(T iden ,BinaryOperator b)

>

```java
   /**
     * reduce(T iden,BinaryOperator b)
     * 0 是初值
     * BinaryOperator 是 传入 两个参数，返回一个结果
     */
    @Test
    public void test1() {
        System.out.println(Arrays.asList(1, 2, 3).stream().reduce(0, Integer::sum));
    }

```



#### reduce(BinaryOperator b)

>

```java
  /**
     * reduce(BinaryOperator b)
     */
    @Test
    public void test2() {
        List<Emp> emps = Emp.get();
        Stream<Integer> stream = emps.stream().map(Emp::getId);
        System.out.println(stream.reduce(Integer::sum));//Optional[15]
    }
```



