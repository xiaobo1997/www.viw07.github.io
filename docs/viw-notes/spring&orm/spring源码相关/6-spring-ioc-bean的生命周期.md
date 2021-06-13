[TOC]



# bean的生命周期



## 流程图



![image-20201220200533435](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201220200533435.png)



- 1.实例化，ioc容器根据配置实例化bean
- 2.属性赋值，这一步主要是di为bean设置相关依赖和属性值
- 3.初始化，
- 4.销毁，



## 主要的流程类



### AbstractAutowireCapableBeanFactory类

主要是里面的doCreateBean方法



```java

protected Object doCreateBean(final String beanName, final RootBeanDefinition mbd, final @Nullable Object[] args)
    throws BeanCreationException {

    // 1. 实例化
    BeanWrapper instanceWrapper = null;
    if (instanceWrapper == null) {
        instanceWrapper = createBeanInstance(beanName, mbd, args);
    }
    
    Object exposedObject = bean;
    try {
        // 2. 属性赋值
        populateBean(beanName, mbd, instanceWrapper);
        // 3. 初始化
        exposedObject = initializeBean(beanName, exposedObject, mbd);
    }

    // 4. 销毁-注册回调接口
    try {
        registerDisposableBeanIfNecessary(beanName, bean, mbd);
    }

    return exposedObject;
}

```



### 初始化



```java
// AbstractAutowireCapableBeanFactory.java
protected Object initializeBean(final String beanName, final Object bean, @Nullable RootBeanDefinition mbd) {
    // 3. 检查 Aware 相关接口并设置相关依赖
    //如果bean实现了BeanNameAware接口，工厂传递bean id调用setBeanName
    //如果bean实现了BeanFactoryAware接口，工厂提供传递自身的实例调用
    // setBeanFactory()方法
    if (System.getSecurityManager() != null) {
        AccessController.doPrivileged((PrivilegedAction<Object>) () -> {
            invokeAwareMethods(beanName, bean);
            return null;
        }, getAccessControlContext());
    }
    else {
        invokeAwareMethods(beanName, bean);
    }

    // 4. BeanPostProcessor 前置处理
    // 如果存在与 bean 关联的任何 BeanPostProcessors，
    //则调用 preProcessBeforeInitialization() 方法。
    Object wrappedBean = bean;
    if (mbd == null || !mbd.isSynthetic()) {
        wrappedBean = applyBeanPostProcessorsBeforeInitialization(wrappedBean, beanName);
    }

    // 5. 若实现 InitializingBean 接口，调用 afterPropertiesSet() 方法
    // 6. 若配置自定义的 init-method方法，则执行
    try {
        invokeInitMethods(beanName, wrappedBean, mbd);
    }
    catch (Throwable ex) {
        throw new BeanCreationException(
            (mbd != null ? mbd.getResourceDescription() : null),
            beanName, "Invocation of init method failed", ex);
    }
    // 7. BeanPostProceesor 后置处理
    // 如果存在与 bean 关联的任何 BeanPostProcessors，
    //则将调用 postProcessAfterInitialization() 方法。
    if (mbd == null || !mbd.isSynthetic()) {
        wrappedBean = applyBeanPostProcessorsAfterInitialization(wrappedBean, beanName);
    }

    return wrappedBean;
}

```



#### Aware接口



如果spring检测到了bean实现了Aware接口，会为其依赖注入。

bean实现了Aware接口，可以在bean中获取相关资源，比如BeanNameAware接口获取到bean在ioc容器中的id等等，让bean有感知的获取自身容器资源属性



spring提供的aware接口 主要是(针对BeanFactory类型的容器)

1. BeanNameAware 注入当前bean对应的beanName
2. BeanClassLoaderAware：注入到当前的bean的类加载器
3. BeanFacroryAware:注入当前BeanFactoy容器的引用

```java
// AbstractAutowireCapableBeanFactory.java
private void invokeAwareMethods(final String beanName, final Object bean) {
    if (bean instanceof Aware) {
        if (bean instanceof BeanNameAware) {
            ((BeanNameAware) bean).setBeanName(beanName);
        }
        if (bean instanceof BeanClassLoaderAware) {
            ((BeanClassLoaderAware) bean).setBeanClassLoader(bcl);
            
        }
        if (bean instanceof BeanFactoryAware) {
            ((BeanFactoryAware) bean).setBeanFactory(AbstractAutowireCapableBeanFactory.this);
        }
    }
}

```



（针对ApplicationContext类型的容器）

这种类型的容器的注入主要是提供处理器（BeanPostProcessor）

1. EnvironmentAware:注入 Enviroment，一般用于获取配置属性
2. EmbeddedValueResolverAware:主要是spring el解析器，参数解析
3. ApplicationContextAware（ResourceLoader、ApplicationEventPublisherAware、MessageSourceAware）：注入 ApplicationContext 容器本身。

```java
// ApplicationContextAwareProcessor.java
private void invokeAwareInterfaces(Object bean) {
    if (bean instanceof EnvironmentAware) {
        ((EnvironmentAware)bean).setEnvironment(this.applicationContext.getEnvironment());
    }

    if (bean instanceof EmbeddedValueResolverAware) {
        ((EmbeddedValueResolverAware)bean).setEmbeddedValueResolver(this.embeddedValueResolver);
    }

    if (bean instanceof ResourceLoaderAware) {
        ((ResourceLoaderAware)bean).setResourceLoader(this.applicationContext);
    }

    if (bean instanceof ApplicationEventPublisherAware) {
        ((ApplicationEventPublisherAware)bean).setApplicationEventPublisher(this.applicationContext);
    }

    if (bean instanceof MessageSourceAware) {
        ((MessageSourceAware)bean).setMessageSource(this.applicationContext);
    }

    if (bean instanceof ApplicationContextAware) {
        ((ApplicationContextAware)bean).setApplicationContext(this.applicationContext);
    }

}

```



#### BeanPostProcessor接口



BeanPostProcessor为修改bean提供了扩展，可以作用容器所有的bean

```java
public interface BeanPostProcessor {

	// 初始化前置处理
	default Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
		return bean;
	}

	// 初始化后置处理
	default Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
		return bean;
	}

}

```

场景：

对于标记接口的实现类，进行自定义处理

为当前对象提供代理实现

```java
// AbstractAutoProxyCreator.java
public Object postProcessBeforeInstantiation(Class<?> beanClass, String beanName) {
    TargetSource targetSource = getCustomTargetSource(beanClass, beanName);
    if (targetSource != null) {
        if (StringUtils.hasLength(beanName)) {
            this.targetSourcedBeans.add(beanName);
        }
        Object[] specificInterceptors = getAdvicesAndAdvisorsForBean(beanClass, beanName, targetSource);
        Object proxy = createProxy(beanClass, beanName, specificInterceptors, targetSource);
        this.proxyTypes.put(cacheKey, proxy.getClass());
        // 返回代理类
        return proxy;
    }

    return null;
}

```



#### InitializingBean 和init -method

这两个都是为初始化bean提供的扩展



接口**InitializingBean** 



```
public interface InitializingBean {
	void afterPropertiesSet() throws Exception;
}

```



在 afterPropertiesSet() 方法写初始化逻辑。

指定 init-method 方法，指定初始化方法：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="demo" class="com.xxx.Demo" init-method="init()"/>
    
</beans>

```



### 销毁



```java
// DisposableBeanAdapter.java
public void destroy() {
    // 9. 若实现 DisposableBean 接口，则执行 destory()方法
    if (this.invokeDisposableBean) {
        try {
            if (System.getSecurityManager() != null) {
                AccessController.doPrivileged((PrivilegedExceptionAction<Object>) () -> {
                    ((DisposableBean) this.bean).destroy();
                    return null;
                }, this.acc);
            }
            else {
                ((DisposableBean) this.bean).destroy();
            }
        }
    }
    
	// 10. 若配置自定义的 destory-method 方法，则执行
    if (this.destroyMethod != null) {
        invokeCustomDestroyMethod(this.destroyMethod);
    }
    else if (this.destroyMethodName != null) {
        Method methodToInvoke = determineDestroyMethod(this.destroyMethodName);
        if (methodToInvoke != null) {
            invokeCustomDestroyMethod(ClassUtils.getInterfaceMethodIfPossible(methodToInvoke));
        }
    }
}

```

