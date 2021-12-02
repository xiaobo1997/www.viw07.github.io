[TOC]



# aop



## 入门类AnnotationAwareAspectJAutoProxyCreator



> 通过 标签 `<aop:aspectj-autoproxy/>` 的配置，就看他使用aop功能
>
> 可以让Spring自动完成对AnnotationAwareAspectJAutoProxyCreator类的注册，从而实现AOP的功能。

## AnnotationAwareAspectJAutoProxyCreator的注册



> ![image-20201220234904565](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201220234904565.png)
>
> spring把xmls 都解析错了 解析器
>
> ![image-20201220235003755](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201220235003755.png)



**在Spring中，所有的解析器都是对BeanDefinitionParser接口的同一实现：**



### AopNamespaceHandler类



```java
public class AopNamespaceHandler extends NamespaceHandlerSupport {

	/**
	 对aspectj-autoproxy也就是AnnotationAwareAspectJAutoProxyCreator进行了注册 */
	@Override
	public void init() {
		// In 2.0 XSD as well as in 2.5+ XSDs
		registerBeanDefinitionParser("config", new ConfigBeanDefinitionParser());
		registerBeanDefinitionParser("aspectj-autoproxy", new AspectJAutoProxyBeanDefinitionParser());
		registerBeanDefinitionDecorator("scoped-proxy", new ScopedProxyBeanDefinitionDecorator());

		// Only in 2.0 XSD: moved to context namespace in 2.5+
		registerBeanDefinitionParser("spring-configured", new SpringConfiguredBeanDefinitionParser());
	}

}
```



### AspectJAutoProxyBeanDefinitionParser



```java
@Override
@Nullable
public BeanDefinition parse(Element element, ParserContext parserContext) {
   AopNamespaceUtils.registerAspectJAnnotationAutoProxyCreatorIfNecessary(parserContext, element);
   extendBeanDefinition(element, parserContext);
   return null;
}
```



### AopNamspaceUtils



```java
public static void registerAspectJAnnotationAutoProxyCreatorIfNecessary(ParserContext parserContext, Element sourceElement) {
        //把应用了@Aspect注解修饰的bean注册成BeanDefinition
        BeanDefinition beanDefinition = AopConfigUtils.registerAspectJAnnotationAutoProxyCreatorIfNecessary(parserContext.getRegistry(), parserContext.extractSource(sourceElement));
        //处理proxy-target-class和expose-proxy属性
        useClassProxyingIfNecessary(parserContext.getRegistry(), sourceElement);
        //注册组件并通知解析器
        registerComponentIfNecessary(beanDefinition, parserContext);
    }
```

### AopConfigUtils 

```java
// 获取AnnotationAwareAspectJAutoProxyCreator的BeanDefinition，
//然后根据优先级来装配这个BeanDefinition。
// 获取到了AnnotationAwareAspectJAutoProxyCreator之后，
//接下来就要将配置信息和BeanDefinition一起注册到SpringIOC中。
@Nullable
	private static BeanDefinition registerOrEscalateApcAsRequired(
			Class<?> cls, BeanDefinitionRegistry registry, @Nullable Object source) {

		Assert.notNull(registry, "BeanDefinitionRegistry must not be null");

		if (registry.containsBeanDefinition(AUTO_PROXY_CREATOR_BEAN_NAME)) {
			BeanDefinition apcDefinition = registry.getBeanDefinition(AUTO_PROXY_CREATOR_BEAN_NAME);
			if (!cls.getName().equals(apcDefinition.getBeanClassName())) {
				int currentPriority = findPriorityForClass(apcDefinition.getBeanClassName());
				int requiredPriority = findPriorityForClass(cls);
				if (currentPriority < requiredPriority) {
					apcDefinition.setBeanClassName(cls.getName());
				}
			}
			return null;
		}

		RootBeanDefinition beanDefinition = new RootBeanDefinition(cls);
		beanDefinition.setSource(source);
		beanDefinition.getPropertyValues().add("order", Ordered.HIGHEST_PRECEDENCE);
		beanDefinition.setRole(BeanDefinition.ROLE_INFRASTRUCTURE);
		registry.registerBeanDefinition(AUTO_PROXY_CREATOR_BEAN_NAME, beanDefinition);
		return beanDefinition;
	}




// 如果proxy-target-class为true，则走该方法的逻辑
	public static void forceAutoProxyCreatorToUseClassProxying(BeanDefinitionRegistry registry) {
		if (registry.containsBeanDefinition(AUTO_PROXY_CREATOR_BEAN_NAME)) {
			BeanDefinition definition = registry.getBeanDefinition(AUTO_PROXY_CREATOR_BEAN_NAME);
			definition.getPropertyValues().add("proxyTargetClass", Boolean.TRUE);
		}
	}
//如果expose-proxy为true，则走该方法的逻辑
	public static void forceAutoProxyCreatorToExposeProxy(BeanDefinitionRegistry registry) {
		if (registry.containsBeanDefinition(AUTO_PROXY_CREATOR_BEAN_NAME)) {
			BeanDefinition definition = registry.getBeanDefinition(AUTO_PROXY_CREATOR_BEAN_NAME);
			definition.getPropertyValues().add("exposeProxy", Boolean.TRUE);
		}
	}
```

> proxy-target-class的作用：
>
> 1.使用CGLIB,配置如下属性使用 CGLIB,  `<aop:config proxy-target-class=“true”/>`
>
> 2.使用CGLIB代理和@AspectJ自动代理支持，配置如下配置即可实现<aop:aspectj-autoproxy proxy-target-class=“true”/>
>
> expose-proxy:
>
> 在内部调用方法时无法调用切面逻辑的一种强制通知

