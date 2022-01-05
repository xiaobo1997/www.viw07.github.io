[TOC]

# 1.bigInteger& bigDecimal

## bigInteger

> 在 java.math  package 下 有两个类
>
> 主要是对  整数 和浮点数精度不满足要求的时候
>
> BigInteget (实现了任意精度的整数运算)和 BigDecimal (任意精度的浮点数运算)
>



```java
// 转为大数值
BigInteger a  = BigInteger.valueOf(100);
```

```java
/**
处理算术运算符 + - * /
需要使用大数
值类中的 add 和 multiply 等方法
*/

BigInteget b = c.add(a); //  b=c+a

BigInger d = c.multiply(b.add(BigInteger.valueOf(2))); //  d = c * (b + 2)
```



## BigDecimal



> 常用于金额计算，建议使用string的构造函数

```java

BigDecimal add(BigDecimal augend) 加法运算。

BigDecimal subtract(BigDecimal subtrahend) 减法运算。

BigDecimal multiply(BigDecimal multiplicand) 乘法运算。

BigDecimal divide(BigDecimal divisor) 除法运算。

// 保留精度

　	  //取两位整数和三位小数，整数不足部分以0填补。 
　　　　System.out.println(new DecimalFormat("00.000").format(pi));// 03.142 
　　　　//取所有整数部分 
　　　　System.out.println(newDecimalFormat("#").format(pi));　　　//3 
　　　　//以百分比方式计数，并取两位小数 
　　　　System.out.println(new DecimalFormat("#.##%").format(pi));　//314.16% 

```



# 2. 数组



## 1.数组

> 在声明数组变量时， 需要指出数组类型 （ 数据元素类型紧跟 []) 和数组变量的名字。

```java
int[] a;
//将 a 初始化为一个真正的数组。应该使用 new 运算符创建数组。
int[] a = new int[2];
```



> 一旦创建了数组， 就不能再改变它的大小（尽管可以改变每一个数组元素）
>
> 或者可以使用动态数组



> 创建一个数字数组时， 所有元素都初始化为 0。boolean 数组的元素会初始化为 fals% 对
>
> 象数组的元素则初始化为一个特殊值 null, 这表示这些元素（还）未存放任何对象。



## 2 for each

> 如：打印数组 a 的每一个元素，一个元素占一行

```java
for (int element : a)
System.out.println(element):
```



```java
// 传统循环
for (int i = 0; i < a.length; i++)
System,out.println(a[i]);
```



>式打印数组中的所有值 也可以  toString(a)
>
>调 用 Arrays.toString(a), 返回一个包含数组元素的字符串，这些元素被放置在括号内， 并
>
>用逗号分隔， 例如，“ [2,3,5,7，11，13]” 、



## 3.匿名数组

> 创建数组对象并同时赋予初始值

```
int[] small Primes = { 2, 3, 5, 7, 11, 13 };
```



> 初始化一个匿名的数组：
>
> ```
> new intD { 17, 19, 23, 29, 31, 37 }
> ```
>
> 将创建一个新数组并利用括号中提供的值进行初始化，数组的大小就是初始值的
>
> 个数。 使用这种语法形式可以在不创建新变量的情况下重新初始化一个数组。例如：
>
> ```
> small Primes = new int[] { 17, 19, 23, 29, 31, 37 };
> ```



## 4.数组拷贝和内存中的数组

>数组的拷贝：将一个数组变量拷贝给另一个数组变量。这时， 两个变量将引用同一个数组

```java
int[] arr2 = {6,1};

int[] arr1 = arr2;
//arr1[0] 和arr2[0] 指向同一内存地址
arr1[0] = 6;


// 拷贝数组api
<T> copyOf(<T> arr,int newLength)
```



> 数组是一种引用数据类型， 也就是 数组实际元素的值和数组变量是存储在不同的地方的
>
> 访问真实对象：引用变量是访问真实对象的根本方式
>
> 实际数组元素是存储在heap中，而引用该数组元素的变量如果是局部变量，那么就是存储在stack中
>
> 如果需要让数组被垃圾回收，要么该heap中的数组没有任何引用变量指向它，或者让它的变量赋null，就相当于 让数组的引用变量 和 实际数组元素自己的 联系断开了

![image-20201026225958898](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201026225958898.png)





##  5 多维数组

> 创建二维数组

```java
int[][] arr;
```



> 访问 二维数组的元素

```
for(arr1[] row : a)
 for(arr2[] value: b)
  ....
```

```java
 int[][] arr = {
                {1, 2, 3},
                {4, 5, 6}
        };
// 两层for循环
        for (int i = 0;i<arr.length;i++) {
            for (int j = 0;j<arr[i].length;j++) {
                System.out.println(arr[i][j]);
            }
        }
```



# 







