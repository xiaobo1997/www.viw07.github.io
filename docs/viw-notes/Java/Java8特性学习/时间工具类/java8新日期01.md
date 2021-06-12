[TOC]



# jdk8 新日期api





# 大纲



![image-20210607013049693](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210607013049693.png)







# 时间日期



## LocalDate



>

```java
 /**
     *  实例化
     */
    @Test
    public void test1(){
        // 静态方法 现在时间
        LocalDate now = LocalDate.now();
        System.out.println(now);//2021-06-07
        // 自定义日期
        LocalDate of = LocalDate.of(2020, 5, 20);
        System.out.println(of);//2020-05-20

        int year = now.getYear();
        int month = now.getMonth().getValue();
        int dayOfWeek = now.getDayOfWeek().getValue();

        System.out.println("年："+year+"月："+month+"星期的第"+dayOfWeek+"天");//年：2021月：6星期的第1天
    }
```



## LocalTime



>实例化

```java
  /**
     * 实例化
     */
    @Test
    public void test1(){
        LocalTime now = LocalTime.now();
        System.out.println("当前时间："+now);//当前时间：16:57:31.895

        LocalTime of = LocalTime.of(12, 2, 20);
        System.out.println(of);//12:02:20
    }
```



## LocalDateTime



### 实例化

> 实例化



```java

    /**
     * 实例化
     */
    @Test
    public void test1(){
        LocalDateTime now = LocalDateTime.now();
        System.out.println(now);//2021-06-07T17:00:01.099

        LocalDateTime of = LocalDateTime.of(2020, 5, 20, 5, 20, 00);
        System.out.println(of);//2020-05-20T05:20

    }
```

### 修改

> 修改

```java

    /**
     * 修改时间
     */
    @Test
    public void test2(){
        LocalDateTime now = LocalDateTime.now();
        System.out.println(now);//2021-06-07T17:06:03.532

        // 设置时间
        LocalDateTime time = now.withYear(2022);
        System.out.println(time);//2022-06-07T17:06:03.532

        //修改时间
        LocalDateTime time1 = now.plusMonths(10);
        System.out.println(now);// 2021-06-07T17:07:56.966
        System.out.println(time1);//2022-04-07T17:07:09.110
        LocalDateTime time2 = now.minusYears(1);
        System.out.println(time2);//2020-06-07T17:09:10.916
    }
```

### 比较

> 比较

```java

    /**
     * 比较时间
     */
    @Test
    public void test3(){
        LocalDateTime now = LocalDateTime.now();
        System.out.println(now);//2021-06-07T17:00:01.099

        LocalDateTime of = LocalDateTime.of(2020, 5, 20, 5, 20, 00);
        System.out.println(of);//2020-05-20T05:20

        boolean before = now.isBefore(of);
        System.out.println(before);// false
        boolean after = now.isAfter(of);
        System.out.println(after);//true
        boolean equal = now.isEqual(of);
        System.out.println(equal);//false

    }
```



# 格式化和解析

## DateTimeFormatter





### 格式化和解析



> 格式化和解析

```java

  /**
     * 格式化 和解析
     */
    @Test
    public void test1(){
        LocalDateTime now = LocalDateTime.now();

        DateTimeFormatter btf = DateTimeFormatter.ISO_DATE;
        String format = now.format(btf);
        System.out.println(format);//2021-06-07

        DateTimeFormatter pattern = DateTimeFormatter.ofPattern("yyyyMMddHHmmss");
        String format1 = now.format(pattern);
        System.out.println(format1);//20210607171755

        LocalDateTime parse = LocalDateTime.parse("20210520120001", pattern);
        System.out.println(parse);//2021-05-20T12:00:01
    }


```





# 时间戳

## Instant



> 时间戳

```java

    /**
     * 时间戳
     */
    @Test
    public void test1(){
        //实例化 ，有纳秒  统计用
        Instant now = Instant.now();
        System.out.println(now);//2021-06-07T10:35:41.772Z
        
        now.plusSeconds(20);//加20秒
        now.plusMillis(10); //加 10毫秒
        
        now.minusNanos(10)//减 10纳秒
    }
```





# 计算时间差



## Duration

```java
  /**
     *
     * Duration 计算时间
     */
    @Test
    public void test2(){
        LocalTime now = LocalTime.now();
        LocalTime of = LocalTime.of(23, 30, 20);
        Duration between = Duration.between(now, of);
        System.out.println(between.toDays());//0
        System.out.println(between.toHours());//4

    }
```



## Period

```java

    /**
     * 计算日期
     *Period 计算日期
     */
    @Test
    public void test1(){
        //LocalDateTime now = LocalDateTime.now();
        LocalDate now = LocalDate.now();
        LocalDate of = LocalDate.of(2020, 5, 20);
        Period between = Period.between(now, of);
        System.out.println(between.getDays());//-18
        System.out.println(between.getMonths());// 0
        System.out.println(between.getYears());//

    }

```





# 时间校正器



> 时间调整



```java

    /**
     * 时间校正  时间调整
     */
    @Test
    public void test3(){
        LocalDateTime now = LocalDateTime.now();
        TemporalAdjuster aa = e->{
            LocalDateTime e1 = (LocalDateTime) e;
            return e1.plusDays(1).withDayOfMonth(1);// 增加一条，为月的第一天
        };

        LocalDateTime with = now.with(aa);
        System.out.println(with);//2021-06-01T23:07:20.254
        
        //TemporalAdjusters 自带的工具类
        TemporalAdjuster day = TemporalAdjusters.firstDayOfMonth();
        LocalDateTime with1 = now.with(day);
        System.out.println(with1);//2021-06-01T23:10:34.089
    }

```



# ZonedDateTime(时区)



> 时区

```java

    /**
     * Set the time zone of the date and time
     */
    @Test
    public void test1(){
        // Get all time zones
        //ZoneId.getAvailableZoneIds().forEach(System.out::println);

        // 不带时区
        LocalDateTime now = LocalDateTime.now();// 中国的时区时间 比标准早8个小时
        System.out.println(now);//2021-06-08T23:16:46.710

        //带时区
        ZonedDateTime now1 = ZonedDateTime.now(Clock.systemUTC());// 标准时区时间
        System.out.println(now1);///2021-06-08T15:16:46.713Z
        
        //使用指定时区创建时间
        
//        ZonedDateTime.now(ZoneId.of("xxxxx"));
    }

```







