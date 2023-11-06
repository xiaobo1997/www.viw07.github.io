[TOC]



# aop原理



> ioc为我们提供了我要谁 你就给我谁，你什么时候要，我就什么时候给，而aop提供了更多的横向扩展，比如日志，缓存



aop代理主要是方式：

1. 静态代理  AspectJ：aspectJ是静态代理的增强，采用的是编译时生成aop代理类
2. 动态代理 Spring AOP：动态代理，运行时生成代理类，在内存中临时为方法生成一个aop对象，这个aop对象包括了目标对象的全部方法，然后在特殊切点做处理，并回调原对象的方法，



aop动态代理的主要实现方式是JDK动态代理和 cglib



## jdk动态代理

> jdk动态代理是通过==反射==来接收被代理的类，并且要求被代理的类必须实现一个接口。JDK动态代理的核心是InvocationHandler接口和Proxy类。



步骤：

1. 为目标类定义一个接口A和接口方法a，

   ```java
   /**
    * 目标类
    */
   public  implements  JdkInterface {
     
       public void add();
       
   }
   ```
   
2. 目标类T去实现这个接口 并重写方法a

   ```java
   /**
    * 目标类
    */
   public class JdkClass implements  JdkInterface {
       @Override
       public void add() {
           System.out.println("目标类的add方法");
       }
   }
   ```

   

3. 编写一个实现InvocationHandler接口的具体实现类，并重写里面的invoke方法

   ```java
   package com.viw.aop;
   
   import java.lang.reflect.InvocationHandler;
   import java.lang.reflect.Method;
   
   /**
    *  * @Author: xhb
    *  * @Date: 2020/12/22 21:12
    *
    */
   public class MyInvocationHandler implements InvocationHandler {
       private Object object;
   
       public  MyInvocationHandler(Object o){
           this.object = o;
       }
   
       @Override
       public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
           System.out.println("before");
           Object invoke = method.invoke(object, args);
           System.out.println("after");
           return invoke;
       }
   }
   
   ```

4. 调用

   ```java
   package com.viw.test;
   
   import com.viw.aop.JdkClass;
   import com.viw.aop.JdkInterface;
   import com.viw.aop.MyInvocationHandler;
   
   import java.lang.reflect.Proxy;
   
   /**
    * @Author: xhb
    * @Date: 2020/12/22 21:15
    */
   public class JdkProxy {
       public static void main(String[] args) {
           JdkClass aClass = new JdkClass();
           MyInvocationHandler handler = new MyInvocationHandler(aClass);
           // Proxy为InvocationHandler实现类动态的创建一个符合接口的代理实例
           // o就是我们的目标类的增强类
           JdkInterface o =(JdkInterface) Proxy.newProxyInstance(aClass.getClass().getClassLoader(),aClass.getClass().getInterfaces(),handler);
           o.add();
           System.out.println("==="+o.getClass());
       }
   }
   
   // console
   
   before
   目标类的add方法
   after
   ===class com.sun.proxy.$Proxy0
   ```




**JDK代理动态代理的核心是InvocationHandle接口和Proxy类**





## cglib

> 如果目标类没有实现接口，也就是说不能jdk动态代理,那么Spring AOP会选择使用CGLIB来动态代理目标类。CGLIB（Code Generation Library），是一个代码生成的类库，可以在运行时动态的生成某个类的子类（通过==修改字节码==来实现代理）。 注意，CGLIB是通过==继承==的方式做的动态代理，因此如果某个类被标记为final，那么它是无法使用CGLIB做动态代理的。 jdk和cglib动态代理来共同实现我们的aop面向切面的功能。





步骤：

1. 定义一个目标类

   ```java
   package com.viw.cglib;
   
   /**
    * @Author: xhb
    * @Date: 2020/12/22 21:32
    * 定义一个被代理的目标类
    */
   public class Base {
       void add(){
           System.out.println("target class method");
       }
   
   }
   
   ```

   

2. 定义CglibProxy类，实现MethodInterceptor接口，实现intercept方法。该代理的目的也是在add方法前后加入了自定义的切面逻辑，目标类add方法执行语句为proxy.invokeSuper(object, args)

   ```java
   package com.viw.cglib;
   
   import org.springframework.cglib.proxy.MethodInterceptor;
   import org.springframework.cglib.proxy.MethodProxy;
   
   import java.lang.reflect.Method;
   
   /**
    * @Author: xhb
    * @Date: 2020/12/22 21:34
    */
   public class CglibProxy implements MethodInterceptor {
       @Override
       public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
           System.out.println("before==== 加入切名逻辑");
           methodProxy.invokeSuper(o,objects);
           System.out.println("after=====加入切名逻辑");
           return null;
       }
   }
   
   ```

3. 测试

   ```java
   package com.viw.cglib;
   
   import org.springframework.cglib.proxy.Enhancer;
   
   /**
    * @Author: xhb
    * @Date: 2020/12/22 21:36
    */
   public class CglibTest {
       public static void main(String[] args) {
           CglibProxy proxy = new CglibProxy();
           Enhancer enhancer = new Enhancer();
           enhancer.setSuperclass(Base.class);
           // 回调方法的参数为代理类对象CglibProxy,增强目标类调用的是代理类对象中的intercept方法
           enhancer.setCallback(proxy);
           //o现在是已经增强的目标类
           Base o = (Base)enhancer.create();
           o.add();
           System.out.println(o.getClass());
   
           /**
            * 看增强类的父类是什么
            */
           Class<? extends Base> aClass = o.getClass();
           System.out.println("增强类的父类"+aClass.getSuperclass().getName());
   
           /**
            * 原目标类
            */
           Base base = new Base();
           System.out.println("原类"+base.getClass().getSuperclass().getName());
       }
   }
   
   //console
   
   before==== 加入切名逻辑
   target class method
   after=====加入切名逻辑
   class com.viw.cglib.Base$$EnhancerByCGLIB$$de65f732
   增强类的父类com.viw.cglib.Base
   原类java.lang.Object
   
   Process finished with exit code 0
   
   ```

**代理增强类相当于是目标类的子类，但是在目标类原有基础上，动态增加了很多方法，**