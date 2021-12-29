

[TOC]



#  1.剖析类

## 1. 类



## 2.静态域和静态方法



> 被static 修饰的 域就是静态域

```java
/**
n个emp对象 共享一个 idComm域，也就是 10个emp对象 有10个id域 但是 共享一个 idComm域，他们都是有着
自己的一份 idComm拷贝
*/
class emp{
 private  static int idComm = 1;
	private int id;
}
```



> 静态方法：静态方法是一种不能向对象进行操作的方法
>
> 静态方法可以访问自身类中的静态域
>
> 使用静态方法的两种情况：
>
> - 1.一个方法不需要访问对象状态，参数都是显式参数提供
>
> - 2.一个方法只需要访问类的静态域 如
>
>   - ```java
>     static int getNext(){
>     	return nextId; // return static filed;
>     }
>     ```
>
> 静态方法：使用 静态工厂方法构造不同风格对象 如：
>
> - ```java
>   NumberFormat currencyFormatter = NumberFormat.getCurrencylnstanceO;
>   NumberFormat percentFormatter = NumberFormat.getPercentlnstance()；
>   double x = 0.1;
>   System.out.println(currencyFormatter.format(x)); // prints SO.10
>   System.out.println(percentFomatter.format(x)); // prints 10%
>   ```
>
> ==不需要构造对象调用静态方法；==



> main方法：也是一个静态方法；main不对任何对象操作，而是在启动程序时  静态的main创建程序需要的对象

```java
 public static void main(String[] args){
 
 }
```



## 方法参数(call by value)

> java世界中只是按值调用(call by value):==方法得到的是所有参数值的一个拷贝，特别是，方法不能修改传递给它的任何参数变量的内容。==
>



> 方法参数共有两种类型： 
>
> •基本数据类型（数字、布尔值）
>
> •对象引用。



> 一个方法不可能修改一个基本数据类型的参数，对值的参数修改没有保留下来

```java
// 
public static void a(int x){
 x = 3*x;
}
int i  = 1;
a(i);

```



![image-20201027130916649](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201027130916649.png)

> 对象引用作为参数：方法得到的是对象引用的拷贝，对象引用及其他的拷贝同时引用同一个对象。

```java
public static void funA(Emp e){
 e.salary(10);
}

// 提供员工的工资
aaa = new Emp(...);
funA(aaa);
//对 对象参数的修改保留下来
```



![image-20201027131429844](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201027131429844.png)



> ```java
> package java8.base1;
> 
> /**
>  * @Author: xiaobo
>  * @Date: 2020/10/28 13:15
>  */
> public class ObjectUse {
>     static class Emp {
>         String name;
> 
>         Emp(String name) {
>             this.name = name;
>         }
>     }
> 
>     static void swap(Emp a, Emp b) {
>         Emp temp = a;
>         a = b;
>         b = temp;
>     }
>     public static void main(String[] args) {
>         Emp a = new Emp("aaa");
>         System.out.println(a);// @1540e19d
>         Emp b = new Emp("bbb");
>         System.out.println(b);// @677327b6
>         swap(a, b);
>         System.out.println(a);// @1540e19d
>         System.out.println(b);// @677327b6
>     }
> }
> 
> ```

**总结：使用了swap之前和之后，a和b的对象引用没有变，是方法调用之前引用的对象地址，相当于 交换对象参数的结果没有保留下来，方法并没有改变存储在变量 a 和 b 中的对象引用**

方法参数的三种情况：

- ==一个方法不能修改一个基本数据类型的参数（即数值型或布尔型）。== 

- ==一个方法可以改变一个对象参数的状态。== 

- ==一个方法不能让对象参数引用一个新的对象==



## 3.成员变量和局部变量



> 根据变量的位置不同可以分为成员变量和局部变量
>
> - 成员变量：
>   - 1.带static 的类变量filed：从类被创建就存在，与类共存亡
>   - 2.不带static的 实例变量filed：从该类实例被创建才存在，与实例共存亡
> - 局部变量：
>   - 1.形参，方法签名上的
>   - 2.方法内局部变量
>   - 3.代码块局部变量
>



## 4.对象构造&构造器



### 隐式参数和显式参数

```
public class Emp{
	int id;
	public Emp(int id){
	this.id = id;
	}

public void add(int i){
	i +=1;
	id  = i;
}

}
```

> 在这里我们定义了一个类和add方法，然后实例化类 调用方法

```
Emp  e = new Emp(1);
e.add(1);
```

> 我们可以看到 add方法  有两个参数， 一个是add 前面的 Emp对象e, 一个是方法后面的数值 1, 这个1 也就是我们的==显式参数==， 对象e就是我们的==隐式参数==(方法调用的目标)， 
>
> 在方法中 我们也可以与关键字this来 表示隐式参数



```java
public void add(int i){
 i+=1;
 this.id = i;
}
```

> 并且用this 我们可以把 实例域 和局部变量 分别开来。





### 构造器和初始化

> **构造器**：说一下构造器，构造器的作用就是在创建对象定义对象的初始状态，当我们没有显式给构造器赋初值时，系统会赋上默认值，如果是基本数值类型 赋 0,boolean  赋 false。对象引用类型 赋null，如果不赋那么是很影响程序代码的可读性。
>
> **无参构造器**：在编写一个类时， 如果没有编写任何构造器，系统会默认提供一个无参构造器，那么默认值也就是系统默认赋的，
>
> **显式初始化**：通过重载类的构造器方法，可以采用多种形式设置类的实例域的初始状态。确保不管怎
>
> 样调用构造器，每个实例域都可以被设置为一个有意义的初值。在执行构造器之前就会先进行赋值操作



>先说一下重载：
>
>- 在java世界中，允许任何方法重载，而构造器也是可以重载的，相同的方法，不同的参数就构成了重载
>- 重载解析 ：编译器会通过各个方法给出的方法类型 和调用方法所使用的类型就是匹配，然后选择特定的方法来 执行，如果没有匹配成功，那么就会编译时错误。



> 我们对构造器的参数名 起的名字要有意义，这样别人在阅读你的代码才能快速知道你想表达的是什么意思，要不然像这种，可读性就很差
>
> ```java
> public Emp(String a){
>  name = a;
> }
> ```
>
> 我们通过 隐式参数的形式访问实例域 如 this.name , this 表示隐式参数， 也就是要构造对象，也就是我们的目标对象
>
> ```java
> // 参数变量用同样的名字将实例域屏蔽起来
> public Emp(String name){
>  name = name;
> }
> 
> // ==
> public Emp(String name){
>  this.name = name
> }
> ```



>如果我们需要在一个构造器中调用另外构造器，比如在一个构造器中我们要调用一个公共的构造器，就可以减少我们的代码，我们在构造器的第一个语句中 用this(...)来 调用
>
>```java
>public Emp(int id){
> this(name,id); // call Emp(String,age)
> id ++;
>}
>```



### 初始化块



```java
public class Emp{
	private static int nextId;
	private int id;
	// 初始化块
	{
		int =nextId;
		nextId++;
	}
	public Emp(int i){
	this.nextId = i;
	}
	public Emp(...){
	...
	}
}
```

> 如上面，我们初始化块先运行，然后再执行构造器主体，相当于 我们使用哪一个构造器，我们的id域都被初始化





```
static
{
	nextId = 2; 
}
```

> 如上，静态初始化块，使用 静态初始化块 来 初始化 静态域，在类第一次加载时，就会进行静态域的初始化，



## 5.接口和抽象





## 6.类，超类，子类







### 继承



> 继承

```java
public class Emp{
 xxxx
}
```

```
public class Manager extends Emp{
xxxxx
} 
```

> 我们通过关键字 `extends` 来表示正在构造的类 继承 一个已存在的类



> 我们把公共的方法放在父类，然后特殊的方法放在子类





### 覆盖



> **覆盖方法：**
>
> 父类的方法不一定子类也适用，所以我们需要在子类中提供一个方法来 覆盖 （override）父类的这个方法
>
> 我们可以所以关键字 `super`来表示我们要调用的是 父类的方法，
>
> `super` 表示编译器调用超类方法的特殊关键字
>
> ```java
> // 如 我们需要 得到 idA(父类才有) +idB(子类才有)
> public int getId(){
> int idA = super.getId(); // 调用父类的方法 得到 idA
> return idA+idB
> }
> ```
>
> 



### 子类构造器



```java
public class A{
	private	String name;
	public A(String name){
	this.name = name;
	}
}
// ===========
public class B extends A{
	private	int id;
	public B(String name,int id){
	super(String name);
	this.id = id;
	}
}
```

> 在B中构造器不能访问A的私有域，所以我们通过A的构造器对A私有域进行初始化，因为我们可以通过super 来调用父类的构造器，所以可以实现
>
> ==使用super 调用构造器的语句必须是子类构造器的第一条语句。==



### this和super的两个用途

this:

- 1.引用隐式参数
- 2.调用该类其他构造器

super:

- 1.调用超类的方法
- 2.调用父类构造器



### 超类Object



> Object是所有没有指明超类的类的超类
>
> 在java世界中，每一个类的都是由object扩展来的



> equals方法：
>
> Object 类中的 equals 方法用于检测一个对象是否等于另外一个对象。
>
> 在 Object 类中，这个方法将判断两个对象是否具有相同的引用。
>
> 事实上 很多类都覆写了 equals方法

```java
Emp e1 =new Emp();
Emp e2 = new Emp();
Manager m1 = new Manager();
e1.equals(e2);
```



> hashcode()方法：每个对象都有一个默认的散列码，其值为对象的存储地址。

```java
String str = "a";
str.hashCode();
```



## 对象包装和拆箱装箱

> 在java世界中，因为不能把8个基本数据类型当成对象处理，所以通过对每一个基本类型提供包装类，来 处理基本数据类型

| 基本数据类型 | 包装类    | 区别                                                         |
| ------------ | --------- | ------------------------------------------------------------ |
| float        | Float     |                                                              |
| double       | Double    |                                                              |
| char         | Chararter |                                                              |
| boolean      | Boolean   |                                                              |
| int          | Integer   | Integer必须实例化才能使用，而int 不需要，Integet是对象引用，当new Integer生成引用该对象，int是直接存储， int默认值是0，Integer是对象引用默认值是null |
| long         | Long      |                                                              |
| byte         | Byte      |                                                              |
| short        | Short     |                                                              |



> Integer:
>
> ```java
> // 两个构造方法
> Integer i1 = new Integer(10);
> Integer i2 = new Integer("10");
> // api
> i1.byteValue() // 以byte类型返回该值
> i1.doubleValue() // ..
> i1.equals(i2);// true ; Integer 覆写了Object 的equals方法，看源码 我们可以知道，如果传入的参数类型是Integer 就比较他们的值，值相等就返回ture  而 = 比较的是  原
> i1 == i2; //false ； 
> valueOf(String str) //
> toString() // 返回表示该值的string对象
> parseInt(String str) // 返回包含由str指定字符串中的数字的等价整数值  
>     int i = Integer.parseInt(String str);
> ```
>
> 





> Boolean: Boolean将boolean包装在对象中，只包括一个类型为boolean的字段，
>
> ```java
> //构造方法
> Boolean(boolean value)
> Boolean(String str)
> // api
>     booleanValue() //以boolean 值 返回这个对象的值
>     equals(Object obj) //覆写了Object 的equals方法
>     parseBoolean(String s) // 返回一个boolean值。 将字符串解析成boolean
>     toString() // 
>     valueOf(String s) //看源代码，先调用parseBoolean() 如果不为空并且忽略大小写 = true 就返回true
> ```
>
> 



> Byte:  Byte将byte包装在对象中，只包括一个类型为byte的字段，
>
> ```java
> // 构造方法
> public Byte(byte value)
> public Byte(String s) // 要用数值类型string作为变量 
> ```
>
> ![image-20201029233605008](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201029233605008.png)
>
> ![image-20201029233634607](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201029233634607.png)



> Character:
>
> ```java
> // 构造方法 构造函数必须是一个char类型的数据
> Character(char value)
>     
>     
> ```
>
> ![image-20201029234821281](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201029234821281.png)
>
> ![image-20201029234833520](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201029234833520.png)



> Double和Float: 该类提供了把double转string,string 转double
>
> ```java
> //构造方法
> Double(double value) //
> Double(String str) // 构造一个新分配的
> ```
>
> ![image-20201030001946050](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201030001946050.png)



> Number: 抽象类是 BigDecimal  , BigInteger , Byte, Double, Float,Integer,Long,Short的父类
>
> ```
> // Number的子类 必须提供将表示的数值转 byte int long float short double
> ```
>
> ![image-20201030002135066](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201030002135066.png)





## 定义参数数量可变的方法

> ```java
> public static void main(String[] args)
>     //
> public static void main(String... args)
> ```
>
> 像main方法就是 可变参数的方法，
>
> 现在java可以用可变参数的数量调用的方法
>
> ```java
>  static int add(int... value){
>         int a= 0;
>         for(int i : value){
>             a+=i;
>         }
>         return  a;
>     }
> 
> // 调用
> add(1,3,4); //return 8
> ```
>
> 







## 7. 内部类



> 内部类( `inner class` )一句话就是 在定义在一个已知类中的类，也就是类中类，有些也称之为嵌套类

```java
public class A{
	static class B{
	}
}
```



> 内部类又分为：
>
> - 1.静态内部类
>
>   ```java
>   /**
>   1. 静态内部类可以访问外部类所有的静态变量和方法，即使是 private 的也一样。
>   2. 静态内部类和一般类一致，可以定义静态变量、方法，构造方法等。
>   3. 其它类使用静态内部类需要使用“外部类.静态内部类”方式，如下所示：Out.Inner inner = 
>   new Out.Inner();inner.print();
>   */
>   public class A{
>   	private int i;
>   	public static class B{
>   		public void printInfo(){
>   		System.oit.println(a);
>   		}
>   	}
>   }
>   ```
>
> - 2.成员内部类
>
>   ```java
>   /**
>   定义在类内部的非静态类，就是成员内部类。成员内部类不能定义静态方法和变量（final 修饰的
>   除外）。这是因为成员内部类是非静态的，类初始化的时候先初始化静态成员，如果允许成员内
>   部类定义静态变量，那么成员内部类的静态变量初始化顺序是有歧义的。
>   */
>   
>   public class A{
>   	public class B{
>   	
>   	}
>   }
>   ```
>
> - 3.**局部内部类（定义在方法中的类）**
>
>   ```java
>   public class A{
>   	public void fun(){
>   		class B{
>   		  public void print(){ ....}
>   		}
>   	}
>   
>   }
>   ```
>
> - 4，==**匿名内部类（要继承一个父类或者实现一个接口、直接使用new来生成一个对象的引用）**== 。当创建一个匿名内部类时，必须实现接口或者抽象父类的所有方法，必要时还可以重写父类的方法
>
>   ```java
> /**
>   匿名内部类我们必须要继承一个父类或者实现一个接口，当然也仅能只继承一个父类或者实现一
>   个接口。同时它也是没有 class 关键字，这是因为匿名内部类是直接使用 new 来生成一个对象的引
>   用。
>   匿名内部类不会重复使用，也就是说 创建完使用一次就消失
>   两条规则： 1. 匿名内部类不能是抽象类 2.不能定义构造器
>   */
>   
>    // ======================= 实现接口
> /**
>    * @Author: xiaobo
>    * @Date: 2020/11/4 12:39
>    */
>   public interface CatA {
>       public  String getName();
>   }
>   
>   
>   /**
>    * @Author: xiaobo
>    * @Date: 2020/11/4 12:35
>    */
>   public class InnerClass4 {
>       public void test(CatA c) {
>           System.out.println("名字是"+c.getName());
>       }
>       public static void main(String[] args) {
>           InnerClass4 innerClass4 = new InnerClass4();
>           innerClass4.test( new CatA(){
>               public String getName(){
>                   return  "777";
>               }
>           } );
>       }
>   }
>   
>   // ========继承父类===============
>   /**
>    * @Author: xiaobo
>    * @Date: 2020/11/4 12:50
>    */
>   public abstract class CatB {
>       public String name;
>       public abstract  int getI();
>       public CatB(String name){
>           this.name = name;
>       }
>       public  CatB(){
>       }
>   
>       public String getName() {
>           return name;
>       }
>   
>       public void setName(String name) {
>           this.name = name;
>       }
>   }
>   
>   // ========
>   
>   package java8.base2;
>   
>   /**
>    * @Author: xiaobo
>    * @Date: 2020/11/4 12:51
>    */
>   public class InnerClassB {
>       public void test(CatB b){
>           System.out.println("name"+b.getName()+"i="+b.getI());
>       }
>   
>       public static void main(String[] args) {
>           InnerClassB innerClassB = new InnerClassB();
>           innerClassB.test(new CatB("调用有参构造器") {
>               @Override
>               public int getI() {
>                   return 1;
>               }
>           });
>           // 无参构造 创建匿名内部类
>           CatB b = new CatB() {
>               //重写父类的方法
>               @Override
>               public int getI() {
>                   return 2;
>               }
>               //重写 父类实例方法
>               public String getName(){
>                   return "重写了父类的实例方法";
>               }
>           };
>           innerClassB.test(b);
>       }
>   }
>   
>   
>   ```
>   
>   



> 那么为什么要有内部类呢，
>
> - 1.内部类方法可以访问该类定义所在的作用域中的数据， 包括私有的数据。
>
> - 2.内部类可以对同一个包中的其他类隐藏起来。
>
> - 3.要定义一个回调函数且不想编写大量代码时，使用匿名 （anonymous) 内部类比较
>
>   便捷。
>   
> - 4.清晰的代码结构(但是现在可以用 ==lambda== )

