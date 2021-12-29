
<!-- TOC -->

- [collection Framework](#collection-framework)
  - [继承树和概述](#继承树和概述)
    - [集合迭代器接口](#集合迭代器接口)
    - [Iterable 接口](#iterable-接口)
    - [collection接口](#collection接口)
  - [List Interface](#list-interface)
      - [ListIterator  Interface](#listiterator--interface)
      - [思考](#思考)
    - [ArrayList](#arraylist)
    - [LinkedList](#linkedlist)
    - [ArrayList vs LinkedList](#arraylist-vs-linkedlist)
  - [set Inteface](#set-inteface)
    - [HashSet](#hashset)
    - [TreeSet](#treeset)
    - [LikedHashSet](#likedhashset)
  - [Map interface](#map-interface)
      - [Entry interface](#entry-interface)
    - [HashMap](#hashmap)
    - [TreeMap](#treemap)
    - [LinkedHashMap](#linkedhashmap)
    - [HashTable](#hashtable)
    - [HashMap vs  HashTable](#hashmap-vs--hashtable)

<!-- /TOC -->
# collection Framework

> Java中的Collection是一个框架，提供了用于存储和操作对象组的体系结构。



> Java Collection表示单个对象单元。 Java Collection框架提供了许多接口（Set，List，Queue，Deque）和类（ArrayList，Vector，LinkedList，PriorityQueue，HashSet，LinkedHashSet，TreeSet）。



**collections** 

> Java Collections可以完成您对数据执行的所有操作，例如搜索，排序，插入，操作和删除。





> 整个集合框架就围绕一组标准接口而设计。你可以直接使用这些接口的标准实现，诸如： **LinkedList**, **HashSet**, 和 **TreeSet** 等,除此之外你也可以通过这些接口实现自己的集合。大多数日常使用的都是他们的实现类。



## 继承树和概述



1.Java集合框架继承树：

![image-20201111122518967](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201111122518967.png)



2.collection 的基础树

![image-20201111123307432](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201111123307432.png)



3.Map的基础树

![image-20201111123335421](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201111123335421.png)





### 集合迭代器接口

> 集合迭代器中提供了向前迭代的功能

```java
Iterator<T> iterator()
```



> 迭代器的method

| 1    | public boolean hasNext() | 如果迭代器具有下一个元素，则返回true，否则返回false。 |
| ---- | ------------------------ | ----------------------------------------------------- |
| 2    | public Object next()     | 它返回该元素，并将光标指针移动到下一个元素。          |
| 3    | public void remove()     | 它将删除迭代器返回的最后一个元素。 使用较少           |



### Iterable 接口

> Iterable接口是所有集合类的根接口。 Collection接口扩展了Iterable接口，因此Collection接口的所有子类也实现了Iterable接口，它仅包含一个抽象方法。 即

```
Iterator<T> iterator() //返回类型T的元素上的迭代器。
```



### collection接口

>Collection接口是由收集框架中的所有类实现的接口。 它声明每个集合将具有的方法。 换句话说，我们可以说Collection接口建立了collection框架所依赖的基础.Collection接口的一些方法是Boolean add（Object obj），Boolean addAll（Collection c），void clear（）等。 由Collection接口的所有子类实现。
>
>



> collection 接口的method
>
> 在collection接口中声明了很多的方法，

| 1    | public boolean add(E e)                               | 用于在此集合中插入元素                                       |
| ---- | ----------------------------------------------------- | ------------------------------------------------------------ |
| 2    | public boolean addAll(Collection<? extends E> c)      | 用于在调用集合中插入指定的集合元素。                         |
| 3    | public boolean remove(Object element)                 | It is used to delete an element from the collection.         |
| 4    | public boolean removeAll(Collection<?> c)             | It is used to delete all the elements of the specified collection from the invoking collection. |
| 5    | default boolean removeIf(Predicate<? super E> filter) | 用于删除集合中所有满足指定条件的元素。                       |
| 6    | public boolean retainAll(Collection<?> c)             | 删除调用集合中除指定集合之外的所有元素。                     |
| 7    | public int size()                                     | 返回集合中元素的总数。                                       |
| 8    | public void clear()                                   | 从集合中删除元素总数。                                       |
| 9    | public boolean contains(Object element)               | 用于搜索指定元素。                                           |
| 10   | public boolean containsAll(Collection<?> c)           | 搜索集合中的是否有该指定集合。                               |
| 11   | public Iterator iterator()                            | 返回一个迭代器。                                             |
| 12   | public Object[] toArray()                             | 集合转换为数组。                                             |
| 13   | public <T> T[] toArray(T[] a)                         | 将集合转换为数组。 在这里，返回数组的运行时类型是指定数组的运行时类型，也就是泛型 |
| 14   | public boolean isEmpty()                              | It checks if collection is empty.                            |
| 15   | default Stream<E> parallelStream()                    | 返回一个可能并行的Stream，并将其集合作为源。                 |
| 16   | default Stream<E> stream()                            | 返回一个以集合为源的顺序Stream。                             |
| 17   | default Spliterator<E> spliterator()                  | 集合中的指定元素上生成拆分器                                 |
| 18   | public boolean equals(Object element)                 | 匹配两个集合。                                               |
| 19   | public int hashCode()                                 | 返回集合的哈希码编号。                                       |



## List Interface

> List接口是Collection接口的子接口。 我们可以在其中存储对象的有序集合。



> Java中的List提供了维护有序集合的工具。 它包含用于插入，更新，删除和搜索元素的基于索引的方法。 它也可以有重复的元素。 我们还可以将null元素存储在列表中。List接口位于java.util包中，并继承Collection接口。 它是ListIterator接口的工厂。 通过ListIterator，我们可以向前和向后迭代列表。 List接口的实现类是ArrayList，LinkedList，Stack和Vector。 ArrayList和LinkedList在Java编程中被广泛使用。 从Java 5开始不推荐使用Vector类。



> 接口声明

```java
public interface List<E> extends Collection<e> 
```



> list method

| void add(int index, E element)                       | 用于将指定的元素插入列表中的指定位置。                       |      |
| ---------------------------------------------------- | ------------------------------------------------------------ | ---- |
| boolean add(E e)                                     | 它用于在列表末尾附加指定的元素。                             |      |
| boolean addAll(Collection<? extends E> c)            | 将指定集合中的所有元素附加到列表的末尾。                     |      |
| boolean addAll(int index, Collection<? extends E> c) | 从列表的指定位置开始，它用于附加指定集合中的所有元素。       |      |
| void clear()                                         | 从此列表中删除所有元素。                                     |      |
|                                                      |                                                              |      |
| boolean equals(Object o)                             | 将指定的对象与列表的元素进行比较。                           |      |
| int hashcode()                                       | 返回列表的哈希码值。                                         |      |
| E get(int index)                                     | 用于从列表的特定位置获取元素                                 |      |
| boolean isEmpty()                                    | It returns true if the list is empty, otherwise false.       |      |
| int lastIndexOf(Object o)                            | 用于返回指定元素最后一次出现在此列表中的索引；如果列表不包含此元素，则返回-1。 |      |
| Object[] toArray()                                   | 以正确的顺序返回包含此列表中所有元素的数组。                 |      |
| <T> T[] toArray(T[] a)                               | 以正确的顺序返回包含此列表中所有元素的数组。                 |      |
| boolean contains(Object o)                           | 如果列表包含指定的元素，则返回true                           |      |
| boolean containsAll(Collection<?> c)                 | 如果列表包含所有指定的元素，则返回true                       |      |
| int indexOf(Object o)                                | 它用于返回指定元素首次出现在此列表中的索引；如果List不包含此元素，则返回-1。 |      |
| E remove(int index)                                  | 用于删除列表中指定位置上存在的元素。                         |      |
| boolean remove(Object o)                             | 用于删除指定元素的第一个匹配项。                             |      |
| boolean removeAll(Collection<?> c)                   | 用于从列表中删除所有元素。                                   |      |
| void replaceAll(UnaryOperator<E> operator)           | 用指定的元素替换列表中的所有元素。                           |      |
| void retainAll(Collection<?> c)                      | 保留列表中指定集合中存在的所有元素。                         |      |
| E set(int index, E element)                          | 替换列表中位于指定位置的指定元素。                           |      |
| void sort(Comparator<? super E> c)                   | 根据指定的比较器对列表中的元素进行排序。                     |      |
| Spliterator<E> spliterator()                         | 用于在列表中的元素上创建分隔符。                             |      |
| List<E> subList(int fromIndex, int toIndex)          | 获取位于给定范围内的所有元素。                               |      |
| int size()                                           | It is used to return the number of elements present in the list. |      |



> how to create list
>
> ArrayList和LinkedList类提供List接口的实现。
>
> 可以创建任何类型的列表。 ArrayList <T>和LinkedList <T>类用于指定类型。 在此，T表示类型。 

```java
//Creating a List of type String using ArrayList  
List<String> list=new ArrayList<String>();  
  
//Creating a List of type Integer using ArrayList  
List<Integer> list=new ArrayList<Integer>();  
  
//Creating a List of type Book using ArrayList  
List<Book> list=new ArrayList<Book>();  
  
//Creating a List of type String using LinkedList  
List<String> list=new LinkedList<String>();  
```





> List接口中有多种方法可用于从列表中插入，删除和访问元素。

```java
List<Type-data> list1 = new ArrayList();
List <data-type> list2 = new LinkedList();  
List <data-type> list3 = new Vector();  
List <data-type> list4 = new Stack();  
```



> use list example--01

```java
package java8.list;

import java.util.ArrayList;

/**
 * @Author: xiaobo
 * @Date: 2020/11/12 0:09
 */
public class List01 {
    public static void main(String[] args) {
        // create a list
        ArrayList<String > arrayList = new ArrayList<>();
        // adding elements in the list
        arrayList.add("xiaobo");
        arrayList.add("viw");
        // Iterating the list eleement using for-ecah loop
        for (String strLi : arrayList){
            System.out.print(strLi);
        }
    }
}

```

> use list exampl--02
>
> array to list

```java
package java8.list;

        import java.util.ArrayList;

/**
 * @Author: xiaobo
 * @Date: 2020/11/12 0:09
 */
public class List01 {
    public static void main(String[] args) {
        ArrayList<String > arrayList = new ArrayList<>();
        // create string array
        String[] strArr = {"a","b","c"};
        System.out.println(strArr.toString());
        // converting array to list
        for (String strLi : strArr){
            arrayList.add(strLi);
        }
        System.out.println(arrayList);
    }
}

```

> using list example-03
>
> list to array

```java
package java8.list;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

/**
 * @Author: xiaobo
 * @Date: 2020/11/12 0:18
 */
public class List02 {
    public static void main(String[] args) {
       List<String> list = new ArrayList<>();
       list.add("xiaobo");
       list.add("viw");
        // converting list to array
        System.out.println(Arrays.toString(list.toArray(new String[list.size()])));
    }
}

```



> example--04
>
> collections sort
>
> set  and  get 

```java
package java8.list;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

/**
 * @Author: xiaobo
 * @Date: 2020/11/12 0:22
 */
public class List03 {
    public static void main(String[] args) {
       List<String> stringList = new ArrayList<>();
       List<Integer> integerArrayList = new ArrayList<>();
       stringList.add("aaa");
       stringList.add("Ab");
       stringList.add("xiaobo");
       integerArrayList.add(3);
       integerArrayList.add(1);
       integerArrayList.add(2);
        System.out.println(stringList);
        stringList.set(1,"AA");
        System.out.println(stringList+stringList.get(1));

        System.out.println("=====");
        Collections.sort(stringList);
        Collections.sort(integerArrayList);
        System.out.println(stringList+"\n"+integerArrayList);
    }
}

```



```java
// 数组 转list
//1
List<String> resultList = new ArrayList<>(array.length);
Collections.addAll(resultList,array);
// 2
List<String> resultList = new ArrayList<>(array.length);
for (String s : array) {
    resultList.add(s);
}
```



#### ListIterator  Interface



> ListIterator接口用于沿前后方向遍历元素。



>  类的声明

```
public interface ListIterator<E> extends Iterator<E>  
```

>   ListIterator  method

| void add(E e)         | 将指定的元素插入列表。                                       |
| --------------------- | ------------------------------------------------------------ |
| boolean hasNext()     | 如果列表迭代器在向前遍历列表时有更多元素，则此方法返回true。 |
| E next()              | 返回列表中的下一个元素并前进光标位置。                       |
| int nextIndex()       | 返回元素的索引，该元素的索引将由对next（）的后续调用返回     |
| boolean hasPrevious() | 此列表迭代器在反向遍历列表时有更多元素，则此方法返回true。   |
| E previous()          | 返回列表中的前一个元素，并将光标位置向后移动。               |
| E previousIndex()     | 返回元素的索引，该元素的索引将由后续对previous（）的调用返回。 |
| void remove()         | 从next（）或previous（）方法返回的列表中删除最后一个元素     |
| void set(E e)         | 用指定的元素替换next（）或previous（）方法返回的最后一个元素。 |



> example ---05
>
> ListIterator interface

```java
package java8.list;

import java.util.ArrayList;
import java.util.ListIterator;

/**
 * @Author: xiaobo
 * @Date: 2020/11/12 0:31
 */
public class List04 {
    public static void main(String[] args) {
        ArrayList<String> arrayList = new ArrayList<>();
        arrayList.add("a");
        arrayList.add("b");
        System.out.println(arrayList);
        arrayList.add(1,"c");
        System.out.println(arrayList);
        ListIterator<String> iterator = arrayList.listIterator();
        while (iterator.hasNext()){
            System.out.print(iterator.nextIndex()+"="+iterator.next()+" ");
        }
        System.out.println("\n");
        while (iterator.hasPrevious()) {
            System.out.print(iterator.previousIndex()+"="+iterator.previous()+" ");
        }
    }

}

```



#### 思考

[ArrayList in Java](https://www.javatpoint.com/java-arraylist)

[LinkedList in Java](https://www.javatpoint.com/java-linkedlist)

[Difference between ArrayList and LinkedList](https://www.javatpoint.com/difference-between-arraylist-and-linkedlist)

[Difference between Array and ArrayList](https://www.javatpoint.com/difference-between-array-and-arraylist)

[When to use ArrayList and LinkedList in Java](https://www.javatpoint.com/when-to-use-arraylist-and-linkedlist-in-java)

[Difference between ArrayList and Vector](https://www.javatpoint.com/difference-between-arraylist-and-vector)

[How to Compare Two ArrayList in Java](https://www.javatpoint.com/how-to-compare-two-arraylist-in-java)

[How to reverse ArrayList in Java](https://www.javatpoint.com/how-to-reverse-arraylist-in-java)

[When to use ArrayList and LinkedList in Java](https://www.javatpoint.com/when-to-use-arraylist-and-linkedlist-in-java)

[How to make ArrayList Read Only](https://www.javatpoint.com/how-to-make-java-arraylist-read-only)

[Difference between length of array and size() of ArrayList in Java](https://www.javatpoint.com/difference-between-length-of-array-and-size-of-arraylist-in-java)

[How to Synchronize ArrayList in Java](https://www.javatpoint.com/how-to-synchronize-arraylist-in-java)

[How to convert ArrayList to Array and Array to ArrayList in java](https://www.javatpoint.com/how-to-convert-arraylist-to-array-and-array-to-arraylist-in-java)

[Array vs ArrayList in Java](https://www.javatpoint.com/array-vs-arraylist-in-java)

[How to Sort Java ArrayList in Descending Order](https://www.javatpoint.com/how-to-sort-java-arraylist-in-descending-order)

[How to remove duplicates from ArrayList in Java](https://www.javatpoint.com/how-to-remove-duplicates-from-arraylist-in-java)



### ArrayList

> Java ArrayList类使用动态数组来存储元素。 它就像一个数组，但是没有大小限制。 我们可以随时添加或删除元素。 因此，它比传统阵列灵活得多。 在java.util包中可以找到它。 Java中的ArrayList也可以具有重复的元素。 它实现了List接口，因此我们可以在此处使用List接口的所有方法。 ArrayList在内部维护插入顺序，它继承了AbstractList类并实现List接口。



**对于arraylist需要注意的是**

- 1.Java ArrayList类可以包含重复的元素。
- 2.Java ArrayList类维护插入顺序。
- 3.Java ArrayList类不同步。
- 4.ArrayList允许随机访问，因为数组基于索引工作。
- 5.在ArrayList中，操作要比Java中的LinkedList慢一点，因为如果从数组列表中删除任何元素，则需要进行很多转换。



> 继承树关系和类声明
>
> - Iterable
>   - -->extends-->Collection
>     - -->extends-->List
>       - -->Implements-->AbstractList
>         - -->extends-->ArrayList

```java
public class ArrayList<E> extends AbstractList<E> implements List<E>, RandomAccess, Cloneable, Serializable  
```



> 构造函数

| ArrayList()                          | 构建一个空数组列表。                  |
| ------------------------------------ | ------------------------------------- |
| ArrayList(Collection<? extends E> c) | 构建使用集合c的元素初始化的数组列表。 |
| ArrayList(int capacity)              | 生成具有指定的初始容量的数组列表。    |





> method

| void [add](https://www.javatpoint.com/java-arraylist-add-method)(int index, E element) | 将指定的元素插入列表中的指定位置。                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| boolean [add](https://www.javatpoint.com/java-arraylist-add-method)(E e) | 在列表末尾附加指定的元素。                                   |
| boolean [addAll](https://www.javatpoint.com/java-arraylist-addall-method)(Collection<? extends E> c) | 将指定集合中的所有元素附加到此列表的末尾，以指定集合的迭代器返回它们的顺序。 |
| boolean [addAll](https://www.javatpoint.com/java-arraylist-addall-method)(int index, Collection<? extends E> c) | 从列表的指定位置开始，它用于附加指定集合中的所有元素。       |
| void [clear](https://www.javatpoint.com/java-arraylist-clear-method)() | 从此列表中删除所有元素。                                     |
| void ensureCapacity(int requiredCapacity)                    | 增强ArrayList实例的容量。                                    |
| E get(int index)                                             | 从列表的特定位置获取元素。                                   |
| boolean isEmpty()                                            | It returns true if the list is empty, otherwise false.       |
| [Iterator()](https://www.javatpoint.com/java-arraylist-iterator-method) |                                                              |
| [listIterator()](https://www.javatpoint.com/java-arraylist-listiterator-method) |                                                              |
| int lastIndexOf(Object o)                                    | 返回指定元素最后一次出现在此列表中的索引；如果列表不包含此元素，则返回-1。 |
| Object[] toArray()                                           | 以正确的顺序返回包含此列表中所有元素的数组。                 |
| <T> T[] toArray(T[] a)                                       | 以正确的顺序返回包含此列表中所有元素的数组                   |
| Object clone()                                               | 返回ArrayList的浅拷贝表副本。                                |
| boolean contains(Object o)                                   | 列表包含指定的元素，则返回true                               |
| int indexOf(Object o)                                        | 返回指定元素首次出现在此列表中的索引；如果List不包含此元素，则返回-1。 |
| E remove(int index)                                          | It is used to remove the element present at the specified position in the list. |
| boolean [remove](https://www.javatpoint.com/java-arraylist-remove-method)(Object o) | It is used to remove the first occurrence of the specified element. |
| boolean [removeAll](https://www.javatpoint.com/java-arraylist-removeall-method)(Collection<?> c) | 从列表中删除所有元素。                                       |
| boolean removeIf(Predicate<? super E> filter)                | 从满足给定条件的列表中删除所有元素。                         |
| protected void [removeRange](https://www.javatpoint.com/java-arraylist-removerange-method)(int fromIndex, int toIndex) | 删除位于给定范围内的所有元素。                               |
| void replaceAll(UnaryOperator<E> operator)                   | 用指定的元素替换列表中的所有元素。                           |
| void [retainAll](https://www.javatpoint.com/java-arraylist-retainall-method)(Collection<?> c) | 保留列表中指定集合中存在的所有元素。                         |
| E set(int index, E element)                                  | 替换列表中位于指定位置的指定元素。                           |
| void sort(Comparator<? super E> c)                           | 根据指定的比较器对列表中的元素进行排序。                     |
| Spliterator<E> spliterator()                                 | 列表中的元素上创建分隔符。                                   |
| List<E> subList(int fromIndex, int toIndex)                  | 获取位于给定范围内的所有元素。                               |
| int size()                                                   | It is used to return the number of elements present in the list. |
| void trimToSize()                                            | 将ArrayList实例的容量调整为列表的当前大小。                  |

> example--01
>
> simple useing arrayList  add get  remove set  toArray

```java
package java8.list;

import java.util.ArrayList;
import java.util.Arrays;

/**
 * @Author: xiaobo
 * @Date: 2020/11/12 12:24
 */
public class List05 {
    public static void main(String[] args) {
        ArrayList<String> arrayList = new ArrayList<>();
        arrayList.add("a");
        arrayList.add("c");
        System.out.println(arrayList);
        arrayList.add(1,"b");
        System.out.println("指定索引1 插入 元素后"+arrayList);
        System.out.println(arrayList.get(2));
        arrayList.set(2,"xiaobo");
        System.out.println(arrayList.get(2));
        arrayList.remove(2);
        System.out.println(arrayList);
        System.out.println(Arrays.toString(arrayList.toArray()));
    }
}
//
[a, c]
指定索引1 插入 元素后[a, b, c]
c
xiaobo
[a, b]
[a, b]
```

>example--02
>
>useing iterator 
>
>using for-each loop

```java
package java8.list;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.Iterator;
import java.util.ListIterator;

/**
 * @Author: xiaobo
 * @Date: 2020/11/12 12:24
 */
public class List05 {
    public static void main(String[] args) {
        ArrayList<String> arrayList = new ArrayList<>();
        arrayList.add("a");
        arrayList.add("c");
        arrayList.add("b");
        // iterating arraylist
        ListIterator<String> listIterator = arrayList.listIterator();
        while (listIterator.hasNext()){
            System.out.print(listIterator.next()+" ");
        }
        System.out.println("\n");
        // using for loop
        for(int i = 0;i<arrayList.size();i++){
            System.out.print(arrayList.get(i)+"  ");
        }
        System.out.println("\n");
        // using for-each loop
        for(String str : arrayList){
            System.out.print(str+" ");
        }
        // using java8 forEach
        System.out.println("\n");
        arrayList.forEach(a->{
            System.out.println(a);
        });
        System.out.println("\n");
        // using
        Iterator<String> iterator = arrayList.iterator();
        iterator.forEachRemaining(a->{
            System.out.print(a+" ");
        });
          Collections.sort(arrayList);
        System.out.print(arrayList);
    }
}
//
a c b 

a  c  b  

a c b 

a
c
b

a c b 
    
[a, b, c]
```

> example--03
>
> useing other ways traverse 

```
在 example-02
```

 

> example--04
>
> **collections :**
>
> java.util包提供了一个实用程序类Collections，它具有静态方法sort（）。 使用Collections.sort（）方法，我们可以轻松地对ArrayList进行排序。

```
在 example-02
```



> example--05
>
>  serialize an ArrayList object and then deserialize it.

```java
package java8.list;

import java8.io.File01;

import java.io.*;
import java.util.ArrayList;

/**
 * @Author: xiaobo
 * @Date: 2020/11/12 12:46
 */
public class List06 {
    public static void main(String[] args) {
        File file = new File("D://xiaobo//a.txt");
        ArrayList<String> arrayList = new ArrayList<>();
        arrayList.add("xiaobo");
        arrayList.add("hello");
        try {

			// serialization
            FileOutputStream fileOutputStream = new FileOutputStream(file);
            ObjectOutputStream OutputStream = new ObjectOutputStream(fileOutputStream);
            OutputStream.writeObject(arrayList);
            fileOutputStream.close();
            OutputStream.close();
			// deserialization
            FileInputStream fileInputStream = new FileInputStream(file);
            ObjectInputStream objectInputStream = new ObjectInputStream(fileInputStream);
            ArrayList read =(ArrayList) objectInputStream.readObject();
            System.out.println(read);
            fileInputStream.close();
            objectInputStream.close();

        }catch(IOException e){
            e.printStackTrace();
        }catch (Exception ex){
            ex.printStackTrace();
        }
    }
}

```



### LinkedList

> Java LinkedList类使用双链表来存储元素。 它提供了一个链表数据结构。 它继承了AbstractList类并实现List和Deque接口。



**需要注意的是：**

- Java LinkedList类可以包含重复的元素.
- Java LinkedList类保持插入顺序.
- Java LinkedList类是不同步的。
- 在Java LinkedList类中，由于不需要进行移位，因此操作速度很快.
- Java LinkedList类可以用作列表，堆栈或队列 。





> 继承树和声明

![image-20201112130128022](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201112130128022.png)



```java
public class LinkedList<E> extends AbstractSequentialList<E> implements List<E>, Deque<E>, Cloneable, Serializable 
```





> 构造函数

| LinkedList()                          | 构造一个空列表。                                             |
| ------------------------------------- | ------------------------------------------------------------ |
| LinkedList(Collection<? extends E> c) | 构造一个包含指定集合元素的列表，这些元素按顺序由集合的迭代器返回。 |

> method

| boolean add(E e)                                     | It is used to append the specified element to the end of a list. |
| ---------------------------------------------------- | ------------------------------------------------------------ |
| void add(int index, E element)                       | It is used to insert the specified element at the specified position index in a list. |
| boolean addAll(Collection<? extends E> c)            | It is used to append all of the elements in the specified collection to the end of this list, in the order that they are returned by the specified collection's iterator. |
| boolean addAll(Collection<? extends E> c)            | It is used to append all of the elements in the specified collection to the end of this list, in the order that they are returned by the specified collection's iterator. |
| boolean addAll(int index, Collection<? extends E> c) | It is used to append all the elements in the specified collection, starting at the specified position of the list. |
| void addFirst(E e)                                   | 将给定元素插入列表的开头。                                   |
| void addLast(E e)                                    | 将给定元素附加到列表的末尾。                                 |
| void clear()                                         | 从列表中删除所有元素。                                       |
| Object clone()                                       | 返回ArrayList的浅拷贝表副本。                                |
| boolean contains(Object o)                           | 如果列表包含指定的元素，则返回true。                         |
| Iterator<E> descendingIterator()                     | 用于按逆序顺序在双端队列返回元素上的迭代器。                 |
| E element()                                          | 检索列表的第一个元素。                                       |
| E get(int index)                                     | 返回列表中指定位置的元素。                                   |
| E getFirst()                                         | 用于返回列表中的第一个元素。                                 |
| E getLast()                                          | 返回列表中的最后一个元素。                                   |
| int indexOf(Object o)                                | 从列表中的指定位置开始，它用于按正确顺序返回元素的列表迭代器。 |
| int lastIndexOf(Object o)                            | It is used to return the index in a list of the last occurrence of the specified element, or -1 if the list does not contain any element. |
| ListIterator<E> listIterator(int index)              | It is used to return a list-iterator of the elements in proper sequence, starting at the specified position in the list. |
| boolean offer(E e)                                   | 将指定的元素添加为列表的最后一个元素。                       |
| boolean offerFirst(E e)                              | 指定的元素插入列表的开头。                                   |
| boolean offerLast(E e)                               | 指定的元素插入列表的末尾。                                   |
| E peek()                                             | 取出列表的第一个元素                                         |
| E peekFirst()                                        | 取出列表的第一个元素；如果列表为空，则返回null。             |
| E peekLast()                                         | 取出列表的最后一个元素；如果列表为空，则返回null。           |
| E poll()                                             | 检索并删除列表的第一个元素。                                 |
| E pollFirst()                                        | 检索并删除列表的第一个元素，如果列表为空，则返回null。       |
| E pollLast()                                         | 检索并删除列表的最后一个元素，如果列表为空，则返回null。     |
| E pop()                                              | 从列表表示的堆栈中弹出一个元素。                             |
| void push(E e)                                       | 将元素压入列表表示的堆栈。                                   |
| E remove()                                           | 删除列表的第一个元素。                                       |
| E remove(int index)                                  | 删除列表中指定位置的元素。                                   |
| boolean remove(Object o)                             | 删除列表中第一次出现的指定元素。                             |
| E removeFirst()                                      | 从列表中删除并返回第一个元素。                               |
| boolean removeFirstOccurrence(Object o)              | 用于删除列表中指定元素的首次出现（当从头到尾遍历列表时）。   |
| E removeLast()                                       | 删除并返回最后一个元素。                                     |
| boolean removeLastOccurrence(Object o)               | 删除列表中最后一次出现的指定元素（从头到尾遍历列表时）。     |
| E set(int index, E element)                          | 列表中指定位置的元素替换为指定元素。                         |
| Object[] toArray()                                   | 以正确的顺序（从第一个元素到最后一个元素）返回包含列表中所有元素的数组。 |
| <T> T[] toArray(T[] a)                               | 它返回一个数组，该数组包含按正确顺序排列的所有元素（从第一个元素到最后一个元素）； 返回数组的运行时类型是指定数组的运行时类型。 |
| int size()                                           | It is used to return the number of elements in a list.       |

> example--01
>
> add remove set

```java
package java8.list;

import java.util.Iterator;
import java.util.LinkedList;
import java.util.ListIterator;

/**
 * @Author: xiaobo
 * @Date: 2020/11/12 12:59
 */
public class LinkedList01 {
    public static void main(String[] args) {
        LinkedList<String> linkedList = new LinkedList<>();
        linkedList.add("a");
        linkedList.add("c");
        linkedList.add(1,"b");
        System.out.println(linkedList);
        linkedList.set(2,"xiaobo");
        linkedList.addLast("d");
        System.out.println(linkedList);
        linkedList.removeFirst();
        System.out.println(linkedList);
        //using traverse
        ListIterator<String> listIterator = linkedList.listIterator();
        while (listIterator.hasNext()) {
            System.out.print(listIterator.next()+"  ");
        }
        System.out.println("\n");
        //reverse list
        Iterator<String> descendingIterator = linkedList.descendingIterator();
        while (descendingIterator.hasNext()){
            System.out.print(descendingIterator.next()+"  ");
        }

    }
}

```



### ArrayList vs LinkedList

> 简单说一下 两者的大概区别

| ArrayList                                                    | LinkedList                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| 1) ArrayList在内部使用动态数组存储元素。                     | LinkedList在内部使用双向链表来存储元素。                     |
| 2) 使用ArrayList进行操作很慢，因为它在内部使用数组。 如果从阵列中删除了任何元素，则所有位都在存储器中移位。 | 使用LinkedList进行操作要比ArrayList快，因为它使用了双向链接列表，因此内存中不需要移位。 |
| 3) ArrayList类只能用作list，因为它仅实现List。               | LinkedList类可以实现列表和队列，因为它实现了List和Deque接口。 |
| 4) ArrayList更适合存储和访问数据。                           | LinkedList更适合处理数据。                                   |



## set Inteface



### HashSet

> Java HashSet类用于创建使用哈希表进行存储的集合。 它继承了AbstractSet类并实现Set接口。



**需要注意的是：**

- HashSet通过一种称为哈希的机制存储元素.
- HashSet仅包含唯一元素.
- HashSet允许为空值.
- HashSet类是不同步的.
- HashSet不维护插入顺序。 
- 元素是根据其哈希码插入的.HashSet是搜索操作的最佳方法.
- HashSet的初始默认容量为16，负载系数为0.75。

==列表可以包含重复元素，而Set仅包含唯一元素。==



> 继承树和声明

![image-20201112232700298](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201112232700298.png)

```java
public class HashSet<E> extends AbstractSet<E> implements Set<E>, Cloneable, Serializable  
```

> 构造函数

| 1)   | HashSet()                               | 构造默认的HashSet。                                          |
| ---- | --------------------------------------- | ------------------------------------------------------------ |
| 2)   | HashSet(int capacity)                   | 将hash set的容量初始化为给定的整数容量。 随着将元素添加到HashSet中，容量会自动增长。 |
| 3)   | HashSet(int capacity, float loadFactor) | 它用于将hashset的容量初始化为给定的整数容量和指定的负载系数。 |
| 4)   | HashSet(Collection<? extends E> c)      | 用于通过使用集合c的元素来初始化hashset。                     |

> method

| 1)   | boolean        | [add(E e)](https://www.javatpoint.com/java-hashset-add-method) | 指定的元素尚不存在，则用于将其添加到该集合中。               |
| ---- | -------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2)   | void           | [clear()](https://www.javatpoint.com/java-hashset-clear-method) | 删除集合中的所有元素。                                       |
| 3)   | object         | [clone()](https://www.javatpoint.com/java-hashset-clone-method) | 返回此HashSet实例的浅拷贝副本：元素本身未克隆。              |
| 4)   | boolean        | [contains(Object o)](https://www.javatpoint.com/java-hashset-contains-method) | 此集合包含指定的元素，则用于返回true。                       |
| 5)   | boolean        | [isEmpty()](https://www.javatpoint.com/java-hashset-isempty-method) | 此集合不包含任何元素，则用于返回true。                       |
| 6)   | Iterator<E>    | [iterator()](https://www.javatpoint.com/java-hashset-iterator-method) | 返回此集合中元素的迭代器。                                   |
| 7)   | boolean        | [remove(Object o)](https://www.javatpoint.com/java-hashset-remove-method) | 指定的元素存在，则用于从该集合中删除该元素。                 |
| 8)   | int            | [size()](https://www.javatpoint.com/java-hashset-size-method) | 返回集合中的元素数。                                         |
| 9)   | Spliterator<E> | [spliterator()](https://www.javatpoint.com/java-hashset-spliterator-method) | It is used to create a late-binding and fail-fast Spliterator over the elements in the set |



> example --01
>
> add set remove  removeIf iterator

```java
package java8.set;

import java.util.HashSet;
import java.util.Iterator;

/**
 * @Author: xiaobo
 * @Date: 2020/11/12 23:42
 */
public class HashSet01 {
    public static void main(String[] args) {
        HashSet<String> hashSet = new HashSet();
        hashSet.add("a");
        hashSet.add("b");
        hashSet.add("c");
        boolean a = hashSet.contains("a");
        System.out.println(a);
        hashSet.remove("a");
        Iterator<String> iterator = hashSet.iterator();
//        while (iterator.hasNext()) {
//            System.out.print(" "+iterator.next());
//        }
        // 在这里会出现并发修改异常 如果hashSet.remove("a");
        //  using  forEachRemaining
        iterator.forEachRemaining(i->{
            System.out.print(i+" ");
        });
        System.out.println("\n");
        System.out.println(hashSet.removeIf(str->str.contains("b")));
        System.out.println(hashSet);
    }
}

```





### TreeSet

> Java TreeSet类实现了Set接口，该接口使用树进行存储。 它继承了AbstractSet类并实现了NavigableSet接口。 TreeSet类的对象以升序存储。

**需要注意的是：**

- Java TreeSet类仅包含像HashSet这样的唯一元素.
- Java TreeSet类的访问和检索非常快速
- Java TreeSet类不允许使用null元素.
- Java TreeSet类不同步.
- Java TreeSet类保持升序。



> 继承和声明
>
> Java TreeSet类实现了NavigableSet接口。 NavigableSet接口按层次结构顺序扩展了SortedSet，Set，Collection和Iterable接口。

![image-20201113000231327](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201113000231327.png)

```java
public class TreeSet<E> extends AbstractSet<E> implements NavigableSet<E>, Cloneable, Serializable  
```



> 构造函数

| TreeSet()                                 | 构造一个空树集，该树集将根据树集的自然顺序以升序排序。 |
| ----------------------------------------- | ------------------------------------------------------ |
| TreeSet(Collection<? extends E> c)        | 构建包含集合c的元素的新树集。                          |
| TreeSet(Comparator<? super E> comparator) | 构造一个空树集，该树集将根据给定的比较器进行排序。     |
| TreeSet(SortedSet<E> s)                   | 构建包含给定SortedSet元素的TreeSet。                   |

> method

| boolean add(E e)                                             | 指定的元素尚不存在，则用于将其添加到该集合中。               |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| boolean addAll(Collection<? extends E> c)                    | 将指定集合中的所有元素添加到该集合中。                       |
| E ceiling(E e)                                               | 集合中返回指定元素的等于或最接近的最大元素，如果没有，则返回null。 |
| Comparator<? super E> comparator()                           | 返回按顺序排列元素的比较器。                                 |
| Iterator descendingIterator()                                | 按降序迭代元素。                                             |
| NavigableSet descendingSet()                                 | 相反的顺序返回元素。                                         |
| E floor(E e)                                                 | 从集合中返回指定元素的等于或最接近的最小元素，如果没有，则返回null。 |
| SortedSet headSet(E toElement)                               | 它返回小于指定元素的元素组。                                 |
| NavigableSet headSet(E toElement, boolean inclusive)         | 返回小于或等于（如果包含，则为true）指定元素的元素组。       |
| E higher(E e)                                                | 从集合中返回指定元素中最接近的最大元素，如果没有，则返回null。 |
| Iterator iterator()                                          | 按升序迭代元素。                                             |
| E lower(E e)                                                 | 集合中返回指定元素中最接近的最小元素，如果没有，则返回null。 |
| E pollFirst()                                                | 删除最低（第一个）元素。                                     |
| E pollLast()                                                 | 删除最后元素。                                               |
| Spliterator spliterator()                                    | It is used to create a late-binding and fail-fast spliterator over the elements. |
| NavigableSet subSet(E fromElement, boolean fromInclusive, E toElement, boolean toInclusive) | 回位于给定范围之间的一组元素。                               |
| SortedSet subSet(E fromElement, E toElement))                | 返回位于给定范围（包括fromElement和不包括toElement）之间的一组元素。 |
| SortedSet tailSet(E fromElement)                             | 返回一组大于或等于指定元素的元素。                           |
| NavigableSet tailSet(E fromElement, boolean inclusive)       | 返回一组大于或等于（如果包含在内，则为真）指定元素的元素。   |
| boolean contains(Object o)                                   | 此集合包含指定的元素，则返回true。                           |
| boolean isEmpty()                                            | 此集合不包含任何元素，则返回true。                           |
| boolean remove(Object o)                                     | 指定的元素存在，则用于从该集合中删除该元素。                 |
| void clear()                                                 | 删除此集合中的所有元素。                                     |
| Object clone()                                               | 返回此TreeSet实例的浅表副本。                                |
| E first()                                                    | 返回此排序集中当前的第一个（最低）元素。                     |
| E last()                                                     | 返回此排序集中当前的最后一个（最高）元素。                   |
| int size()                                                   | 返回此集合中的元素数。                                       |

> example--01

```java
package java8.set;

import java.util.HashSet;
import java.util.Iterator;
import java.util.TreeSet;

/**
 * @Author: xiaobo
 * @Date: 2020/11/13 0:02
 */
public class TreeSet01 {
    public static void main(String[] args) {
        TreeSet<String> treeSet = new TreeSet<>();
        treeSet.add("a");
        treeSet.add("c");
        treeSet.add("b");
        treeSet.add("f");
        treeSet.add("e");
        // 判断是否存在a
        System.out.println(treeSet.contains("a"));
        // 迭代器遍历
        Iterator<String> iterator = treeSet.iterator();
        while (iterator.hasNext()) {
            System.out.print(iterator.next()+" ");
        }
        // 反向迭代器
        Iterator<String> descendingIterator = treeSet.descendingIterator();
        while (descendingIterator.hasNext()){
            System.out.print(" "+descendingIterator.next());
        }
        // 取出第一个和最后一个
//        System.out.println("\npollFirst:"+treeSet.pollFirst());
//        System.out.println("pollLast:"+treeSet.pollLast());
        //  NavigableSet method
        System.out.println("\n"+treeSet);
        System.out.println(treeSet.descendingSet());
        System.out.println(treeSet.headSet("d",true));
        System.out.println(treeSet.subSet("a",true,"c",false));
        System.out.println(treeSet.tailSet("c",true));
        
    }
}
// 
true
a b c e f  f e c b a
[a, b, c, e, f]
[a, b, c]
[a, b]
[c, e, f]
```



### LikedHashSet

> Java LinkedHashSet类是set接口的Hashtable和Linked list实现。 它继承了HashSet类并实现Set接口。



**需要注意的是：**

- Java LinkedHashSet类仅包含像HashSet这样的唯一元素.
- Java LinkedHashSet类提供所有可选的设置操作并允许使用null元素.
- Java LinkedHashSet类是不同步的.
- Java LinkedHashSet类保持插入顺序。



> 继承树和声明

![image-20201113004518864](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201113004518864.png)



```java
public class LinkedHashSet<E> extends HashSet<E> implements Set<E>, Cloneable, Serializable  
```



> 构造函数

| HashSet()                                    | 构造默认的HashSet。                                      |
| -------------------------------------------- | -------------------------------------------------------- |
| HashSet(Collection c)                        | 通过使用集合c的元素来初始化哈希集。                      |
| LinkedHashSet(int capacity)                  | 将链接的哈希集的容量初始化为给定的整数容量。             |
| LinkedHashSet(int capacity, float fillRatio) | 根据其参数初始化哈希集的容量和填充率（也称为负载容量）。 |

> method



> example--01



## Map interface

> 映射包含基于键以及值，即键和值对（key-value）。 每个键和值对称为一个条目。 映射包含唯一键。
>
> 

==映射不允许重复的键，但是您可以有重复的值。 HashMap和LinkedHashMap允许空键和值，但是TreeMap不允许任何空键或值。==

==不能遍历Map，因此您需要使用keySet（）或entrySet（）方法将其转换为Set。==

> 继承树
>
> 在Java中有两个用于实现Map的接口：Map和SortedMap，以及三个类：HashMap，LinkedHashMap和TreeMap。 

![image-20201113010744719](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201113010744719.png)



> 实现类

| [HashMap](https://www.javatpoint.com/java-hashmap)           | HashMap是Map的实现，但不维护任何顺序。                       |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [LinkedHashMap](https://www.javatpoint.com/java-linkedhashmap) | LinkedHashMap是Map的实现。 它继承了HashMap类。 它保持插入顺序。 |
| [TreeMap](https://www.javatpoint.com/java-treemap)           | TreeMap是Map和SortedMap的实现。 它保持升序。                 |



> method

| V put(Object key, Object value)                              | 用于在map中插入一个条目。                                    |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| void putAll(Map map)                                         | 在map中插入指定的map。                                       |
| V putIfAbsent(K key, V value)                                | 仅当尚未将指定值和指定键插入映射时，才将其插入。             |
| V remove(Object key)                                         | 删除指定键的条目。                                           |
| boolean remove(Object key, Object value)                     | 从map中删除具有关联的指定键的指定值                          |
| Set keySet()                                                 | 返回包含所有键的Set                                          |
| Set<Map.Entry<K,V>> entrySet()                               | 返回包含所有键和值的Set                                      |
| void clear()                                                 | 重置map。                                                    |
| V compute(K key, BiFunction<? super K,? super V,? extends V> remappingFunction) | 它用于计算指定键及其当前映射值的映射（如果没有当前映射，则为null）。 |
| V computeIfAbsent(K key, Function<? super K,? extends V> mappingFunction) | 指定的键尚未与值关联（或已映射为null），则使用给定的映射函数来计算其值，除非为null，否则将其输入此映射。 |
| V computeIfPresent(K key, BiFunction<? super K,? super V,? extends V> remappingFunction) | 指定键的值存在且非空，则用于给定键及其当前映射值的情况下计算新映射。 |
| boolean containsValue(Object value)                          | 映射中存在等于该值的某个值，则此方法返回true，否则返回false。 |
| boolean containsKey(Object key)                              | 如果映射中存在等于该键的某个键，则此方法返回true，否则返回false。 |
| boolean equals(Object o)                                     | 指定的Object与Map进行比较。                                  |
| void forEach(BiConsumer<? super K,? super V> action)         | 对映射中的每个条目执行给定的for each操作，直到所有条目都已处理或该操作引发异常为止。 |
| V get(Object key)                                            | 返回包含与键关联的值的对象。                                 |
| V getOrDefault(Object key, V defaultValue)                   | 返回指定键映射到的值，如果映射不包含键的映射，则返回defaultValue。 |
| int hashCode()                                               | 返回Map的哈希码值                                            |
| boolean isEmpty()                                            | 映射为空，则此方法返回true；否则，返回false。 如果包含至少一个key，则返回false。 |
| V merge(K key, V value, BiFunction<? super V,? super V,? extends V> remappingFunction) | 如果指定的键尚未与值关联或与null关联，请将其与给定的非null值关联。 |
| V replace(K key, V value)                                    | 将指定的值替换为指定的键。                                   |
| boolean replace(K key, V oldValue, V newValue)               | 指定键的新值替换旧值。                                       |
| void replaceAll(BiFunction<? super K,? super V,? extends V> function) | 用在该条目上调用给定函数的结果替换每个条目的值，直到处理完所有条目或该函数引发异常为止。 |
| Collection values()                                          | 返回映射中包含的值的集合                                     |
| int size()                                                   | This method returns the number of entries in the map.        |



#### Entry interface

> Entry是Map的子接口。 因此，将通过Map.Entry名称对其进行访问。 它返回map，其元素属于此类。 它提供了获取键和值的方法。



> method 

| K getKey()                                                   | 用于获取key。                                                |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| V getValue()                                                 | 用于获取值。                                                 |
| int hashCode()                                               | 用于获取hashCode。                                           |
| V setValue(V value)                                          | 设置值                                                       |
| boolean equals(Object o)                                     | 将指定的对象与其他现有对象进行比较。                         |
| static <K extends Comparable<? super K>,V> Comparator<Map.Entry<K,V>> comparingByKey() | 返回一个比较器，该比较器以键上的原始顺序比较对象。           |
| static <K,V> Comparator<Map.Entry<K,V>> comparingByKey(Comparator<? super K> cmp) | 返回一个比较器，该比较器使用给定的Comparator通过键比较对象。 |
| static <K,V extends Comparable<? super V>> Comparator<Map.Entry<K,V>> comparingByValue() | 返回一个比较器，该比较器以自然顺序对值进行比较。             |
| static <K,V> Comparator<Map.Entry<K,V>> comparingByValue(Comparator<? super V> cmp) | 返回一个比较器，该比较器使用给定的Comparator通过值比较对象。 |

> example--01
>
> put  遍历

```java
package java8.Map;

import java.util.HashMap;
import java.util.Iterator;
import java.util.Map;
import java.util.Set;

/**
 * @Author: xiaobo
 * @Date: 2020/11/13 12:22
 */
public class Map01 {
    public static void main(String[] args) {
        // non-generic
        Map map = new HashMap();
        map.put(1,"xiaobo");
        map.put(3,"viw");
        map.put(2,"annie");
        // 遍历map
        Set entrySet = map.entrySet();
        Iterator iterator = entrySet.iterator();
        while (iterator.hasNext()) {
            //转换为Map.Entry，以便我们可以分别获取键和值
            Map.Entry entry = (Map.Entry) iterator.next();
            System.out.println(" "+"key:"+entry.getKey()+"value:"+entry.getValue());
        }
        // generic
        Map<Integer, String> hashMap = new HashMap<>();
        hashMap.put(1,"xiaobo");
        hashMap.put(3,"viw");
        hashMap.put(2,"annie");
        for(Map.Entry m : hashMap.entrySet()){
            System.out.println(" "+"key:"+m.getKey()+"value:"+m.getValue());
        }
        
    }
}
//
 key:1value:xiaobo
 key:2value:annie
 key:3value:viw
 key:1value:xiaobo
 key:2value:annie
 key:3value:viw
```

> example--02
>
> comparingBykey()  comparingByValue() 排序

```java
package java8.Map;

import java.util.Comparator;
import java.util.HashMap;
import java.util.Map;

/**
 * @Author: xiaobo
 * @Date: 2020/11/13 12:37
 */
public class Map02 {
    public static void main(String[] args) {
        Map<Integer, String> map = new HashMap<Integer, String>();
        map.put(100, "Amit");
        map.put(101, "Vijay");
        map.put(102, "Rahul");
        //返回此映射中包含的映射的Set视图
        map.entrySet()
                //返回以此集合为源的顺序Stream
                .stream()
                //根据提供的比较器排序  key降序
                .sorted(Map.Entry.comparingByKey(Comparator.reverseOrder()))
                //为此流的每个元素执行一个遍历动作
                .forEach(System.out::println);

        //Returns a Set view of the mappings contained in this map
        map.entrySet()
                //Returns a sequential Stream with this collection as its source
                .stream()
                //Sorted according to the provided Comparator  key升序
                .sorted(Map.Entry.comparingByKey())
                //Performs an action for each element of this stream
                .forEach(System.out::println);

        // value 升序
        map.entrySet().stream().sorted(Map.Entry.comparingByValue()).forEach(System.out::println);
        // value 降序
        map.entrySet().stream().sorted(Map.Entry.comparingByValue(Comparator.reverseOrder())).forEach(System.out::println);

    }
}
//
102=Rahul
101=Vijay
100=Amit
100=Amit
101=Vijay
102=Rahul
100=Amit
102=Rahul
101=Vijay
101=Vijay
102=Rahul
100=Amit
```



### HashMap

> Java HashMap类实现了Map接口，该接口允许我们存储键和值对，其中键应该是唯一的。 如果尝试插入重复键，它将替换相应键的元素。 使用键索引（例如更新，删除等）可以轻松执行操作。HashMap类位于java.util包中。
>
> Java中的HashMap类似于旧式Hashtable类，但未同步。 它也允许我们存储null元素，但是应该只有一个null键。 从Java 5开始，将其表示为HashMap <K，V>，其中K代表键，V代表值。 它继承了AbstractMap类并实现Map接口。

==HashSet仅包含值，而HashMap包含条目（键和值）==



**需要注意的是**

- Java HashMap包含基于键的值.
- Java HashMap仅包含唯一键.
- Java HashMap可能具有一个null键和多个null值.
- Java HashMap不同步.
- Java HashMap保持无序.
- Java HashMap类的初始默认容量为16 负载系数为0.75。

> 继承树和声明

![image-20201113125124201](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201113125124201.png)

```java
public class HashMap<K,V> extends AbstractMap<K,V> implements Map<K,V>, Cloneable, Serializable
```



>  constructors hashmap

| HashMap()                               | 构造默认的HashMap。                            |
| --------------------------------------- | ---------------------------------------------- |
| HashMap(Map<? extends K,? extends V> m) | 通过使用给定Map对象m的元素来初始化哈希图。     |
| HashMap(int capacity)                   | 将哈希映射的容量初始化为给定的整数值，容量。   |
| HashMap(int capacity, float loadFactor) | 通过使用其参数来初始化哈希图的容量和负载因子。 |

> method
>

| void clear()                                                 | It is used to remove all of the mappings from this map.      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| boolean isEmpty()                                            | It is used to return true if this map contains no key-value mappings. |
| Object clone()                                               | It is used to return a shallow copy of this HashMap instance: the keys and values themselves are not cloned. |
| Set entrySet()                                               | It is used to return a collection view of the mappings contained in this map. |
| Set keySet()                                                 | 返回此映射中包含的键的set。                                  |
| V put(Object key, Object value)                              | It is used to insert an entry in the map.                    |
| void putAll(Map map)                                         | It is used to insert the specified map in the map.           |
| V putIfAbsent(K key, V value)                                | It inserts the specified value with the specified key in the map only if it is not already specified. |
| V remove(Object key)                                         | It is used to delete an entry for the specified key.         |
| boolean remove(Object key, Object value)                     | It removes the specified values with the associated specified keys from the map. |
| V compute(K key, BiFunction<? super K,? super V,? extends V> remappingFunction) | It is used to compute a mapping for the specified key and its current mapped value (or null if there is no current mapping). |
| V computeIfAbsent(K key, Function<? super K,? extends V> mappingFunction) | It is used to compute its value using the given mapping function, if the specified key is not already associated with a value (or is mapped to null), and enters it into this map unless null. |
| V computeIfPresent(K key, BiFunction<? super K,? super V,? extends V> remappingFunction) | 给出一个新的映射键和其当前映射的值如果存在指定的键的值和空。 |
| boolean containsValue(Object value)                          | 映射中存在等于该值的某个值，则此方法返回true，否则返回false。 |
| boolean containsKey(Object key)                              | 映射中存在等于该键的某个键，则此方法返回true，否则返回false。 |
| boolean equals(Object o)                                     | It is used to compare the specified Object with the Map.     |
| void forEach(BiConsumer<? super K,? super V> action)         | 对映射中的每个条目执行遍历，直到所有条目都已处理或该操作引发异常为止。 |
| V get(Object key)                                            | This method returns the object that contains the value associated with the key. |
| V getOrDefault(Object key, V defaultValue)                   | 返回指定键映射到的值，如果映射不包含键的映射，则返回defaultValue。 |
| boolean isEmpty()                                            | This method returns true if the map is empty; returns false if it contains at least one key. |
| V merge(K key, V value, BiFunction<? super V,? super V,? extends V> remappingFunction) | 指定的键尚未与值关联或与null关联，将其与给定的非null值关联。. |
| V replace(K key, V value)                                    | It replaces the specified value for a specified key.         |
| boolean replace(K key, V oldValue, V newValue)               | It replaces the old value with the new value for a specified key. |
| void replaceAll(BiFunction<? super K,? super V,? extends V> function) | It replaces each entry's value with the result of invoking the given function on that entry until all entries have been processed or the function throws an exception. |
| Collection<V> values()                                       | 返回映射中包含的值的集合视。.                                |
| int size()                                                   | This method returns the number of entries in the map         |

> example--01
>
> 

```java
package java8.Map;

import java.util.HashMap;
import java.util.Map;

/**
 * @Author: xiaobo
 * @Date: 2020/11/13 12:10
 */
public class HashMap01 {
    public static void main(String[] args) {
        HashMap<Integer, String> hashMap = new HashMap<>();
        hashMap.put(10, "c");
        hashMap.put(30, "b");
        hashMap.put(20, "a");
        hashMap.put(40, null);
        hashMap.put(50, "a");
        /**
         * 要获取键和值元素，我们应该调用getKey（）和getValue（）方法。
         * Map.Entry接口包含getKey（）和getValue（）方法。
         * 但是，我们应该调用Map接口的entrySet（）方法来获取Map.Entry的实例。
         */
        for (Map.Entry m : hashMap.entrySet()) {
            System.out.println("key:" + m.getKey() + "value:" + m.getValue() + " ");
        }
        System.out.println("插入key相同后");
        hashMap.put(30,"bb");
        for (Map.Entry m : hashMap.entrySet()) {
            System.out.println("key:" + m.getKey() + "value:" + m.getValue() + " ");
        }
        System.out.println("putIfAbsent");
        hashMap.putIfAbsent(60,"xiaobo");
        for (Map.Entry m : hashMap.entrySet()) {
            System.out.println("key:" + m.getKey() + "value:" + m.getValue() + " ");
        }
        //
        System.out.println("remove");
        hashMap.remove(20);
        hashMap.remove(50,"a");
        for (Map.Entry m : hashMap.entrySet()) {
            System.out.println("key:" + m.getKey() + "value:" + m.getValue() + " ");
        }
        System.out.println("replace");
        hashMap.replace(40,null,"40");
        System.out.println("replaceAll");
        hashMap.replaceAll((k,v)->"xiaoobo");
        for (Map.Entry m : hashMap.entrySet()) {
            System.out.println("key:" + m.getKey() + "value:" + m.getValue() + " ");
        }

    }
}
//
key:50value:a 
key:20value:a 
key:40value:null 
key:10value:c 
key:30value:b 
插入key相同后
key:50value:a 
key:20value:a 
key:40value:null 
key:10value:c 
key:30value:bb 
putIfAbsent
key:50value:a 
key:20value:a 
key:40value:null 
key:10value:c 
key:60value:xiaobo 
key:30value:bb 
remove
key:40value:null 
key:10value:c 
key:60value:xiaobo 
key:30value:bb 
replace
replaceAll
key:40value:xiaoobo 
key:10value:xiaoobo 
key:60value:xiaoobo 
key:30value:xiaoobo 

```



### TreeMap

> Java TreeMap类是基于红黑树的实现。 它提供了一种按排序顺序存储键值对的有效方法。

**需要注意的是：**

- Java TreeMap包含基于键的值。 它实现了NavigableMap接口并扩展了AbstractMap类.
- Java TreeMap仅包含唯一元素.
- Java TreeMap不能具有null键，但是可以具有多个null值.
- Java TreeMap不同步.
- Java TreeMap保持升序

> treeMap 维护的 key-value
>
> K：这是此映射维护的键的类型。V：这是映射值的类型。

> 继承树和声明

![image-20201113131200842](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201113131200842.png)

```java
public class TreeMap<K,V> extends AbstractMap<K,V> implements NavigableMap<K,V>, Cloneable, Serializable  
```



> constructors

| TreeMap()                                 | 构造一个空的treeMap，该TreeMap将使用其键的自然顺序进行排序。 |
| ----------------------------------------- | ------------------------------------------------------------ |
| TreeMap(Comparator<? super K> comparator) | 构造一个空的treemap，基于树的映射，该映射将使用比较器comp进行排序。 |
| TreeMap(Map<? extends K,? extends V> m)   | 它用于使用m中的条目初始化树图，该树图将使用键的自然顺序进行排序。 |
| TreeMap(SortedMap<K,? extends V> m)       | 它用于使用SortedMap sm中的条目初始化树图，这些条目将以与sm相同的顺序排序。 |

> method

| Map.Entry<K,V> ceilingEntry(K key)                           | 返回具有最小键值（大于或等于指定键）的键值对，如果没有这样的键，则返回null。 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| K ceilingKey(K key)                                          | 返回大于指定的键最小的键；如果没有这样的键，则返回null。     |
| void clear()                                                 | 所有键值对从映射中删除。                                     |
| Object clone()                                               |                                                              |
| Comparator<? super K> comparator()                           | 按顺序排列键的比较器；如果映射使用自然顺序，则返回null。     |
| NavigableSet<K> descendingKeySet()                           | 返回映射中包含的键的逆序NavigableSet视图。                   |
| NavigableMap<K,V> descendingMap()                            | 降序返回指定的键值对                                         |
| Map.Entry firstEntry()                                       | 返回键值最小的键值对。                                       |
| Map.Entry<K,V> floorEntry(K key)                             | 返回最大的键，小于或等于指定的键；如果没有这样的键，则返回null。 |
| void forEach(BiConsumer<? super K,? super V> action)         | for-each 遍历                                                |
| SortedMap<K,V> headMap(K toKey)                              | 返回键值严格小于(===)toKey的键值对                           |
| NavigableMap<K,V> headMap(K toKey, boolean inclusive)        | 返回其键小于  (或等于  （为true的话））toKey的键值对。       |
| Map.Entry<K,V> higherEntry(K key)                            | 返回严格大于给定键的最小键；如果没有这样的键，则返回null。   |
| K higherKey(K key)                                           | 此映射包含指定键的映射，则用于返回true                       |
| Set keySet()                                                 | 返回映射中存在的键的集合。返回一个set                        |
| Map.Entry<K,V> lastEntry()                                   | I返回具有最大键的键值对，如果没有这样的键，则返回null。      |
| Map.Entry<K,V> lowerEntry(K key)                             | 返回与最大键严格小于给定键的键-值映射关系；如果没有这样的键，则返回null。 |
| K lowerKey(K key)                                            | 返回的最大key严格小于给定的key；如果没有这样的key，则返回null。 |
| NavigableSet<K> navigableKeySet()                            | It returns a NavigableSet view of the keys contained in this map. |
| Map.Entry<K,V> pollFirstEntry()                              | 删除并返回与此映射中最小键关联的键-值映射；如果映射为空，则返回null。 |
| Map.Entry<K,V> pollLastEntry()                               | 删除并返回与此映射中最大键关联的键-值映射；如果映射为空，则返回null。 |
| V put(K key, V value)                                        | 在地图中插入具有指定键的指定值。                             |
| void putAll(Map<? extends K,? extends V> map)                | 用于将所有键值对从一个映射复制到另一个映射。                 |
| V replace(K key, V value)                                    | 将指定的值的键替换为指定的键。                               |
| boolean replace(K key, V oldValue, V newValue)               | It replaces the old value with the new value for a specified key. |
| void replaceAll(BiFunction<? super K,? super V,? extends V> function) | 用在该条目上调用给定函数的结果替换每个条目的值，直到处理完所有条目或该函数引发异常为止。 |
| NavigableMap<K,V> subMap(K fromKey, boolean fromInclusive, K toKey, boolean toInclusive) | 返回键-值对，其键的范围从fromKey到toKey。                    |
| SortedMap<K,V> subMap(K fromKey, K toKey)                    | 返回键-值对，其键的范围从fromKey（包括）到toKey（不包括）。  |
| SortedMap<K,V> tailMap(K fromKey)                            | 返回键值大于或等于fromKey的键值对。                          |
| NavigableMap<K,V> tailMap(K fromKey, boolean inclusive)      | 返回其键大于（或等于，如果包含在内，则为true）fromKey的键-值对。 |
| boolean containsKey(Object key)                              | 映射包含指定键的映射，则返回true。                           |
| boolean containsValue(Object value)                          | 判断是否有值                                                 |
| K firstKey()                                                 | 用于返回此排序映射中当前的第一个（最低）键。                 |
| V get(Object key)                                            | 用于返回映射将指定键映射到的值。                             |
| K lastKey()                                                  | 返回排序映射图中当前的最后一个（最高）键。                   |
| V remove(Object key)                                         | 从映射中删除指定键的键值对。                                 |
| Set<Map.Entry<K,V>> entrySet()                               | 返回映射中包含的映射的set。                                  |
| int size()                                                   | It returns the number of key-value pairs exists in the hashtable. |
| Collection values()                                          | 返回映射中包含的值的集合。                                   |

> example--01
>
> add  remove

```java
package java8.Map;

import java.util.Map;
import java.util.TreeMap;

/**
 * @Author: xiaobo
 * @Date: 2020/11/13 13:14
 */
public class TreeMap01 {
    public static void main(String[] args) {
       Map<Integer,String > map = new TreeMap<>();
       map.put(5,"a");
       map.put(10,"c");
       map.put(1,"a");
       map.put(20,"aa");
       map.put(0,"b");
       for(Map.Entry m : map.entrySet()){
           System.out.print(" "+m.getKey()+"="+m.getValue());
       }
       System.out.println("\n");
       map.forEach((key,value)-> System.out.print(" "+key+"="+value));
       map.remove(1);
       map.forEach((key,value)-> System.out.print(" "+key+"="+value));
    }
}
//
 0=b 1=a 5=a 10=c 20=aa

 0=b 1=a 5=a 10=c 20=aa 0=b 5=a 10=c 20=aa
```

> example---02
>
> NavigableMap

```java
package java8.Map;

import java.util.NavigableMap;
import java.util.TreeMap;

/**
 * @Author: xiaobo
 * @Date: 2020/11/13 21:55
 */
public class TreeMap02 {
    public static void main(String[] args) {
        NavigableMap<Integer, String> map = new TreeMap();
        map.put(5,"a");
        map.put(10,"c");
        map.put(1,"a");
        map.put(20,"aa");
        map.put(0,"b");
        // 保持降序
        System.out.println(map.descendingMap());
        //返回键值对小于或等于指定键的键值对。
        System.out.println(map.headMap(10,true));
        // 返回区间的key-value
        System.out.println(map.subMap(8,true,15,false));
        // 返回键值对大于或等于指定键的键值对。
        System.out.println(map.tailMap(10,true));
    }
}
//
{20=aa, 10=c, 5=a, 1=a, 0=b}
{0=b, 1=a, 5=a, 10=c}
{10=c}
{10=c, 20=aa}
```

> example--03
>
> sortedMap

```java
package java8.Map;

import java.util.SortedMap;
import java.util.TreeMap;

/**
 * @Author: xiaobo
 * @Date: 2020/11/13 21:59
 */
public class TreeMap03 {
    public static void main(String[] args) {
        SortedMap map = new TreeMap();
        map.put(5,"a");
        map.put(10,"c");
        map.put(1,"a");
        map.put(20,"aa");
        map.put(0,"b");
        //返回键值对少于指定键的键值对。
        System.out.println("headMap: "+map.headMap(10));
        //返回键值对大于或等于指定键的键值对。
        System.out.println("tailMap: "+map.tailMap(9));
        //返回指定键之间存在的键值对。
        System.out.println("subMap: "+map.subMap(10, 20));
    }
}
//
headMap: {0=b, 1=a, 5=a}
tailMap: {10=c, 20=aa}
subMap: {10=c}
```



==总结：hashMap和TreeMap的区别：==

| HashMap                      | TreeMap                   |
| :--------------------------- | :------------------------ |
| 1) HashMap可以包含一个空键。 | TreeMap不能包含任何空键。 |
| 2) HashMap不维护任何顺序。   | TreeMap保持默认升序。     |



### LinkedHashMap

> Java LinkedHashMap类是Map接口的Hashtable和Linked list实现，具有可预测的迭代顺序。 它继承了HashMap类并实现了Map接口。

**需要注意的是：**

- Java LinkedHashMap包含基于键的值.
- Java LinkedHashMap包含唯一元素.
- Java LinkedHashMap可能具有一个null键和多个null值.
- Java LinkedHashMap是不同步的.
- Java LinkedHashMap保持插入顺序.
- Java HashMap类的初始默认容量为16 负载系数为0.75。

> 继承树和声明

![image-20201113220949009](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201113220949009.png)

```java
public class LinkedHashMap<K,V> extends HashMap<K,V> implements Map<K,V>  
```



> constructor

| LinkedHashMap()                                              | 用于构造默认的LinkedHashMap。                   |
| ------------------------------------------------------------ | ----------------------------------------------- |
| LinkedHashMap(int capacity)                                  | 初始化具有给定容量的LinkedHashMap。             |
| LinkedHashMap(int capacity, float loadFactor)                | 用于初始化容量和负载系数。                      |
| LinkedHashMap(int capacity, float loadFactor, boolean accessOrder) | 用于初始容量和加载因子与指定的排序模式          |
| LinkedHashMap(Map<? extends K,? extends V> m)                | 用于使用给定Map类m中的元素初始化LinkedHashMap。 |



> method

| V get(Object key)                                            | 返回指定键映射到的值。                                       |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| void clear()                                                 | 将所有键值对从映射中删除                                     |
| boolean containsValue(Object value)                          | 判断value，有则返回true。                                    |
| Set<Map.Entry<K,V>> entrySet()                               | 返回映射中包含的映射的Set视图。                              |
| void forEach(BiConsumer<? super K,? super V> action)         | 对映射中的每个条目执行给定的遍历操作，直到所有条目都已处理或该操作引发异常为止。 |
| V getOrDefault(Object key, V defaultValue)                   | 返回指定键所映射到的值；如果此映射不包含键的映射关系，则返回defaultValue。 |
| Set<K> keySet()                                              | 返回map中包含的键的Set视图                                   |
| protected boolean removeEldestEntry(Map.Entry<K,V> eldest)   | 删除最大的条目时返回true。                                   |
| void replaceAll(BiFunction<? super K,? super V,? extends V> function) | It replaces each entry's value with the result of invoking the given function on that entry until all entries have been processed or the function throws an exception. |
| Collection<V> values()                                       | 返回此映射中包含的值的Collection视图。                       |

> example--01
>
> put  iterator

```java
package java8.Map;

import java.util.LinkedHashMap;

/**
 * @Author: xiaobo
 * @Date: 2020/11/13 22:21
 */
public class LinkedHashMap01 {
    public static void main(String[] args) {
        LinkedHashMap<Integer, String> map = new LinkedHashMap<>();
        map.put(5,"a");
        map.put(10,"c");
        map.put(1,"a");
        map.put(20,"aa");
        map.put(0,"b");
        map.forEach((key,value)-> System.out.print("|"+key+"="+value));
        System.out.println("\n");
        System.out.println("map.key:"+map.keySet());
        System.out.println("map.value"+map.values());
        System.out.println("map.entry:"+map.entrySet());
        map.remove("0");
    }
}
//
|5=a|10=c|1=a|20=aa|0=b

map.key:[5, 10, 1, 20, 0]
map.value[a, c, a, aa, b]
map.entry:[5=a, 10=c, 1=a, 20=aa, 0=b]
```



### HashTable

> Java Hashtable类实现了一个哈希表，该哈希表将键映射到值。 它继承了Dictionary类并实现Map接口。



**需要注意的是：**

- 哈希表是列表的数组。 每个列表称为存储桶。 桶的位置通过调用hashcode（）方法来标识。
-  Hashtable包含基于key的值.Java Hashtable类包含唯一的元素.
- Java Hashtable类不允许空键或值.
- Java Hashtable类已同步.
-  Hashtable类的初始默认容量为11而loadFactor为0.75。



> 继承树和声明![image-20201113225643566](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201113225643566.png)

```java
public class Hashtable<K,V> extends Dictionary<K,V> implements Map<K,V>, Cloneable, Serializable  

```



> constructors

| Hashtable()                               | 创建一个具有初始默认容量和负载因子的空哈希表。         |
| ----------------------------------------- | ------------------------------------------------------ |
| Hashtable(int capacity)                   | 接受一个整数参数，并创建一个包含指定初始容量的哈希表。 |
| Hashtable(int capacity, float loadFactor) | 用于创建具有指定初始容量和负载因子的哈希表。           |
| Hashtable(Map<? extends K,? extends V> t) | 使用与给定Map相同的映射创建一个新的哈希表。            |

> method
>
> 常用的api

| void clear()                                                 | It is used to reset the hash table.                          |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Object clone()                                               | It returns a shallow copy of the Hashtable.                  |
| V compute(K key, BiFunction<? super K,? super V,? extends V> remappingFunction) | It is used to compute a mapping for the specified key and its current mapped value (or null if there is no current mapping). |
| V computeIfAbsent(K key, Function<? super K,? extends V> mappingFunction) | It is used to compute its value using the given mapping function, if the specified key is not already associated with a value (or is mapped to null), and enters it into this map unless null. |
| V computeIfPresent(K key, BiFunction<? super K,? super V,? extends V> remappingFunction) | It is used to compute a new mapping given the key and its current mapped value if the value for the specified key is present and non-null. |
| Enumeration elements()                                       | 返回哈希表中值的枚举。                                       |
| Set<Map.Entry<K,V>> entrySet()                               | 返回映射中包含的映射的set。                                  |
| boolean equals(Object o)                                     | 将指定的Object与Map中进行比较。                              |
| void forEach(BiConsumer<? super K,? super V> action)         | It performs the given action for each entry in the map until all entries have been processed or the action throws an exception. |
| V getOrDefault(Object key, V defaultValue)                   | It returns the value to which the specified key is mapped, or defaultValue if the map contains no mapping for the key. |
| int hashCode()                                               | 返回Map的哈希码值                                            |
| Enumeration<K> keys()                                        | 返回哈希表中键的枚举                                         |
| Set<K> keySet()                                              | 返回地图中包含的键的Set视图                                  |
| V merge(K key, V value, BiFunction<? super V,? super V,? extends V> remappingFunction) | If the specified key is not already associated with a value or is associated with null, associates it with the given non-null value. |
| V put(K key, V value)                                        | 指定的值和指定的键插入哈希表中。                             |
| void putAll(Map<? extends K,? extends V> t))                 | It is used to copy all the key-value pair from map to hashtable. |
| V putIfAbsent(K key, V value)                                | If the specified key is not already associated with a value (or is mapped to null) associates it with the given value and returns null, else returns the current value. |
| boolean remove(Object key, Object value)                     | It removes the specified values with the associated specified keys from the hashtable. |
| V replace(K key, V value)                                    | It replaces the specified value for a specified key.         |
| boolean replace(K key, V oldValue, V newValue)               | It replaces the old value with the new value for a specified key. |
| void replaceAll(BiFunction<? super K,? super V,? extends V> function) | It replaces each entry's value with the result of invoking the given function on that entry until all entries have been processed or the function throws an exception. |
| String toString()                                            | It returns a string representation of the Hashtable object.  |
| Collection values()                                          | 返回映射中包含的值的集合视图。                               |
| boolean contains(Object value)                               | 如果哈希表中存在等于该值的值，则此方法返回true，否则返回false。 |
| boolean containsValue(Object value)                          | This method returns true if some value equal to the value exists within the hash table, else return false. |
| boolean containsKey(Object key)                              | This method return true if some key equal to the key exists within the hash table, else return false. |
| boolean isEmpty()                                            | 如果哈希表为空，则此方法返回true；否则，此方法返回true。 如果包含至少一个key，则返回false。 |
| protected void rehash()                                      | 用于增加哈希表的大小并重新哈希其所有键。                     |
| V get(Object key)                                            | 方法返回包含与键关联的值的对象。                             |
| V remove(Object key)                                         | 用于删除密钥及其值。 此方法返回与键关联的值。                |
| int size()                                                   | This method returns the number of entries in the hash table. |



> example --01
>
> 

```java
package java8.Map;

import java.util.Hashtable;

/**
 * @Author: xiaobo
 * @Date: 2020/11/13 22:54
 */
public class HashTable01 {
    public static void main(String[] args) {
        Hashtable<Object, Object> hashtable = new Hashtable<>();
        hashtable.put(5,"a");
        hashtable.put(10,"c");
        hashtable.put(1,"a");
        hashtable.put(20,"aa");
        hashtable.put(0,"b");
        hashtable.forEach((key,value)-> System.out.print(" "+key+"="+value));
        hashtable.remove("5");
        System.out.println("\n");
        System.out.println(hashtable.getOrDefault(30,"not found"));
        System.out.println(hashtable.get(30));
        System.out.println("putIfAbsent");
        hashtable.putIfAbsent(100,"aaa");
        hashtable.forEach((key,value)-> System.out.println(key+"="+value));

    }
}
//
 0=b 1=a 5=a 20=aa 10=c

not found
null
putIfAbsent
0=b
100=aaa
1=a
5=a
20=aa
10=c
```



### HashMap vs  HashTable

> HashMap和Hashtable都用于存储键和值形式的数据。 两者都使用散列技术来存储唯一键。

| HashMap                                                      | Hashtable                                           |
| :----------------------------------------------------------- | :-------------------------------------------------- |
| 1)HashMap不同步。 它不是线程安全的，没有适当的同步代码就无法在许多线程之间共享。 | 哈希表已同步。 它是线程安全的，可以与许多线程共享。 |
| 2) HashMap允许一个空键和多个空值                             | Hashtable不允许任何空键或值。                       |
| 3) HashMap是JDK 1.2中引入的新类。                            | 哈希表是一个遗留类。                                |
| 4) HashMap很快。                                             | Hashtable 哈希表很慢。.                             |
| 5) 我们可以通过调用以下代码映射使HashMap同步：m = Collections.synchronizedMap（hashMap）; | 哈希表在内部是同步的，不能不同步。                  |
| 6) HashMap被Iterator遍历。                                   | Hashtable被Enumerator和Iterator遍历。               |
| 7)HashMap中的迭代器是快速失败的。                            | Hashtable中的枚举器不是快速失败的。                 |
| 8) HashMap继承AbstractMap类。                                | Hashtable继承Dictionary类。.                        |