[TOC]



# 并行流





## 认识和使用



### 获取

> 串行流

```java
/**
     * 串流  一个线程
     */ 
    @Test
    public void test1(){
        Emp.get().stream().map(a->{
            System.out.println(Thread.currentThread().getName());
            return a.getName();
        }).forEach(System.out::print);
        /**
         * main
         * aamain
         * ccmain
         * ffmain
         * bbmain
         * eemain
         * ddmain
         * aa
         */
    }
```



> 并行流

```java
 /**
     * 获取流
     */
    @Test
    public void test2(){
        //直接获取
        Stream<Emp> parallelStream = Emp.get().parallelStream();
        // 转
        Stream<Emp> parallel = Emp.get().stream().parallel();
        parallel.filter(a->{
            System.out.println(Thread.currentThread());
            return a.getId()>1;
        }).forEach(System.out::println);
        /**
         * Thread[main,5,main]
         * Emp(id=2, name=ee)
         * Thread[main,5,main]
         * Emp(id=2, name=bb)
         * Thread[main,5,main]
         * Emp(id=2, name=aa)
         * Thread[main,5,main]
         * Emp(id=2, name=dd)
         * Thread[main,5,main]
         * Emp(id=4, name=cc)
         * Thread[main,5,main]
         * Emp(id=4, name=ff)
         * Thread[main,5,main]
         */
    }
```

### 效率

```java

    long start ;

    @Before
    public void before(){
        start  = System.currentTimeMillis();
       // System.out.println(System.currentTimeMillis());
    }
    @After
    public void after(){
        System.out.println(System.currentTimeMillis()-start);
    }

    @Test
    public void test(){
     //   LongStream.rangeClosed(0,500000000).reduce(0,Long::sum);//449
        LongStream.rangeClosed(0,500000000).parallel().reduce(0,Long::sum);//406  332
    }
```

### 线程安全问题





> 1.加锁 2.线程安全集合 3. stream  api 

```java
 /**
     * 线程安全问题
     * 使用 stream api   toArray   collect
     */
    @Test
    public void test4(){
        List<Integer> collect = IntStream.rangeClosed(0, 1000).parallel().boxed().collect(Collectors.toList());
        
    }
```



### 并行流的fork join



```java

import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.RecursiveTask;

/**
 * @Author: xhb
 * @email xiaobo97@163.com
 * gitee: https://gitee.com/xiaobo97
 * @Date: 2021/6/7 1:12
 * @description: 并行流 的 forkjoin
 */
public class ParallerStreamForkJoin01 {

    public static void main(String[] args) {
        Long start  = System.currentTimeMillis();
        System.out.println(System.currentTimeMillis());
        ForkJoinPool joinPool = new ForkJoinPool();
        SumClass sumClass = new SumClass(1,10000);
        Integer integer = sumClass.invoke();
        System.out.println(System.currentTimeMillis()-start);
        System.out.println(integer);

    }

}



class SumClass extends RecursiveTask<Integer>{
    private static final int shoudown = 3000;

    private final int start ;
    private final int end;

    public SumClass(int start,int end){ this.start = start; this.end = end;}


    @Override
    protected Integer compute() {
        // 操作
        // 求和
        if((end-start)<shoudown){
                int sum = 0;
                for(int i = start;i<=end;i++){
                    sum +=i;
                }
                return sum;
        }else {
            // 拆分
            int mid = (start+end)/2;
            SumClass aClass = new SumClass(start, mid);
            aClass.fork();
            SumClass bClass = new SumClass( mid,end);
            bClass.fork();
            return aClass.join()+bClass.join();

        }
    }
}
```

