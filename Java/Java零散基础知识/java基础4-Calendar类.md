

[TOC]



# 1.calendar类



## 介绍



> Calendar主要是用来处理日期



> calendar中的常量

| 字段值                                                | 含义                                                         |
| ----------------------------------------------------- | ------------------------------------------------------------ |
| **public final static int ERA = 0;**                  |                                                              |
| **public final static int YEAR = 1;**                 | 默认情况下指示为年份                                         |
| **public final static int MONTH = 2;**                | 月份（从 0 开始，也就是全年 12 个月由 0 ~ 11 进行表示）      |
| **public final static int WEEK_OF_YEAR = 3;**         | 当前时间是所在当前年的第几个星期(日历式的第几周)             |
| **public final static int WEEK_OF_MONTH = 4;**        | 当前时间是所在当前月的第几个星期(日历式的第几周)             |
| **public final static int DATE = 5;**                 | 某月的第几天                                                 |
| **public final static int DAY_OF_MONTH = 5;**         | 某月的第几天                                                 |
| **public final static int DAY_OF_YEAR = 6;**          | 一年的第几天                                                 |
| **public final static int DAY_OF_WEEK = 7;**          | 一周中的第几天，用数字（1-7）表示（星期日~星期六）           |
| **public final static int DAY_OF_WEEK_IN_MONTH = 8;** | 当前时间是所在当前月的第几个星期<br>以月份天数为标准，一个月的1号为第一周，8号为第二周 |
| **public final static int AM_PM = 9;**                | 判断当前时间是AM,还是PM,若是AM返回结果为0,若是PM返回结果为1  |
| **public final static int HOUR = 10;**                | 时（12小时制）                                               |
| **public final static int HOUR_OF_DAY = 11;**         | 时（24小时制）                                               |
| **public final static int MINUTE = 12;**              | 分                                                           |
| **public final static int SECOND = 13;**              | 秒                                                           |
| **public final static int MILLISECOND = 14;**         | 毫秒                                                         |
| **public final static int ZONE_OFFSET = 15;**         |                                                              |
| **public final static int DST_OFFSET = 16;**          |                                                              |
| **public final static int FIELD_COUNT = 17;**         |                                                              |
|                                                       |                                                              |
| **public final static int SUNDAY = 1;**               | 星期天                                                       |
| **public final static int MONDAY = 2;**               | 星期一                                                       |
| **public final static int TUESDAY = 3;**              | 星期二                                                       |
| **public final static int WEDNESDAY = 4;**            | 星期三                                                       |
| **public final static int THURSDAY = 5;**             | 星期四                                                       |
| **public final static int FRIDAY = 6;**               | 星期五                                                       |
| **public final static int SATURDAY = 7;**             | 星期六                                                       |
|                                                       |                                                              |
| **public final static int JANUARY = 0;**              |                                                              |
| **public final static int FEBRUARY = 1;**             |                                                              |
| **public final static int MARCH = 2;**                |                                                              |
| **public final static int APRIL = 3;**                |                                                              |
| **public final static int MAY = 4;**                  |                                                              |
| **public final static int JUNE = 5;**                 |                                                              |
| **public final static int JULY = 6;**                 |                                                              |
| **public final static int AUGUST = 7;**               |                                                              |
| **public final static int SEPTEMBER = 8;**            |                                                              |
| **public final static int OCTOBER = 9;**              |                                                              |
| **public final static int NOVEMBER = 10;**            |                                                              |
| **public final static int DECEMBER = 11;**            |                                                              |
| **public final static int UNDECIMBER = 12;**          |                                                              |
|                                                       |                                                              |
| **public final static int AM = 0;**                   | 上午                                                         |
| **public final static int PM = 1;**                   | 下午                                                         |



## 使用



```java
 public static void main(String[] args) {
        Date date;
        Calendar calendar = Calendar.getInstance();
        // 获取当前年
        System.out.println(calendar.get(Calendar.YEAR));
        // 获取当前月
        System.out.println(calendar.get(Calendar.MONTH)+1);
        //获取日
        System.out.println(calendar.get(Calendar.DAY_OF_MONTH));
        // 获取分
        System.out.println(calendar.get(Calendar.MINUTE));
        //获取秒
        System.out.println(calendar.get(Calendar.SECOND));
        // 一年后的今天
        calendar.add(Calendar.YEAR,1);
        System.out.println(calendar.get(Calendar.YEAR));
        // 今天减1
        calendar.set(Calendar.DATE,calendar.get(Calendar.DATE)-1);
        System.out.println(calendar.get(Calendar.DAY_OF_MONTH));
        //按照格式获取日期
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyyMMdd");
        String format = simpleDateFormat.format(calendar.getTime());
        System.out.println(format);
    }

//console

2020
12
16
59
32
2021
15
20211215

Process finished with exit code 0

```





