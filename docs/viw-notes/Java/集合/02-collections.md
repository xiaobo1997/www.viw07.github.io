[TOC]
<!-- TOC -->

- [collections](#collections)
  - [Java Collections class](#java-collections-class)

<!-- /TOC -->
# collections

> Collections 是一个包装类。 它包含有各种有关集合操作的静态多态方法。此类不能实例化，就像一个工具类，服务于Java的Collection框架。

## Java Collections class

> 继承树和声明
>
> - Object
>   - Collections

```java
public class Collections extends Object
```



> method

| 序号 | 修饰符                                              | Methods                                                      | 描述                                                         |
| :--- | :-------------------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| 1)   | static <T> boolean                                  | [addAll()](https://www.javatpoint.com/java-collections-addall-method) | 将所有指定的元素添加到指定的集合中                           |
| 2)   | static <T> Queue<T>                                 | [asLifoQueue()](https://www.javatpoint.com/java-collections-aslifoqueue-method) | 返回一个双端队列作为后进先出（LIFO）队列。                   |
| 3)   | static <T> int                                      | [binarySearch()](https://www.javatpoint.com/java-collections-binarysearch-method) | 在列表中搜索指定的对象，并返回它们在已排序列表中的位置。     |
| 4)   | static <E> Collection<E>                            | [checkedCollection()](https://www.javatpoint.com/java-collections-checkedcollection-method) | 用于返回指定集合的动态类型安全集合。。 如果插入的元素类型错误，将立即导致ClassCastException。 |
| 5)   | static <E> List<E>                                  | [checkedList()](https://www.javatpoint.com/java-collections-checkedlist-method) | It is used to returns a dynamically typesafe view of the specified list. |
| 6)   | static <K,V> Map<K,V>                               | [checkedMap()](https://www.javatpoint.com/java-collections-checkedmap-method) | It is used to returns a dynamically typesafe view of the specified map. |
| 7)   | static <K,V> NavigableMap<K,V>                      | [checkedNavigableMap()](https://www.javatpoint.com/java-collections-checkednavigablemap-method) | It is used to returns a dynamically typesafe view of the specified navigable map. |
| 8)   | static <E> NavigableSet<E>                          | [checkedNavigableSet()](https://www.javatpoint.com/java-collections-checkednavigableset-method) | It is used to returns a dynamically typesafe view of the specified navigable set. |
| 9)   | static <E> Queue<E>                                 | [checkedQueue()](https://www.javatpoint.com/java-collections-checkedqueue-method) | It is used to returns a dynamically typesafe view of the specified queue. |
| 10)  | static <E> Set<E>                                   | [checkedSet()](https://www.javatpoint.com/java-collections-checkedset-method) | It is used to returns a dynamically typesafe view of the specified set. |
| 11)  | static <K,V> SortedMap<K,V>                         | [checkedSortedMap()](https://www.javatpoint.com/java-collections-checkedsortedmap-method) | It is used to returns a dynamically typesafe view of the specified sorted map. |
| 12)  | static <E> SortedSet<E>                             | [checkedSortedSet()](https://www.javatpoint.com/java-collections-checkedsortedset-method) | It is used to returns a dynamically typesafe view of the specified sorted set. |
| 13)  | static <T> void                                     | [copy()](https://www.javatpoint.com/java-collections-copy-method) | 用于将所有元素从一个列表复制到另一列表。                     |
| 14)  | static boolean                                      | [disjoint()](https://www.javatpoint.com/java-collections-disjoint-method) | It returns true if the two specified collections have no elements in common. |
| 15)  | static <T> Enumeration<T>                           | [emptyEnumeration()](https://www.javatpoint.com/java-collections-emptyenumeration-method) | It is used to get an enumeration that has no elements.       |
| 16)  | static <T> Iterator<T>                              | [emptyIterator()](https://www.javatpoint.com/java-collections-emptyiterator-method) | It is used to get an Iterator that has no elements.          |
| 17)  | static <T> List<T>                                  | [emptyList()](https://www.javatpoint.com/java-collections-emptylist-method) | It is used to get a List that has no elements.               |
| 18)  | static <T> ListIterator<T>                          | [emptyListIterator()](https://www.javatpoint.com/java-collections-emptylistiterator-method) | It is used to get a List Iterator that has no elements.      |
| 19)  | static <K,V> Map<K,V>                               | [emptyMap()](https://www.javatpoint.com/java-collections-emptymap-method) | It returns an empty map which is immutable.                  |
| 20)  | static <K,V> NavigableMap<K,V>                      | [emptyNavigableMap()](https://www.javatpoint.com/java-collections-emptynavigablemap-method) | It returns an empty navigable map which is immutable.        |
| 21)  | static <E> NavigableSet<E>                          | [emptyNavigableSet()](https://www.javatpoint.com/java-collections-emptynavigableset-method) | It is used to get an empty navigable set which is immutable in nature. |
| 22)  | static <T> Set<T>                                   | [emptySet()](https://www.javatpoint.com/java-collections-emptyset-method) | It is used to get the set that has no elements.              |
| 23)  | static <K,V> SortedMap<K,V>                         | [emptySortedMap()](https://www.javatpoint.com/java-collections-emptysortedmap-method) | It returns an empty sorted map which is immutable.           |
| 24)  | static <E> SortedSet<E>                             | [emptySortedSet()](https://www.javatpoint.com/java-collections-emptysortedset-method) | It is used to get the sorted set that has no elements.       |
| 25)  | static <T> Enumeration<T>                           | [enumeration()](https://www.javatpoint.com/java-collections-enumeration-method) | It is used to get the enumeration over the specified collection. |
| 26)  | static <T> void                                     | [fill()](https://www.javatpoint.com/java-collections-fill-method) | 用于用指定的元素替换指定列表中的所有元素。                   |
| 27)  | static int                                          | [frequency()](https://www.javatpoint.com/java-collections-frequency-method) | It is used to get the number of elements in the specified collection equal to the specified object. |
| 28)  | static int                                          | [indexOfSubList()](https://www.javatpoint.com/java-collections-indexofsublist-method) | It is used to get the starting position of the first occurrence of the specified target list within the specified source list. It returns -1 if there is no such occurrence in the specified list. |
| 29)  | static int                                          | [lastIndexOfSubList()](https://www.javatpoint.com/java-collections-lastindexofsublist-method) | It is used to get the starting position of the last occurrence of the specified target list within the specified source list. It returns -1 if there is no such occurrence in the specified list. |
| 30)  | static <T> ArrayList<T>                             | [list()](https://www.javatpoint.com/java-collections-list-method) | 用于获取一个数组列表，其中包含指定枚举返回的元素的顺序，这些元素按枚举返回的顺序排列。 |
| 31)  | static <T extends Object & Comparable<? super T>> T | [max()](https://www.javatpoint.com/java-collections-max-method) | 根据其元素的自然顺序，它用于获取给定集合的最大值。           |
| 32)  | static <T extends Object & Comparable<? super T>> T | [min()](https://www.javatpoint.com/java-collections-min-method) | 根据其元素的自然顺序，它用于获取给定集合的最小值             |
| 33)  | static <T> List<T>                                  | [nCopies()](https://www.javatpoint.com/java-collections-ncopies-method) | It is used to get an immutable list consisting of **n** copies of the specified object. |
| 34)  | static <E> Set<E>                                   | [newSetFromMap()](https://www.javatpoint.com/java-collections-newsetfrommap-method) | It is used to return a set backed by the specified map.      |
| 35)  | static <T> boolean                                  | [replaceAll()](https://www.javatpoint.com/java-collections-replaceall-method) | It is used to replace all occurrences of one specified value in a list with the other specified value. |
| 36)  | static void                                         | [reverse()](https://www.javatpoint.com/java-collections-reverse-method) | 反转指定列表中元素的顺序。                                   |
| 37)  | static <T> Comparator<T>                            | [reverseOrder()](https://www.javatpoint.com/java-collections-reverseorder-method) | 用于获取比较器，该比较器对实现Comparable接口的对象集合施加自然顺序的逆向。 |
| 38)  | static void                                         | [rotate()](https://www.javatpoint.com/java-collections-rotate-method) | It is used to rotate the elements in the specified list by a given distance. |
| 39)  | static void                                         | [shuffle()](https://www.javatpoint.com/java-collections-shuffle-method) | 使用默认随机性对指定列表元素进行随机重新排序。               |
| 40)  | static <T> Set<T>                                   | [singleton()](https://www.javatpoint.com/java-collections-singleton-method) | 获取仅包含指定对象的不可变集。                               |
| 41)  | static <T> List<T>                                  | [singletonList()](https://www.javatpoint.com/java-collections-singletonlist-method) | 用于获取仅包含指定对象的不可变列表。                         |
| 42)  | static <K,V> Map<K,V>                               | [singletonMap()](https://www.javatpoint.com/java-collections-singletonmap-method) | 用于获取不可变的映射，仅将指定的键映射到指定的值。           |
| 43)  | static <T extends Comparable<? super T>>void        | [sort()](https://www.javatpoint.com/java-collections-sort-method) | 按升序对指定集合列表中的元素进行排序。                       |
| 44)  | static void                                         | [swap()](https://www.javatpoint.com/java-collections-swap-method) | 交换指定列表中指定位置的元素。                               |
| 45)  | static <T> Collection<T>                            | [synchronizedCollection()](https://www.javatpoint.com/java-collections-synchronizedcollection-method) | 用于获取由指定集合支持的同步（线程安全）集合。               |
| 46)  | static <T> List<T>                                  | [synchronizedList()](https://www.javatpoint.com/java-collections-synchronizedlist-method) | 用于获取由指定列表支持的同步（线程安全）集合                 |
| 47)  | static <K,V> Map<K,V>                               | [synchronizedMap()](https://www.javatpoint.com/java-collections-synchronizedmap-method) | 用于获取由指定的可导航图支持的同步（线程安全）map。          |
| 48)  | static <K,V> NavigableMap<K,V>                      | [synchronizedNavigableMap()](https://www.javatpoint.com/java-collections-synchronizednavigablemap-method) | It is used to get a synchronized (thread-safe) navigable map backed by the specified navigable map. |
| 49)  | static <T> NavigableSet<T>                          | [synchronizedNavigableSet()](https://www.javatpoint.com/java-collections-synchronizednavigableset-method) | It is used to get a synchronized (thread-safe) navigable set backed by the specified navigable set. |
| 50)  | static <T> Set<T>                                   | [synchronizedSet()](https://www.javatpoint.com/java-collections-synchronizedset-method) | It is used to get a synchronized (thread-safe) set backed by the specified set. |
| 51)  | static <K,V> SortedMap<K,V>                         | [synchronizedSortedMap()](https://www.javatpoint.com/java-collections-synchronizedsortedmap-method) | It is used to get a synchronized (thread-safe) sorted map backed by the specified sorted map. |
| 52)  | static <T> SortedSet<T>                             | [synchronizedSortedSet()](https://www.javatpoint.com/java-collections-synchronizedsortedset-method) | It is used to get a synchronized (thread-safe) sorted set backed by the specified sorted set. |
| 53)  | static <T> Collection<T>                            | [unmodifiableCollection()](https://www.javatpoint.com/java-collections-unmodifiablecollection-method) | 用于获取指定集合的不可修改collection                         |
| 54)  | static <T> List<T>                                  | [unmodifiableList()](https://www.javatpoint.com/java-collections-unmodifiablelist-method) | It is used to get an unmodifiable view of the specified list. |
| 55)  | static <K,V> Map<K,V>                               | [unmodifiableMap()](https://www.javatpoint.com/java-collections-unmodifiablemap-method) | It is used to get an unmodifiable view of the specified map. |
| 56)  | static <K,V> NavigableMap<K,V>                      | [unmodifiableNavigableMap()](https://www.javatpoint.com/java-collections-unmodifiablenavigablemap-method) | It is used to get an unmodifiable view of the specified navigable map. |
| 57)  | static <T> NavigableSet<T>                          | [unmodifiableNavigableSet()](https://www.javatpoint.com/java-collections-unmodifiablenavigableset-method) | It is used to get an unmodifiable view of the specified navigable set. |
| 58)  | static <T> Set<T>                                   | [unmodifiableSet()](https://www.javatpoint.com/java-collections-unmodifiableset-method) | It is used to get an unmodifiable view of the specified set. |
| 59)  | static <K,V> SortedMap<K,V>                         | [unmodifiableSortedMap()](https://www.javatpoint.com/java-collections-unmodifiablesortedmap-method) | It is used to get an unmodifiable view of the specified sorted map. |
| 60   | static <T> SortedSet<T>                             | [unmodifiableSortedSet()](https://www.javatpoint.com/java-collections-unmodifiablesortedset-method) | It is used to get an unmodifiable view of the specified sorted set. |



> example--01

```java
package java8.Map;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

/**
 * @Author: xiaobo
 * @Date: 2020/11/14 0:12
 */
public class Collections01 {
    public static void main(String[] args) {
        ArrayList<String> arrayList = new ArrayList<>();
        arrayList.add("c");
        arrayList.add("a");
        arrayList.add("b");
        String[] strArr = {"d","c"};
        System.out.println(arrayList);
        Collections.addAll(arrayList,strArr);
        System.out.println(arrayList);
        String max = Collections.max(arrayList);
        String min = Collections.min(arrayList);
        System.out.println("max:"+max+"min:"+min);
        Collections.sort(arrayList);
        arrayList.forEach(a-> System.out.println(a));
        List<String> list = Collections.synchronizedList(arrayList);
    }
}
//
[c, a, b]
[c, a, b, d, c]
max:dmin:a
a
b
c
c
d
```

