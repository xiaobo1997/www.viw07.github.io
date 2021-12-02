[TOC]



# spring中的扩展处理器



> 在spring有两种处理器，一种是作用bean的处理器，BeanPostProcessor，一种是作用BeanFactory的BeanFactoryPostProcessor





## BeanPostProcessor



作用bean的后置处理器

- **SmartInstantiationAwareBeanPostProcessor 设置Bean的类型，构造函数**

- **InstantiationAwareBeanPostProcessor Bean实例化之前，属性赋值之前的处理器**

  - `postProcessBeforeInstantiation`：Bean实例化之前执行此方法，如果返回不为`null`，则替代当前准备实例化的Bean，Spring将不再自动实例化对象了。
    `postProcessAfterInstantiation`：如果返回 true，执行 `postProcessProperties`方法。
    `postProcessProperties`：设置实例化Bean需要的属性，调用 set 方法赋值，如果指定的属性名没有set方法，则报错。

- **MergedBeanDefinitionPostProcessor 再添加（合并）或修改Bean的定义信息**

  ```java
  /**
       * @param beanDefinition 当前正在创建Bean的定义信息,可以用它再添加或修改一些定义信息
       *                       beanDefinition.setLazyInit(true); 设置懒加载为 true
       */
      void postProcessMergedBeanDefinition(RootBeanDefinition beanDefinition, Class<?> beanType, String beanName);
  
      /**
       * 如果修改了当前正在创建的Bean的 name 会执行
       * @param beanName
       */
      default void resetBeanDefinition(String beanName) {
      }
  
  ```

  

- **BeanPostProcessor Bean初始化之后的处理器，所有后置处理器的顶级接口。可以修改Bean实例的信息，甚至修改Bean的引用指向。**

  ```java
  //在bean初始化方法执行之前，可以拦截返回对象代替spring当前的实例
  @Nullable
  	default Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
  		return bean;
  	}
  	
  //bean初始化最后一个方法 代替当前spring bean实例，aop在这里创建
  	@Nullable
  	default Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
  		return bean;
  	}
  
```
  
  







## BeanFactoryPostProcessor



### 流程和作用

```java
@FunctionalInterface
public interface BeanFactoryPostProcessor {

   /**
   是spring通过的对外的接口，通过这个接口，可以在ioc容器加载刷新了所有bean信息后
   在bean实例化之前，对bean的属性定义就行修改
   处理器是有先后顺序，通过实现order接口来实现
     */
   void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException;

}
```



在spring加载了任何实现了BeanFactoryPostProcess接口的bean配置，都会在beanFactory加载所有bean配置后执行postProcessorBeanFactory方法，

**postProcessorBeanFactory这个方法 可以在bean实例化之前，ioc加载bean定义之后 对bean的配置信息进行修改**

### 应用



只需要实现这个接口就可以了 

自定义实现一个后置处理器，可以修改配置信息

```java
package com.viw.processor;

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanDefinition;
import org.springframework.beans.factory.config.BeanDefinitionVisitor;
import org.springframework.beans.factory.config.BeanFactoryPostProcessor;
import org.springframework.beans.factory.config.ConfigurableListableBeanFactory;
import org.springframework.util.StringValueResolver;

import java.util.Set;

/**
 * @Author: xhb
 * @Date: 2020/12/24 22:12
 */
public class BeanFactoryProcess1 implements BeanFactoryPostProcessor {
    private Set<String> nameSet;
    @Override
    public void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException {
        //BeanDefinition nameSet = beanFactory.getBeanDefinition("nameSet");
        String[] names = beanFactory.getBeanDefinitionNames();
        for (String name : names) {
            BeanDefinition definition = beanFactory.getBeanDefinition(name);
            StringValueResolver resolver =str -> {
                    if ("xiaobo".equals(str)) return "aaa";
                    return str;
            };
            BeanDefinitionVisitor beanDefinitionVisitor = new BeanDefinitionVisitor(resolver);
            beanDefinitionVisitor.visitBeanDefinition(definition);
        }
    }
}
```



**自定义后置处理器可以用来干什么，处理敏感信息，**



激活注册：

**激活BeanFactoryPostProcessor：AbstractApplicationContext#invokeBeanFactoryPostProcessors 委派了PostProcessorRegistrationDelegate.invokeBeanFactoryPostProcessors(beanFactory, getBeanFactoryPostProcessors())**

- **`beanFactory` 是 `BeanDefinitionRegistry` 类型**：需要特殊处理
- **`beanFactory` 不是 `BeanDefinitionRegistry` 类型**：进行普通处理



### BeanDefinitionRegistryPostProcessor

继承了BeanFactoryPostProcessor

通过实现这个接口可以自定义的向容器中注册bean



```java
public class RegisBeanFactoryPro1 implements BeanDefinitionRegistryPostProcessor {
    @Override
    public void postProcessBeanDefinitionRegistry(BeanDefinitionRegistry registry) throws BeansException {
        RootBeanDefinition rootBeanDefinition = new RootBeanDefinition();
        rootBeanDefinition.setBeanClass(Person.class);
        registry.registerBeanDefinition("person",rootBeanDefinition);
    }
```

