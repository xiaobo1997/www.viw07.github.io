[TOC]



# 1.深入浅出认识基础组件和体系



## spring体系结构



- spring core:spring的核心模块，提供ioc和di
- spring context:spring 上下文容器，
- spring web:spring 提供的web开发支持模块
- spring mvc : spring 对 web mvc开发思想的实现
- spring dao :提供了dao抽象层的模块
- spring orm ：spring 支持与 主流 Orm框架的整合模块
- spring aop：spring 提供支持面向切面编程的模块



## 基础组件



### configuration



> 使用注解获取bean

```java
public class Test {
    public static void main(String[] args) {
        //读取xml
       // ApplicationContext classPathXmlApplicationContext = new ClassPathXmlApplicationContext("beans.xml");
      //  Object person = classPathXmlApplicationContext.getBean("person");
        
        // 使用注解
        AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(BeanConfig.class);
        //从容器中获取bean  默认方法名是bean id也可以设置这个Bean的id 在@Bean这里设置
        Object person = applicationContext.getBean("person");
        System.out.println((Person)person);
    }
}
```

```java

@Configuration
public class BeanConfig {

    @Bean
    public Person person(){
        return new Person("xiaobo");
    }
}
```

### componentscan

> 这个主要是容器扫描用的

```

```

