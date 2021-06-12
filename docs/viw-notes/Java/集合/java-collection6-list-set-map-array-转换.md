[TOC]



# 1. list



## list ---> array

> 第一种

```java
ArrayList<Integer> list = new ArrayList<>(array.length);
int[] arr = list.stream().mapToInt(Integer::valueOf).toArray();
```



## list---> map



> 第一种

```

```



# 2.set

##  set----> list

- 1.

```java
new HashSet<>(list);
```



## set--> array

- 1. set --> string[]

  ```java
  String[] strArr = new String[set.size()];
  String[] arr =set.toArray(strArr);
  ```





# 3. map



## map --> list





# 4. array



## array--> list

- 1.int[] 转 List<Integer>

```java
 List<Integer> list1 = Arrays.stream(data).boxed().collect(Collectors.toList());
```

- 2.  int[] 转 Integer[]

```java
 Integer[] integers1 = Arrays.stream(data).boxed().toArray(Integer[]::new);
```

