[TOC]

# Comparable interface

## 使用和用例

> Java Comparable接口用于对用户自定义的类的对象进行排序。 该接口在java.lang包中找到，并且仅包含一个名为compareTo（Object）的方法。 它仅提供单个排序序列，即，您只能基于单个数据成员对元素进行排序。 



**需要注意的是：**

public int compareTo（Object obj）：用

- 于将当前对象与指定对象进行比较。
-  如果当前对象大于指定对象，则返回正整数；
- 如果当前对象小于指定对象，则返回负整数；
- 如果当前对象等于指定对象，则返回零。



> 对以下元素进行排序：

- 字符串对象

- 包装类对象

- 用户定义的类对象

**需要注意的是:**

public void sort（List list）：用于对List的元素进行排序。 列表元素必须是Comparable可比较类型。

字符串类和包装器类默认实现Comparable接口。 

如果将字符串或包装类的对象存储在列表，集合或map中，则默认情况下它是可比较的



> 对于 collectoins 而言

集合类提供用于对集合元素进行排序的静态方法。

我们无法对List的元素进行排序。

 Collections类提供用于对List类型元素的元素进行排序的方法。



> example--01

```java
package java8.comparable;



/**
 * @Author: xiaobo
 * @Date: 2020/11/14 0:28
 */
public class Book implements   Comparable<Book>{
    private Integer price;

    public Integer getPrice() {
        return price;
    }

    public Book(Integer price) {
        this.price = price;
    }

    @Override
    public int compareTo(Book o) {
//       return  this.price-o.price;升序 // 3-1
        /**
         * 1 比较  3  1   o:1  this:3  2
         * 2 比较 2  3    1    o:3   this:2
         *       交换位置 1    2      3
         * 3 比较 2  1    o:1  this:2  3
         */
        if (this.price>o.price) return 1; // 说明  目前 this:小 o:大 返回1 升序
        else if (this.price<o.price) return -1;
        else return 0;
    }

}

// =============

package java8.comparable;

import java.util.ArrayList;
import java.util.Collections;

/**
 * @Author: xiaobo
 * @Date: 2020/11/14 0:28
 */
public class Comparable01 {
    public static void main(String[] args) {
        Book book1 = new Book(1);
        Book book2 = new Book(3);
        Book[] books = {book1,book2};
        ArrayList<Book> arrayList = new ArrayList<>();
        Collections.addAll(arrayList,books);
        Collections.sort(arrayList);
        arrayList.forEach(a-> System.out.println(a.getPrice()));
    }
}

```



# Comparator interface

## 使用

> Java Comparator接口用于对用户定义类的对象进行排序。此接口位于java.util包中，包含2个方法compare（Object obj1，Object obj2）和equals（Object element）。它提供了多个排序序列， 也就是说，您可以根据任何数据成员对元素进行排序



> method

| public int compare(Object obj1, Object obj2) | 第一个对象与第二个对象进行比较。 |
| -------------------------------------------- | -------------------------------- |
| public boolean equals(Object obj)            | 将当前对象与指定对象进行比较     |
| public boolean equals(Object obj)            | 将当前对象与指定对象进行比较。   |



> 对 list排序

public void sort（List list，Comparator c）：用于按给定的Comparator对List的元素进行排序。

> example---01

```java
// ===========

package java8.comparator;

/**
 * @Author: xiaobo
 * @Date: 2020/11/14 14:59
 */
public class Book01 {
    private String name;
    private Integer price;

    public Book01(String name, Integer price) {
        this.name = name;
        this.price = price;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getPrice() {
        return price;
    }

    public void setPrice(Integer price) {
        this.price = price;
    }
}

// ================

package java8.comparator;

import java.util.Comparator;

/**
 * @Author: xiaobo
 * @Date: 2020/11/14 15:00
 */
public class BookNameComparatoor implements Comparator {
    @Override
    public int compare(Object o1, Object o2) {
       Book01 book01 =  (Book01)o1;
        Book01 book02 =  (Book01)o2;
        // 内部对string排序
        return  book01.getName().compareTo(book02.getName());
    }
}

// ==================

package java8.comparator;

import java.util.Comparator;

/**
 * @Author: xiaobo
 * @Date: 2020/11/14 15:00
 */
public class PriceNameComparatoor implements Comparator {
    @Override
    public int compare(Object o1, Object o2) {
        Book01 book1 = (Book01) o1;
        Book01 book2 = (Book01) o2;
        return book1.getPrice()-book2.getPrice();
    }
}

// ===================================


package java8.comparator;

import java.util.ArrayList;
import java.util.Collections;

/**
 * @Author: xiaobo
 * @Date: 2020/11/14 14:59
 */
public class Comparator01 {
    public static void main(String[] args) {
        Book01[] bookarr = {new Book01("a", 12),new Book01("ca", 9),new Book01("cb", 11)};
        ArrayList<Book01> arrayList = new ArrayList<>();
        Collections.addAll(arrayList,bookarr);
        System.out.println("BookNameComparatoor:");
        // 升序
        Collections.sort(arrayList,new BookNameComparatoor());
        arrayList.forEach(a-> System.out.println(" " +"name:"+a.getName()+" price"+a.getPrice()));
        System.out.println("PriceNameComparatoor:");
        // 升序
        Collections.sort(arrayList,new PriceNameComparatoor());
        arrayList.forEach(a-> System.out.println(" "+"name:"+a.getName()+"  price"+a.getPrice()));
    }
}


// ==============console

BookNameComparatoor:
 name:a price12
 name:ca price9
 name:cb price11
PriceNameComparatoor:
 name:ca  price9
 name:cb  price11
 name:a  price12

```



## on java 8 comparator





java 8比较器接口是仅包含一种抽象方法的功能接口。 我们可以将Comparator接口用作lambda表达式或方法引用的分配目标。

> method

| int compare(T o1, T o2)                                      | 将第一个对象与第二个对象进行比较。                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| static <T,U extends Comparable<? super U>> Comparator<T> comparing(Function<? super T,? extends U> keyExtractor) | 接受一个函数，该函数从类型T中提取一个Comparable排序键，然后返回一个与该排序键进行比较的Comparator。 |
| static <T,U> Comparator<T> comparing(Function<? super T,? extends U> keyExtractor, Comparator<? super U> keyComparator) | 接受一个函数，该函数从类型T中提取一个排序键，然后返回一个比较器，该比较器使用指定的比较器按该排序键进行比较。 |
| static <T> Comparator<T> comparingDouble(ToDoubleFunction<? super T> keyExtractor) | 接受一个函数，该函数从类型T中提取一个双精度排序键，然后返回一个与该排序键进行比较的Comparator。 |
| static <T> Comparator<T> comparingInt(ToIntFunction<? super T> keyExtractor) | It accepts a function that extracts an int sort key from a type T, and returns a Comparator that compares by that sort key. |
| static <T> Comparator<T> comparingLong(ToLongFunction<? super T> keyExtractor) | It accepts a function that extracts a long sort key from a type T, and returns a Comparator that compares by that sort key. |
| boolean equals(Object obj)                                   | 将当前对象与指定对象进行比较。                               |
| static <T extends Comparable<? super T>> Comparator<T> naturalOrder() | It returns a comparator that compares Comparable objects in natural order. |
| static <T> Comparator<T> nullsFirst(Comparator<? super T> comparator) | 返回一个比较器，该比较器将null小于非null元素。null在前面     |
| static <T> Comparator<T> nullsLast(Comparator<? super T> comparator) | 返回一个比较器，该比较器将null大于非null元素。null在后面     |
| default Comparator<T> reversed()                             | It returns comparator that contains reverse ordering of the provided comparator. |
| static <T extends Comparable<? super T>> Comparator<T> reverseOrder() | It returns comparator that contains reverse of natural ordering. |
| default Comparator<T> thenComparing(Comparator<? super T> other) | It returns a lexicographic-order comparator with another comparator. |
| default <U extends Comparable<? super U>> Comparator<T> thenComparing(Function<? super T,? extends U> keyExtractor) | It returns a lexicographic-order comparator with a function that extracts a Comparable sort key. |
| default <U> Comparator<T> thenComparing(Function<? super T,? extends U> keyExtractor, Comparator<? super U> keyComparator) | It returns a lexicographic-order comparator with a function that extracts a key to be compared with the given Comparator. |
| default Comparator<T> thenComparingDouble(ToDoubleFunction<? super T> keyExtractor) | It returns a lexicographic-order comparator with a function that extracts a double sort key. |
| default Comparator<T> thenComparingInt(ToIntFunction<? super T> keyExtractor) | It returns a lexicographic-order comparator with a function that extracts a int sort key. |
| default Comparator<T> thenComparingLong(ToLongFunction<? super T> keyExtractor) | It returns a lexicographic-order comparator with a function that extracts a long sort key. |



> example --01
>
> using lambda

```java
package java8.comparator;

import java.util.ArrayList;
import java.util.Collections;
import java.util.Comparator;

/**
 * @Author: xiaobo
 * @Date: 2020/11/14 16:16
 */
public class OnJava8ComparatorLambda {
    public static void main(String[] args) {
        Book01[] bookarr = {new Book01("a", 12), new Book01("ca", 9), new Book01("cb", 11)};
        ArrayList<Book01> arrayList = new ArrayList<>();
        Collections.addAll(arrayList,bookarr);
        // sorting basis  name
        Comparator<Book01> com1 = Comparator.comparing(Book01::getName);
        Collections.sort(arrayList,com1);
        arrayList.forEach(a-> System.out.println(" " +"name:"+a.getName()+" price: "+a.getPrice()));
        // sortign basis price
        Comparator<Book01> com2 = Comparator.comparing(Book01::getPrice);
        Collections.sort(arrayList,com2);
        arrayList.forEach(a-> System.out.println(" " +"name:"+a.getName()+" price: "+a.getPrice()));

    }

}

// 
 name:a price: 12
 name:ca price: 9
 name:cb price: 11
 name:ca price: 9
 name:cb price: 11
 name:a price: 12
```



> example--02
>
> nullsFirst  nullLat 对 包含的null的进行 排序

```java
package java8.comparator;

import java.util.ArrayList;
import java.util.Collections;
import java.util.Comparator;

/**
 * @Author: xiaobo
 * @Date: 2020/11/14 16:16
 */
public class OnJava8ComparatorLambda {
    public static void main(String[] args) {
        Book01[] bookarr = {new Book01("a", 12), new Book01("ca", 9), new Book01(null, 11),new Book01("v", null),new Book01("cc", 5)};
        ArrayList<Book01> arrayList = new ArrayList<>();
        Collections.addAll(arrayList,bookarr);

        // sorting basis  name  对 包含null的排序，null在前  然后升序
        Comparator<Book01> com3 = Comparator.comparing(Book01::getName,Comparator.nullsFirst(String::compareTo));
        Collections.sort(arrayList,com3);
        arrayList.forEach(a-> System.out.println(" " +"name:"+a.getName()+" price: "+a.getPrice()));
        System.out.println("\n");
        // sortign basis price  对 包含null的排序，null在后  然后升序
        Comparator<Book01> com4 = Comparator.comparing(Book01::getPrice,Comparator.nullsLast(Integer::compareTo));
        Collections.sort(arrayList,com4);
        arrayList.forEach(a-> System.out.println(" " +"name:"+a.getName()+" price: "+a.getPrice()));
    }

}
//==========

 name:null price: 11
 name:a price: 12
 name:ca price: 9
 name:cc price: 5
 name:v price: null


 name:cc price: 5
 name:ca price: 9
 name:null price: 11
 name:a price: 12
 name:v price: null

```

