[TOC]

# 1.基本数据类型

> java是强类型语言，每一个变量和表达式需要在编译时确认类型，相当于先声明后使用

## 类型

> 基本数据类型有8种
>
> - 4种整型
> - 2种浮点数类型
> - 1种表示Unicode 编码的char类型
> - 1种表示真值的 boolean类型
>
> 还有一个表示任意精度的 big number 不过是java对象，

### 整型

![image-20201016123139607](C:/Users/xiaobo/AppData/Roaming/Typora/typora-user-images/image-20201016123139607.png)



### 浮点

![image-20201016123238798](C:/Users/xiaobo/AppData/Roaming/Typora/typora-user-images/image-20201016123238798.png)



### char类型

> 在java中， Char表示 UTF-16的一个编码单元，现处理 UTF-16建议把字符串作为抽象数据类型处理
>
> 建议在 程序中少用char
>
> char类型的字面量需要单引号括起来（`'A'`)； 比如，`A`是编码值 65 所对应的字符常量



特殊字符的转义：

![image-20201016125818559](C:/Users/xiaobo/AppData/Roaming/Typora/typora-user-images/image-20201016125818559.png)

### boolean

> boolean 有两个值 true 和false



## 类型转换

###  数据类型自动转换

> 数据类型的合法转换，
>
> 实线表示转换 不影响精度，虚表示会丢失

![image-20201016130939107](C:/Users/xiaobo/AppData/Roaming/Typora/typora-user-images/image-20201016130939107.png)

### 强制类型转换

> 可能会丢失数据
>
> 如果试图将一个数值从一种类型强制转换为另一种类型， 而又超出了目标类型的
>
> 表示范围，结果就会截断成一个完全不同的值。例如，（byte ) 300 的实际值为 44。
>
> 因为 byte 最大就是 127
>
> 注意 不要把 boolean和任何类型转换，

```java
	double d = 1.22;
	// 变量前面接 类型即可
	int i = (int)d;
```



### Striing 和金额decimal和double的转换



```java
// 1.string 
String s1 = "1.01";
// 2.构造DecimalFormat格式化对象
DecimalFormat df = new DecimalFormat("#0.00");
// 3.String => double
Double d1 = Double.parseDouble(s1);
```



# 2.运算符



## 基本运算符和级别

> +,-,*,/
>
> %整数的取余，如 整数的取余 15/7 =1  如  浮点数的取余15.0/7 = 7.5
>
> 还有`i++` , `i--`，`i+=10`, `i/=10`,  `i*=10` 等等
>
> 还有 a>b? 1:0; 
>
> 还有 3==7 这种 关系运算符
>
> 还有 位运算符   |    `^`  `&`   `~`



- 运算符的级别

![image-20201016132233391](C:/Users/xiaobo/AppData/Roaming/Typora/typora-user-images/image-20201016132233391.png)

![image-20201016132244005](C:/Users/xiaobo/AppData/Roaming/Typora/typora-user-images/image-20201016132244005.png)



- 枚举类型

> 有时候，变量的取值只在一个有限的集合内。例如： 销售的服装或比萨饼只有小、中、大和超大这四种尺寸
>
> 针对这种情况， 可以自定义枚举类型。枚举类型包括有限个命名的值
>
> `enum Size {L,LL,LLL,ML}`
>
> 声明这种类型的变量
>
> `Size s = Size.LL;`
>
> Size 类型的变量只能存储这个类型声明中给定的某个枚举值，或者 null 值，null 表示这个变量没有设置任何值



- 三目运算符

```java
String c =  (a>b)? "A":"a";  a>b 为true c =A  false c  =a
```



# 3.字符串



> 不可变字符串
>
> 因为不能修改java字符串中的字符，string也叫不可变字符串，
>
> 不可变字符串 优点：编译器让字符串共享；
>
> 将各种字符串存放在公共的存储池中。字符串变量
>
> 指向存储池中相应的位置。如果复制一个字符串变量， 原始字符串与复制的字符串共享相同
>
> 的字符。

```java
String str = "aaa";
```



> 检测字符是否相等
>
> a.equals(b);  a 和b 可以是字符串变量，也可以是字符串字面量
>
> 不建议使用 `==`来判断两个字符串是否相等

```java
/** 
==符只能够确定两个字串是否放置在同一个位置上。如果字符串放置在同一个位置上， 它们必然相等。但是，
完全有可能将内容相同的多个字符串的拷贝放置在不同的位置上。
 */
String str1 = "aa";
String str2 = str1.substring(0, 1)); 
"a".equals(str2); // true
"a" == str2; //fasle
```



> 空字符串 和null 
>
> 空字符串 ： 是 长度为0  内容为空  是java对象
>
> String 变量还可以存放一个特殊的值， 名为 null, 这表示目前没有任何对象与该变量关联

```java
// 检测字符串是否为空
if(str == null){}
```



> string由 char组成

```java
// 返回代码单元的数量
String str1 = "aa";
int len = str1.length(); // 2
/** 使用 codePoints 方法  遍历string 生成一个 int 值的“ 流”，
每个 int 值对应一个码点。  转换为一个数组 再完成遍历
 */
 int[] ints = str1.codePoints().toArray();
        for(int i :ints ){
            System.out.println(i); // 97  97 是字符编码值
```

> string 的API

```java
		char charAt(int index)
        int codePointAt(int index)
            int copareTo(Sting other)
            InputStream codePoints()
            new String(int[] codePoints,int offsert,int count) //用数组中从 offset 开始的 count 个码点构造一个字符串。
            boolean equasl(Object other)
            boolean equalsIgnoreCase(String other)
            
            boolean startsWith(String preifx)
            boolean endsWith(String suffix) //如果字符串以 suffix 开头或结尾， 则返回 true。
            
            int indexOf(String str)
            int indexOf(String str,int foemIndex)
            int indexOf(int cp)
            int indexOf(int cp ,int formIndex)
            
            int lastIndexOf(String str)
            int lastIndexOf(String str,int formIndex)
            
            int length()
            
            String replace(CharSequence oldString,CharSequence newString))
            
            
			String substring(int beginlndex ) 
            String substring(int beginlndex, int endlndex )
			//返回一个新字符串。这个字符串包含原始字符串中从 beginlndex 到串尾或 endlndex-l的所有代码单元。
            
            String toLowerCase()
            String toUpperCase()
            
            String trim()
            
            String join()
            
```



> 构建字符串
>
> 使用stringBuilder  :  前身是 StringBuffer，允许采用多线程的方式执行添加或删除字符的操作 
>
> 

```java
// new StringBuilder
StringBuilder sb = new StringBuilder();
// 调用 append()
sb.append(ch);// append  a single character
sb.append(str); // append a string
//构建
String str = sb.toString();

// StringBuilder api
StirngBuilder()
    int length()
    StringBuilder append()
   StringBuilder append()
    void setCharAt(int i ,char c) // 将第 i 个代码单元设置为 c。
StringBuilder insert(int offset,String str)//在 offset 位置插入一个字符串并返回 this。
        StringBuilder delete(1 nt startindex,int endlndex)//
       String toString()//返回一个与构建器或缓冲器内容相同的字符串
```



---

# 4输入输出



> 格式化输出

```java
 double i1 = 11.111;
// 4个字符的宽度和小数点后两个字符的精度打印 x。
        System.out.printf("%4.2f",i1);
```

 pringf的转换符

![image-20201022234504804](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201022234504804.png)

printf的标志

![image-20201022234646948](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201022234646948.png)

> 文件输入输出
>
> ==如果文件名中包含反斜杠符号，就要记住在每个反斜杠之前再加一个额外的反斜杠： “ c:\\mydirectory\\myfile.txt ”== 
>
> 文件路径：
>
> - 当指定一个相对文件名时， 如 mytxt.txt 文件位于 Java 虚拟机启动路径的相对位置 ，
>
> -  如果在命令行方式下用下列命令启动程序： `java MyProject` 启动路径就是命令解释器的当前路径
>
> - 如果使用集成开发环境， 那么启动路
>
>   径将由 IDE 控制。 可以使用下面的调用方式找到路径的位置：
>
>   `String dir = System.getProperty("user.dir"):`
>
> - ==绝对路径， 例如：“ c:\\mydirectory\\myfile.txt ”== 

```java
//想对文件进行读取，就需要一个用 File 对象构造一个 Scanner 对象，
try {
            Scanner scanner = new Scanner(Paths.get("mytxt.txt"),"UTF-8");
        } catch (IOException e) {
            e.printStackTrace();
        }
```

```java
// scanner api
Scanner(File f)
构造一个从给定文件读取数据的 Scanner。 
Scanner(String data)
构造一个从给定字符串读取数据的 Scanner。
PrintWriter(String fileName)
构造一个将数据写入文件的 PrintWriter。文件名由参数指定。
static Path get(String pathname)
根据给定的路径名构造一个 Path。
```

