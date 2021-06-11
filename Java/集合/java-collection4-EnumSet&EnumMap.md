[TOC]

# 1.EnumSet

## 使用

> Java EnumSet类是用于枚举类型的专用Set实现。 它继承AbstractSet类并实现Set接口。



> 继承声和声明

![image-20201114163957712](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201114163957712.png)

```java
public abstract class EnumSet<E extends Enum<E>> extends AbstractSet<E> implements Cloneable, Serializable  

```



> method

| static <E extends Enum<E>> EnumSet<E> allOf(Class<E> elementType) | 用于创建一个枚举集，其中包含指定元素类型中的所有元素。 |
| ------------------------------------------------------------ | ------------------------------------------------------ |
| static <E extends Enum<E>> EnumSet<E> copyOf(Collection<E> c) | 创建从指定集合初始化的枚举集。                         |
| static <E extends Enum<E>> EnumSet<E> noneOf(Class<E> elementType) | 创建具有指定元素类型的空枚举集。                       |
| static <E extends Enum<E>> EnumSet<E> of(E e)                | 创建最初包含指定元素的枚举集。                         |
| static <E extends Enum<E>> EnumSet<E> range(E from, E to)    | 创建最初包含指定元素的枚举集。                         |
| EnumSet<E> clone()                                           | 返回此集合的副本。                                     |



> example--01

```java
package java8.enumcollection;

import java.util.EnumSet;
import java.util.Iterator;

/**
 * @Author: xiaobo
 * @Date: 2020/11/14 16:42
 */
enum day{
    SUNDAY, MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY
}
public class EnumSet01 {
    public static void main(String[] args) {
        EnumSet<day> es =  EnumSet.of(day.SUNDAY,day.FRIDAY);
        Iterator<day> iterator = es.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
        System.out.println("\n");
        EnumSet<day> days = EnumSet.allOf(day.class);
        Iterator<day> iterator1 = days.iterator();
        iterator1.forEachRemaining(s-> System.out.println(s));
    }
}
// ========

SUNDAY
FRIDAY
    
SUNDAY
MONDAY
TUESDAY
WEDNESDAY
THURSDAY
FRIDAY
SATURDAY
```



# 2.EnumMap

## 使用

>Java EnumMap类是枚举键的特殊Map实现。 它继承了Enum和AbstractMap类。



> 继承树和声明

![image-20201114230959305](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201114230959305.png)

```java
public class EnumMap<K extends Enum<K>,V> extends AbstractMap<K,V> implements Serializable, Cloneable  

```



> constructor

| EnumMap(Class<K> keyType)         | 创建具有指定键类型的空枚举映射。             |
| --------------------------------- | -------------------------------------------- |
| EnumMap(EnumMap<K,? extends V> m) | 创建具有与指定枚举map相同的键类型的枚举map。 |
| EnumMap(Map<K,? extends V> m)     | 创建从指定map初始化的枚举map。               |



> method

| 1    | [clear()](https://www.javatpoint.com/post/java-enummap-clear-method) | 清除map中的所有映射。                                        |
| ---- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2    | [clone()](https://www.javatpoint.com/post/java-enummap-clone-method) | 将一个映射的映射值复制到另一个映射。                         |
| 3    | [containsKey()](https://www.javatpoint.com/post/java-enummap-containskey-method) | 检查此映射中是否存在指定的key。                              |
| 4    | [containsValue()](https://www.javatpoint.com/post/java-enummap-containsvalue-method) | 检查此映射中是否存在指定的value。                            |
| 5    | [entrySet()](https://www.javatpoint.com/post/java-enummap-entryset-method) | 创建EnumMap中包含的一组set。                                 |
| 6    | [equals()](https://www.javatpoint.com/post/java-enummap-equals-method) | 比较两个映射是否相等。                                       |
| 7    | [get()](https://www.javatpoint.com/post/java-enummap-get-method) | 获取指定键的映射值。                                         |
| 8    | [hashCode()](https://www.javatpoint.com/post/java-enummap-hashcode-method) | It is used to get the hashcode value of the EnumMap.         |
| 9    | [keySet()](https://www.javatpoint.com/post/java-enummap-keyset-method) | 获取地图中包含的键的key。                                    |
| 10   | [size()](https://www.javatpoint.com/post/java-enummap-size-method) | It is used to get the size of the EnumMap.                   |
| 11   | [Values()](https://www.javatpoint.com/post/java-enummap-values-method) | 创建此映射中包含的值的collection。                           |
| 12   | [put()](https://www.javatpoint.com/post/java-enummap-put-method) | 将给定值与此EnumMap中的给定键关联。                          |
| 13   | [putAll()](https://www.javatpoint.com/post/java-enummap-putall-method) | It is used to copy all the mappings from one EnumMap to a new EnumMap. |
| 14   | [remove()](https://www.javatpoint.com/post/java-enummap-remove-method) | 存在给定键，则用于从EnumMap中删除给定键的映射。              |



> example--01

```java
package java8.enumcollection;

import java.util.Collection;
import java.util.EnumMap;

/**
 * @Author: xiaobo
 * @Date: 2020/11/14 23:14
 */

public class EnumMap01 {
    enum size{
        X,XL,XXL
    };
    public static void main(String[] args) {
        EnumMap<size,String> enumMap = new EnumMap<size,String>(size.class);
        enumMap.put(size.X,"1");
        enumMap.put(size.XL,"2");
        enumMap.put(size.XXL,"3");
        System.out.println(enumMap);
        Collection<String> values = enumMap.values();
        System.out.println(values);
    }

}
//============
{X=1, XL=2, XXL=3}
[1, 2, 3]
```