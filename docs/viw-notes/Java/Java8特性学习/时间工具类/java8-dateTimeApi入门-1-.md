[TOC]

# java 8 Date/Time API



## 类介绍



> Java从Java 8开始引入了新的Date and TimeAPI。java.time包包含Java 8 Date和Time类。



>新 date处理

- [java.time.LocalDate class](https://www.javatpoint.com/java-localdate)
- [java.time.LocalTime class](https://www.javatpoint.com/java-localtime)
- [java.time.LocalDateTime class](https://www.javatpoint.com/java-localdatetime)
- [java.time.MonthDay class](https://www.javatpoint.com/java-monthday)
- [java.time.OffsetTime class](https://www.javatpoint.com/java-offsettime)
- [java.time.OffsetDateTime class](https://www.javatpoint.com/java-offsetdatetime)
- [java.time.Clock class](https://www.javatpoint.com/java-clock)
- [java.time.ZonedDateTime class](https://www.javatpoint.com/java-zoneddatetime)
- [java.time.ZoneId class](https://www.javatpoint.com/java-zoneid)
- [java.time.ZoneOffset class](https://www.javatpoint.com/java-zoneoffset)
- [java.time.Year class](https://www.javatpoint.com/java-year)
- [java.time.YearMonth class](https://www.javatpoint.com/java-yearmonth)
- [java.time.Period class](https://www.javatpoint.com/java-period)
- [java.time.Duration class](https://www.javatpoint.com/java-duration)
- [java.time.Instant class](https://www.javatpoint.com/java-instant)
- [java.time.DayOfWeek enum](https://www.javatpoint.com/java-dayofweek-enum)
- [java.time.Month enum](https://www.javatpoint.com/java-month-enum)

> 之前的date处理类

- [java.util.Date class](https://www.javatpoint.com/java-util-date)
- [java.sql.Date class](https://www.javatpoint.com/java-sql-date)
- [java.util.Calendar class](https://www.javatpoint.com/java-util-calendar)
- java.util.GregorianCalendar class
- [java.util.TimeZone class]
- java.sql.Time class
- java.sql.Timestamp class

> 格式化时间

- [java.text.DateFormat class](https://www.javatpoint.com/java-date-format)
- [java.text.SimpleDateFormat class](https://www.javatpoint.com/java-simpledateformat)

> 当前日期时间

current date and time



## on java 8 



### LocalDate 

> Java LocalDate类是一个不可变的类，它以默认格式yyyy-MM-dd表示Date。 它继承了Object类并实现了ChronoLocalDate接口



> 类声明

```java
public final class LocalDate extends Object   
implements Temporal, TemporalAdjuster, ChronoLocalDate, Serializable  
```



> method

| LocalDateTime atTime(int hour, int minute)   | It is used to combine this date with a time to create a LocalDateTime. |
| -------------------------------------------- | ------------------------------------------------------------ |
| int compareTo(ChronoLocalDate other)         | It is used to compares this date to another date.            |
| boolean equals(Object obj)                   | It is used to check if this date is equal to another date.   |
| String format(DateTimeFormatter formatter)   | It is used to format this date using the specified formatter. |
| int get(TemporalField field)                 | It is used to get the value of the specified field from this date as an int. |
| boolean isLeapYear()                         | It is used to check if the year is a leap year, according to the ISO proleptic calendar system rules. |
| LocalDate minusDays(long daysToSubtract)     | It is used to return a copy of this LocalDate with the specified number of days subtracted. |
| LocalDate minusMonths(long monthsToSubtract) | It is used to return a copy of this LocalDate with the specified number of months subtracted. |
| static LocalDate now()                       | It is used to obtain the current date from the system clock in the default time-zone. |
| LocalDate plusDays(long daysToAdd)           | It is used to return a copy of this LocalDate with the specified number of days added. |
| LocalDate plusMonths(long monthsToAdd)       | It is used to return a copy of this LocalDate with the specified number of months added. |



> example--01

```java
package java8.dateapi;

import java.time.LocalDate;

/**
 * @Author: xiaobo
 * @Date: 2020/11/16 12:24
 */
public class LocalDate01 {
    public static void main(String[] args) {
        LocalDate localDate = LocalDate.now();
        // 当前时间月减去指定参数
        LocalDate minusMonths = localDate.minusMonths(1);
        //当前时间日减去指定参数
        LocalDate localDate1 = localDate.minusDays(1);
        System.out.println("年:"+localDate.getYear());
        System.out.println("month:"+minusMonths);
        System.out.println("days:"+localDate1);
    }
}
//
年:2020
month:2020-10-16
days:2020-11-15
```



## java 8 之前



## 时间格式化

