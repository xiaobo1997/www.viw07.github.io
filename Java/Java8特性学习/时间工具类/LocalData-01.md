[TOC]



# LocalData





## 基础



## 使用



### 格式化

```java
LocalDate localDate = LocalDate.now();//For reference
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd LLLL yyyy");
String formattedString = localDate.format(formatter);

// 和上面一样
LocalDate.now().format(DateTimeFormatter.ofPattern("dd/MM/yyyy"));
```



### 加减

```java
 // 获取当前LocalDate
        LocalDate localDate1 = LocalDate.now();
        System.out.println("localDate1:"+localDate1);
        // localDate1加五天
        LocalDate localDate2 = localDate1.plusDays(5);
        System.out.println("localDate1加5天："+localDate2);

        // localDate2减一天
        LocalDate minusDays = localDate2.minusDays(1);
        System.out.println("localDate2减一天："+minusDays);

        // 比较localDate1与localDate2，结果返回localDate1-localDate2的值
        int i = localDate1.compareTo(localDate2);
        System.out.println("localDate1与localDate2比较大小："+ i);
        // 判断localDate1是否在localDate2之前，返回布尔值
        boolean before = localDate1.isBefore(localDate2);
        System.out.println("判断localDate1是否在localDate2之前："+before);
```

