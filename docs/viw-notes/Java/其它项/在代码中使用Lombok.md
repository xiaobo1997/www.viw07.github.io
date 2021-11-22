



# Lombok



## 介绍

> Lombok能以简单的注解形式来简化java代码



## 使用

> 在maven项目中 添加依赖
>
> ```xml
>  <dependency>
>             <groupId>org.projectlombok</groupId>
>             <artifactId>lombok</artifactId>
>             <version>1.16.20</version>
>         </dependency>
> ```
>
> 或者自己引入jar包即可



### @Data

> @Data注解在类上，，会为类的所有属性自动生成setter/getter、equals、canEqual、hashCode、toString方法，如为final属性，则不会为该属性生成setter方法.如果需要不同权限修饰符可以使用AccessLevel.xxx

```java
//@Data= @Setter+@Getter+@EqualsAndHashCode+@NoArgsConstructor
@Data public class StudentEx {
    @Setter(AccessLevel.PRIVATE) 
    private final String name= "aa"; // final 没有set方法
    @Setter(AccessLevel.PACKAGE) private int age; //设置访问权限
    private double score=0.01;
    
     @ToString(includeFieldNames=true)// 生成所有属性的toString()方法
  @Data(staticConstructor="of") // 生成一个默认的静态方法 of()
  public static class Exercise<T> {
    private final String name;
    private final T value;
  }
```



### @Getter/@Setter

> 此注解在属性上，可以为相应的属性自动生成Getter/Setter方法

```java

public class GetterSetterExample {

  @Getter @Setter private int age = 10; // 生成 get  set 方法
  
  @Setter(AccessLevel.PROTECTED) private String name;// 生成 set  权限修饰符protected
```



### @ToString

> 生成所有属性的toString()方法
>
> 通过将`includeFieldNames`参数设为true，就能明确的输出toString()属性。

```java

@ToString(exclude="id") // 排除 id
public class ToStringExample {
  private static final int STATIC_VAR = 10;
  private String name;
  private Shape shape = new Square(5, 10);
  private String[] tags;
  private int id;
  
  public String getName() {
    return this.getName();
  }
  
  @ToString(callSuper=true, includeFieldNames=true)// tostring中 会调用父类 再加上该类
  public static class Square extends Shape {
    private final int width, height;
    
    public Square(int width, int height) {  
      this.width = width;
      this.height = height;
    }
  }
}
```

### @NonNull

> 该注解用在属性或构造器上， Lombok会生成一个非空的声明，可用于校验参数，能帮助避免空指针。

```java
public class NonNullExample extends Something {
  private String name;
  @NonNull private String addr;
  
  public NonNullExample(@NonNull Person person) {
    super("Hello");
    this.name = person.getName();
  }
}
```

### @Cleanup

> 帮助我们自动调用close()方法，

```java
public class CleanupExample {
  public static void main(String[] args) throws IOException {
      /*
       if (in != null) {
          in.close();
        }
      */
    @Cleanup InputStream in = new FileInputStream(args[0]);
       /*
       if (out != null) {
          out.close();
        }
      */
      
    @Cleanup OutputStream out = new FileOutputStream(args[1]);
    byte[] b = new byte[10000];
    while (true) {
      int r = in.read(b);
      if (r == -1) break;
      out.write(b, 0, r);
    }
  }
}
```



### @EqualsAndHashCode

> 使用所有非静态（non-static）和非瞬态（non-transient）属性来生成equals和hasCode，也能通过exclude注解来排除一些属性。

```java
@EqualsAndHashCode(exclude={"id", "shape"}) // 排除 
public class EqualsAndHashCodeExample {
  private transient int transientVar = 10;
  private String name;
  private double score;
  private Shape shape = new Square(5, 10);
  private String[] tags;
  private int id;
  
  public String getName() {
    return this.name;
  }
  
  @EqualsAndHashCode(callSuper=true) // 调用父类  默认false
  public static class Square extends Shape {
    private final int width, height;
    
    public Square(int width, int height) {
      this.width = width;
      this.height = height;
    }
  }
}
```



###  @NoArgsConstructor, @RequiredArgsConstructor and @AllArgsConstructor 

>  无参构造器、部分参数构造器、全参构造器。

```java
@RequiredArgsConstructor(staticName = "of") // 将标记为@NoNull的属性生成一个构造器
@AllArgsConstructor(access = AccessLevel.PROTECTED)
public class ConstructorExample<T> {
  private int x, y;
  @NonNull private T description;
  
  @NoArgsConstructor
  public static class NoArgsExample {
    @NonNull private String field;
  }
}
```

### @Builder

> 构造Builder模式的结构。通过内部类Builder()进行构建对象。

```java
//原始类
@Builder
public class TestEntity {
 
    private String name;
 
    private Integer age;
 
    private final String type = "person";
}
//反编译的类
public class TestEntity {
    private String name;
    private Integer age;
    private final String type = "person";
 
    @ConstructorProperties({"name", "age"})
    TestEntity(String name, Integer age) {
        this.name = name;
        this.age = age;
    }
 
    public static TestEntity.TestEntityBuilder builder() {
        return new TestEntity.TestEntityBuilder();
    }
 
    public static class TestEntityBuilder {
        private String name;
        private Integer age;
 
        TestEntityBuilder() {
        }
 
        public TestEntity.TestEntityBuilder name(String name) {
            this.name = name;
            return this;
        }
 
        public TestEntity.TestEntityBuilder age(Integer age) {
            this.age = age;
            return this;
        }
 
        public TestEntity build() {
            return new TestEntity(this.name, this.age);
        }
 
        public String toString() {
            return "TestEntity.TestEntityBuilder(name=" + this.name + ", age=" + this.age + ")";
        }
    }
}
// 

public  void test(){
    TestEntity testEntity = TestEntity.builder()
                    .name("java")
                    .age(18)
                    .build();
}
```



### @Value

> 如果变量不加@NonFinal ，@Value会给所有的弄成final的。



```java

@Value
public class TestEntity {
    @Setter(AccessLevel.PRIVATE)
    private String name;

```

```jav
//反编译的类
public final class TestEntity {
    private final String name;

 @ConstructorProperties({"name"})
    public TestEntity(String name) {
        this.name = name;
     
    }
 
    public String getName() {
        return this.name;
    }
```


### @Synchronized



```java
//原始类
public class TestEntity {
    private String name;
 
    private Integer age;
 
    private final String type = "person";
    @Synchronized
    public void write(){
        //do something
    }
}
//反编译的类
public class TestEntity {
    private final Object $lock = new Object[0];
    private String name;
    private Integer age;
    private final String type = "person";
 
    public TestEntity() {
    }
 
    public void write() {
        Object var1 = this.$lock;
        synchronized(this.$lock) {
            ;
        }
    }
}
```



## 原理

> 会发现在Lombok使用的过程中，只需要添加相应的注解，无需再为此写任何代码。自动生成的代码到底是如何产生的呢？
>
> 核心之处就是对于注解的解析上。JDK5引入了注解的同时，也提供了两种解析方式。
>
> - 运行时解析
>
> 运行时能够解析的注解，必须将@Retention设置为RUNTIME，这样就可以通过反射拿到该注解。java.lang,reflect反射包中提供了一个接口AnnotatedElement，该接口定义了获取注解信息的几个方法，Class、Constructor、Field、Method、Package等都实现了该接口，对反射熟悉的朋友应该都会很熟悉这种解析方式。
>
> - 编译时解析
>
> 编译时解析有两种机制，分别简单描述下：
>
> 1）Annotation Processing Tool
>
> apt自JDK5产生，JDK7已标记为过期，不推荐使用，JDK8中已彻底删除，自JDK6开始，可以使用Pluggable Annotation Processing API来替换它，apt被替换主要有2点原因：
>
> - api都在com.sun.mirror非标准包下
> - 没有集成到javac中，需要额外运行
>
> 2）Pluggable Annotation Processing API
>
> [JSR 269](https://jcp.org/en/jsr/detail?id=269)自JDK6加入，作为apt的替代方案，它解决了apt的两个问题，javac在执行的时候会调用实现了该API的程序，这样我们就可以对编译器做一些增强，这时javac执行的过程如下：
> ![这里写图片描述](http://img.blog.csdn.net/20160908130644281)
>
> Lombok本质上就是一个实现了“[JSR 269 API](https://www.jcp.org/en/jsr/detail?id=269)”的程序。在使用javac的过程中，它产生作用的具体流程如下：
>
> 1. javac对源代码进行分析，生成了一棵抽象语法树（AST）
> 2. 运行过程中调用实现了“JSR 269 API”的Lombok程序
> 3. 此时Lombok就对第一步骤得到的AST进行处理，找到@Data注解所在类对应的语法树（AST），然后修改该语法树（AST），增加getter和setter方法定义的相应树节点
> 4. javac使用修改后的抽象语法树（AST）生成字节码文件，即给class增加新的节点（代码块）

