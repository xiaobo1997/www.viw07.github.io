[TOC]



# stream02



### 收集



#### collect(Collector c)

![image-20210602003645443](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210602003645443.png)

>收集

```java
    /**
     * 
     */
    @Test
    public  void  test1(){
        List<Emp> emps = Emp.get();
//        List<Emp> collect = emps.stream().filter(a -> a.getId() > 1).collect(Collectors.toList());
        List<Object> collect = emps.stream().map(a -> {
            a.setName("aaaaaa");
            return a;
        }).collect(Collectors.toList());
        collect.parallelStream().forEach(System.out::println);
    }

// print out
Emp(id=2, name=aaaaaa)
Emp(id=2, name=aaaaaa)
Emp(id=2, name=aaaaaa)
Emp(id=4, name=aaaaaa)
Emp(id=4, name=aaaaaa)
Emp(id=1, name=aaaaaa)
```



#### 聚合运算



```java

    /**
     * 聚合运算，
     */
    @Test
    public void test2(){
        List<Emp> emps = Emp.get();
//        最大值
        Optional<Emp> collect = emps.stream().collect(Collectors.maxBy((a, b) -> a.getId() - b.getId()));
        System.out.println(collect.toString());// Optional[Emp(id=4, name=cc)]
//        最小值
        Optional<Emp> collect1 = emps.stream().collect(Collectors.maxBy((a, b) ->  b.getId()-a.getId()));
        // 求和
        Integer integer = emps.stream().collect(Collectors.summingInt(a -> a.getId()));
        System.out.println(integer);//15
        // 平均值
//        Double collect2 = emps.stream().collect(Collectors.averagingInt(a->a.getId()));
        Double collect2 = emps.stream().collect(Collectors.averagingInt(Emp::getId));
        System.out.println(collect2);//2.5
        //统计数量
        Long collect3 = emps.stream().collect(Collectors.counting());
        System.out.println(collect3);//6
    }
```



#### 分组计算



```java

    /**
     * 对流中数据进行分组
     */
    @Test
    public void test3() {
        List<Emp> emps = Emp.get();
        //按类型分
//        Map<Integer, List<Emp>> collect = emps.stream().collect(Collectors.groupingBy(s -> {
//            return s.getId();
//        }));
        Map<Integer, List<Emp>> collect = emps.stream().collect(Collectors.groupingBy(Emp::getId));

        collect.forEach((k, v) -> {
            System.out.println(k + ":" + v);
        });
        /**
         * 1:[Emp(id=1, name=aa)]
         * 2:[Emp(id=2, name=bb), Emp(id=2, name=ee), Emp(id=2, name=dd)]
         * 4:[Emp(id=4, name=cc), Emp(id=4, name=ff)]
         */
        // 按照条件分
        Map<String, List<Emp>> collect1 = emps.stream().collect(Collectors.groupingBy(a -> {
            if (a.getId() > 1) return "1组";
            else return "2组";
        }));
        collect1.forEach((k, v) -> {
            System.out.println(k + ":" + v);
        });
        /**
         * 2组:[Emp(id=1, name=aa)]
         * 1组:[Emp(id=4, name=cc), Emp(id=4, name=ff), Emp(id=2, name=bb), Emp(id=2, name=ee), Emp(id=2, name=dd)]
         */
    }

```



#### 多级分组



> 多条件分组

```java

    /**
     * 多级分组
     */
    @Test
    public void test4(){
        //先根据id分，再根据成绩分组
        List<Emp> emps = Emp.get();
//        Map<Integer, Map<String, List<Emp>>> collect = emps.stream().collect(Collectors.groupingBy(Emp::getId, Collectors.groupingBy(Emp::getName)));
        Map<Integer, Map<String, List<Emp>>> collect = emps.stream().collect(Collectors.groupingBy(Emp::getId, Collectors.groupingBy(s->{
            if (s.getName()=="aa") return "aa";
            else return "no aa";
        })));
        collect.forEach((k,v)->{
            System.out.println("先按照id为："+k+"分");
            v.forEach((a,b)-> System.out.println("再按照k为；"+a+"分。他的value为"+":"+b));
        });
        /**
         * 先按照id为：1分
         * 再按照k为；aa分。他的value为:[Emp(id=1, name=aa)]
         * 先按照id为：2分
         * 再按照k为；aa分。他的value为:[Emp(id=2, name=aa)]
         * 再按照k为；no aa分。他的value为:[Emp(id=2, name=bb), Emp(id=2, name=ee), Emp(id=2, name=dd)]
         * 先按照id为：4分
         * 再按照k为；no aa分。他的value为:[Emp(id=4, name=cc), Emp(id=4, name=ff)]
         */
    }

```



#### 对流分区



>

```java

    /**
     * 对流拼接
     */
    @Test
    public void test6(){
        List<Emp> emps = Emp.get();
        String collect = emps.stream().map(Emp::getName).collect(Collectors.joining("_"));
        System.out.println(collect);//aa_cc_ff_bb_ee_dd_aa
        String collect1 = emps.stream().map(Emp::getName).collect(Collectors.joining("_", "+", "-")); // 前缀 后缀
        System.out.println(collect1);// +aa_cc_ff_bb_ee_dd_aa-
        
    }
```



