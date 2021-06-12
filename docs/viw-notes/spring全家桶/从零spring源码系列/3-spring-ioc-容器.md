[TOC]



# 1.ioc-写在前面



## 从哪些方面入手ioc容器



这是我第一次看spring源码，然后看的我有点晕头转向，一开始我是看视频，然后我是带着我的问题去看，比如说，spring ioc底层是怎么样的，他是怎么解析的，他是怎么准确给我们提供对象，他的factorybean 是怎么样的，他是怎么样生成我们需要的对象的，生成对象的流程，就是加载bean的流程，怎么获取bean，怎么加载bean，怎么定位，怎么解析，怎么注册到工厂中的，aop可以给我们带来什么，spring中的动态代理是怎么样的。。我的问题太多了，

所有我先从ioc 容器开始，从最简单的xml配置开始，一步一步解析，带着问题去看代码，然后百度，然后记下来，遇到不明白的就在下面名词解释，然后最后参考别人画时序图

这样的一个流程下来，我应该能理解spring ioc源码的大概流程，然后就是这样去搞定aop，



**写在前面：**

​		ioc容器：

- 怎么定位资源的BeanDefinitoin
- 这么解析bean成spring特殊的数据结构BeanDefinition
- BeanDefinition怎么注册进ioc 容器中的
- 依赖注入
- BeanPostProcessor
- spring BeanFactory
- 获取bean 加载bean





## ioc容器的原理

> ioc就是一个对象大工厂，也可以说 spring 是一个管理对象的map，
>
> 反射是框架的灵魂，因为要通过它来获取程序运行时的类，方法，变量



## ioc 容器是怎么实现对象的创建和依赖



![image-20201220142350601](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201220142350601.png)

参考java3y，

- 1.首先spring会根据bean的配置信息或者是其他方式，读取以后创建bean的注册表
- 2.然后根据bean的注册报去实例化bean，全限定类名，
- 3.然后把bean放入容器中，首先是到内部的一个缓冲池中，第一次是创建，后面去缓冲池去取，然后使用bean去取ioc容器提供给我们对象



# ioc-容器



## 两个重要ioc容器



> 一个是BeanFactory接口，它是实现了最基本的容器功能，
>
> ```java
> public interface BeanFactory 
> ```
>
> 另外一个是ApplicationContext 上下文 接口，他是扩展了BeanFactory，提供了更多的功能
>
> ```java
> public interface ApplicationContext extends EnvironmentCapable, ListableBeanFactory, HierarchicalBeanFactory,
> 		MessageSource, ApplicationEventPublisher, ResourcePatternResolver
> ```
>
> 

这两个接口 ApplicationContext和BeanFactory是比较核心的两个接口，

这两个容器可以满足我们大部分的应用场景

![image-20201220142836796](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201220142836796.png)

然后我们一般使用的是applicationContext的子类，如ClassPathXmlApplicationContext,

![image-20201220142957980](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201220142957980.png)







> 注意：BeanFacory和FactoryBean是有区别的。
>
> 大概区别是：
>
> beanfactory是我们去获取bean，实例化bean，他是ioc的核心接口，主要是的功能就是 资源定位，实例化应用程序的对象等。比如BeanFactory的子类ClassPathXmlApplicationContext可以从xml中解析标签得到bean对象，然后管理他们
>
> factorybean是把我们的bean放入bean工厂，在spring中有两种bean，一种是普通bean，一个是工厂bean。工厂bean可以产生其他bean实例。FactoryBean也是一个bean，他的设计模式有工厂模式和修饰器模式





## 读取xml文件解析 获取bean





```java
     //读取xml
        ApplicationContext classPathXmlApplicationContext = new ClassPathXmlApplicationContext("beans.xml");
       Object person = classPathXmlApplicationContext.getBean("person");
        System.out.println((Person)person);
```

xml的配置主要过程是 :

获取xml，处理xml每一个元素，读取创建得到每一个资源resource，解析得到BeanDefinition,注册到beanFactory

- 1.资源定位
- 2.加载
- 3.解析
- 4.注册

## IOC容器的初始化



### 两个核心类的继承关系



### DefaultListableBeanFactory



> 类继承树

![image-20201220110323272](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201220110323272.png)

主要的接口和作用：

- AliasRegistry(接口)：可以给bean起别名，这个接口定义了可以给别名的增删改查操作
- SimpleAliasRegistry(类)：使用map作为alias的缓存，对接口aliasRegistry实现
- BeanFactory(接口)，定义了获取bean和bean的属性
- HierarchicalBeanFactory（接口）：继承BeanFactory，也就是在BeanFactory定义功能的基础上增加了对parantFactory的支持。
- BeanDefinitionRegistry（接口）：定义了对BeanDefination的增删改查功能，BeanDefinition是一种spring特殊的结构，描述了一个bean的实例，包含了属性值（scope,bean的name id 懒加载模式等），构造函数和其他的实现信息，
- FactoryBeanRegistrySupport（类）；在DefaultSingleTonBeanRegistry基础上增加了对FactoryBean的特殊功能。
- ConfigurableBeanFactory(接口):提供了bean的各种配置的方法，包括设置父工厂，bean的加载器，添加 beanPostProcessor等功能
- ListableBeanFactory（接口）：根据条件获取bean的配置清单，可以根据bean的name type 获取bean的配置清单
- AbstractBeanFactory（类）：综合FactoryBeanRegistrySupport和ConfigurableBeanFactory的功能。
- AutowireCapableBeanFactory(接口)：提供创建bean，自动注入，初始化，以及应用bean的后置处理器，
- AbstractAutowireCapableBeanFactory(类)：综合AbstractBeanFactory并对接口AutowireCapableBeanFactory进行实现
- ConfigurableListableBeanFactory：BeanFactory配置清单，指定忽略类型及接口等。
- DefaultListableBeanFactory:综合了上面的所以功能，主要是对bean注册后的处理
- SingletonBeanRegistry(接口)：定义了对单例的注册和获取
- DefaultSingleTonBeanRegistry(接口)：实现了SingletonBeanRegistry的方法，同时继承了SimpleAliasRegistry。
- 





### XmlBeanDefinitionReader



> 类继承树

![image-20201220134318128](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201220134318128.png)

XmlBeanDefinitionReader实现了对资源文件的读取，解析，和注册，

他的主要接口和实现类

- EnvironmentCapable(接口)：定义了获取environment的方法，Environment代表了配置文件。
- BeanDefinitionReader(接口)：主要对资源文件读取并转换为BeanDefinition的各个功能
- AbstractBeanDefinitoinReader(类)：对EnvironmentCapable、BeanDefinitionReader类定义的功能进行实现。
- ResourceLoader(接口)：主要是获取资源文件，主要应用于根据给定的资源文件地址返回对应的Resource。



## spring通过xml配置获取bean的流程



### 问题：

spring是怎么通过读取xml配置然后获取配置信息，得到bean实例

### 流程



1.主程序

```
public static void main(String[] args) {
		// 1.读取xml 资源位置
		ApplicationContext cpx = new ClassPathXmlApplicationContext("spring-entry.xml");
		Test test = (Test) cpx.getBean(Test.class);
		System.out.println(test);
	}
```

2.通过debug进去看，实例化创建一个新的ClassPathXmlApplicationContext

```java
//实例化 创建一个新的创建一个新的ClassPathXmlApplicationContext，从给定的XML文件中加载定义，并自动刷新上下文(refresh方法)
	public ClassPathXmlApplicationContext(String configLocation) throws BeansException {
		this(new String[] {configLocation}, true, null);
	}

/**
使用给定的父级创建一个新的ClassPathXmlApplicationContext，然后从
xml中读取配置加载定义，加载所有的bean定义并且创建所有的单例
，从给定的XML文件中加载定义，并自动刷新上下文（调用refresh方法）。
Bean的创建和实例化都是在refresh()方法进行的，
refresh()函数中包含了几乎所有的ApplicationContext中提供的全部功能。
refresh()就是容器启动。
*/

public ClassPathXmlApplicationContext(String[] configLocations, boolean refresh, ApplicationContext parent) throws BeansException {
    super(parent);
    //设置配置路径
    this.setConfigLocations(configLocations);
    if (refresh) {
        //refresh Spring容器
        this.refresh();
    }
}
```

3.进入看setconfigLocation方法

```java
/**
	 * 设置此应用程序上下文的配置位置。
	 * 如果未设置，则实现可酌情使用默认值
	 * 支持多个配置文件已数组方式同时传入。
	 * @param locations
	 */
	public void setConfigLocations(@Nullable String... locations) {
		if (locations != null) {
			Assert.noNullElements(locations, "Config locations must not be null");
			this.configLocations = new String[locations.length];
			for (int i = 0; i < locations.length; i++) {
				this.configLocations[i] = resolvePath(locations[i]).trim();
			}
		}
		else {
			this.configLocations = null;
		}
	}
```



流程总结：

![image-20201220155511211](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201220155511211.png)



#### refresh()



时序图：

![image-20201220161012087](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201220161012087.png)



1.调用 refresh()方法

```java
/**
	 * refresh()标志着ioc容器的启动，
	 * 这个方法标志着IOC容器的正式启动。
	 * 这里的启动包括了BeanDefinition和Resource的定位、载入和注册三个基本过程。
	 *
	 * @throws BeansException
	 * @throws IllegalStateException
	 */
	@Override
	public void refresh() throws BeansException, IllegalStateException {
		synchronized (this.startupShutdownMonitor) {
			// 准备此上下文以进行容器刷新（上下文环境）。
			prepareRefresh();

			//告诉子类刷新内部bean工厂。初始化beanFactory进行xml的读取 解析
			ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();

			// 准备在当前上下文使用的这个bean工厂。并对beanfactory进行功能填充
			prepareBeanFactory(beanFactory);

			try {
				// 允许在上下文子类中对bean工厂进行后处理。就是这个方法中的处理器注册了bean
				postProcessBeanFactory(beanFactory);

				// 调用在上下文中注册并激活为bean的工厂处理器。
				invokeBeanFactoryPostProcessors(beanFactory);

				// 	// 注册拦截bean创建的bean处理器，这里只是注册，
        //真正的调用Bean是在getBean方法的时候
				registerBeanPostProcessors(beanFactory);

				// 为此上下文初始化消息源。如国际化
				initMessageSource();

				// 为此上下文初始化事件多播器。
				initApplicationEventMulticaster();

				// 在特定上下文子类中初始化其他特殊bean。
				onRefresh();

				// 检查侦听器bean并注册它们。
				registerListeners();

				// 实例化所有剩余的（非延迟初始化）单例。
				finishBeanFactoryInitialization(beanFactory);

				//最后一步：发布相应的事件。
				//完成刷新过程，通知生命周期处理器lifecycleProcessor刷新过程，同时发出ContextRefreshEvent通知别人
				finishRefresh();
			}

			catch (BeansException ex) {
				if (logger.isWarnEnabled()) {
					logger.warn("Exception encountered during context initialization - " +
							"cancelling refresh attempt: " + ex);
				}

				// 销毁已创建的单例以避免资源悬空浪费。
				destroyBeans();

				//重置“活动”标志。
				cancelRefresh(ex);

				// 将异常传播给呼叫者。
				throw ex;
			}

			finally {
				//在Spring的核心中重置常见的自省缓存，因为我们可能不再需要单例bean的元数据...
				resetCommonCaches();
			}
		}
	}
```



2.AbstractApplicationContext类obtainFreshBeanFactory

```java
	// 告诉子类刷新内部bean工厂
	protected ConfigurableListableBeanFactory obtainFreshBeanFactory() {
		/**
		 * 子类必须实现此方法才能执行实际的配置负载。 在进行任何其他初始化工作之前，由refresh()调用该方法。
		 * 子类将创建一个新的bean工厂并保留对其的引用，或者返回它持有的单个BeanFactory实例。 
		 * 在后一种情况下，如果多次刷新上下文，通常会抛出IllegalStateException。
		 */
		refreshBeanFactory();
		
		//返回ConfigurableListableBeanFactory
		return getBeanFactory();
	}



```

3. AbstractRefreshableApplicationContext类

```java
//此实现对此上下文的基础bean工厂执行实际的刷新，关闭前一个bean工厂（如果有），
	// 并为上下文生命周期的下一阶段初始化一个新的bean工厂。
	@Override
	protected final void refreshBeanFactory() throws BeansException {
		//如果有bean工厂 就关闭bean工厂
		if (hasBeanFactory()) {
			destroyBeans();
			closeBeanFactory();
		}
		try {
			//创建一个新的bean工厂， 
            //DefaultListableBeanFactory基于bean定义元数据的成熟bean工厂，可通过后处理器进行扩展，核心类
			DefaultListableBeanFactory beanFactory = createBeanFactory();
			// 序列号id，如果需要的话，让这个BeanFactory从ID反序列化掉BeanFactory对象
			// getId() ApplicationContext 返回此应用程序上下文的唯一ID
			beanFactory.setSerializationId(getId());
			////定制beanFactory，设置相关属性，包括是否允许覆盖同名称的不同定义的对象
			// 以及循环依赖以及设置@Autowired和@Qualifier注解解析器QualifierAnnotationAutowireCandidateResolver
			customizeBeanFactory(beanFactory);
			////加载bean定义信息，这一步实际上就从XML配置文件里的bean信息给读取到了Factory里了。
			//通常通过委派一个或多个bean定义读取器，将bean定义加载到给定的bean工厂中
			loadBeanDefinitions(beanFactory);
			this.beanFactory = beanFactory;
		}
		catch (IOException ex) {
			throw new ApplicationContextException("I/O error parsing bean definition source for " + getDisplayName(), ex);
		}
	}
```

> 在loadBeanDefinitions()方法执行后，在beanFactory变量里面存放一个ConcurrentHashMap变量，key是 bean id， value-是一个arraylist里面放着 属性名字 和属性值，如在前面 中的xml解析获取Bean
>
> key就是我们在xml中定义的bean name  person,value是 name和值，如name="name" value="xiaobo"

person的属性封装到 beanFactory

4.AbstractXmlApplicationContext.class

person的属性封装到beanFactory

```java
	//通过XmlBeanDefinitionReader加载bean定义。
	@Override
	protected void loadBeanDefinitions(DefaultListableBeanFactory beanFactory) throws BeansException, IOException {
		// 为给定的BeanFactory创建一个新的XmlBeanDefinitionReader。
		XmlBeanDefinitionReader beanDefinitionReader = new XmlBeanDefinitionReader(beanFactory);

		// 使用此上下文的配置Bean定义阅读器
		//资源加载环境。
		beanDefinitionReader.setEnvironment(this.getEnvironment());
		beanDefinitionReader.setResourceLoader(this);
		beanDefinitionReader.setEntityResolver(new ResourceEntityResolver(this));

		//允许子类提供阅读器的自定义初始化，
		//然后继续实际加载bean定义。
		initBeanDefinitionReader(beanDefinitionReader);
		//真正的加载bean定义信息
		loadBeanDefinitions(beanDefinitionReader);
	}

	//使用给定的XmlBeanDefinitionReader加载bean定义。 <p>
	// bean工厂的生命周期由{@link refreshBeanFactory}方法处理；
//因此，该方法仅用于加载和注册bean定义。
	protected void loadBeanDefinitions(XmlBeanDefinitionReader reader) throws BeansException, IOException {
		Resource[] configResources = getConfigResources();
		if (configResources != null) {
			reader.loadBeanDefinitions(configResources);
		}
		String[] configLocations = getConfigLocations();
		if (configLocations != null) {
			reader.loadBeanDefinitions(configLocations);
		}
	}
```

> 到这里总结:
>
> - 1.首先在refreshBeanFactory()方法中，初始化一个默认的DefaultListableBeanFactory，
> - 2.因为要当前xml配置文件，还要在loadBeanDefinitions（）初始化XmlBeanDefinitionReader,然后就可以读取配置文件了
> - 3.在loadBeanDefinitions(DefaultListableBeanFactory beanFactory)，类型DefaultListableBeanFactory的变量beanFactory就已经包含了所有**解析好的配置**了。



5.AbstractBeanDefinitionReader类



```java
@Override
	public int loadBeanDefinitions(Resource... resources) throws BeanDefinitionStoreException {
		Assert.notNull(resources, "Resource array must not be null");
		int count = 0;
		for (Resource resource : resources) {
			count += loadBeanDefinitions(resource);
		}
		return count;
	}



//从指定的资源位置加载bean定义。 <p>该位置也可以是位置模式，只要此bean定义阅读器的ResourceLoader是ResourcePatternResolver。
	public int loadBeanDefinitions(String location, @Nullable Set<Resource> actualResources) throws BeanDefinitionStoreException {
		ResourceLoader resourceLoader = getResourceLoader();
		if (resourceLoader == null) {
		。。。。。	
        }

		if (resourceLoader instanceof ResourcePatternResolver) {
			// 资源模式匹配可用。
			try {
				Resource[] resources = ((ResourcePatternResolver) resourceLoader).getResources(location);
				int count = loadBeanDefinitions(resources);
				if (actualResources != null) {
					Collections.addAll(actualResources, resources);
				}
				if (logger.isTraceEnabled()) {
		。。。。。
                }
				return count;
			}
			catch (IOException ex) {
	。。。。
            }
		}
		else {
			// 只能通过绝对URL加载单个资源。			
            Resource resource = resourceLoader.getResource(location);
			int count = loadBeanDefinitions(resource);
			if (actualResources != null) {
				actualResources.add(resource);
			}
			if (logger.isTraceEnabled()) {
			。。
            }
			return count;
		}
	}
```



6.PathMatchingResourcePatternResolver类

```java
/**
	 * 
	 * @param locationPattern 要解决的位置模式
	 * @return
	 * @throws IOException
	 */
@Override
	public Resource[] getResources(String locationPattern) throws IOException {
		Assert.notNull(locationPattern, "Location pattern must not be null");
		if (locationPattern.startsWith(CLASSPATH_ALL_URL_PREFIX)) {
			//一个类路径资源（可能有多个名称相同的资源）
			if (getPathMatcher().isPattern(locationPattern.substring(CLASSPATH_ALL_URL_PREFIX.length()))) {
				// 类路径资源模式
				return findPathMatchingResources(locationPattern);
			}
			else {
				// 具有给定名称的所有类路径资源
				return findAllClassPathResources(locationPattern.substring(CLASSPATH_ALL_URL_PREFIX.length()));
			}
		}
		else {
			// 通常只在这里在前缀后面寻找模式，
			//并且仅在Tomcat的“ war：”协议的“”分隔符之后。
			int prefixEnd = (locationPattern.startsWith("war:") ? locationPattern.indexOf("*/") + 1 :
					locationPattern.indexOf(':') + 1);
			if (getPathMatcher().isPattern(locationPattern.substring(prefixEnd))) {
				// 文件模式
				return findPathMatchingResources(locationPattern);
			}
			else {
				// 具有给定名称的单个资源
				return new Resource[] {getResourceLoader().getResource(locationPattern)};
			}
		}
	}
```



7 XmlBeanDefinitionReader类

```java

	/**
	 *从指定的XML文件加载bean定义。
	 */
	public int loadBeanDefinitions(EncodedResource encodedResource) throws BeanDefinitionStoreException {
		Assert.notNull(encodedResource, "EncodedResource must not be null");
		if (logger.isTraceEnabled()) {
			logger.trace("Loading XML bean definitions from " + encodedResource);
		}

		Set<EncodedResource> currentResources = this.resourcesCurrentlyBeingLoaded.get();

		if (!currentResources.add(encodedResource)) {
			throw new BeanDefinitionStoreException(
					"Detected cyclic loading of " + encodedResource + " - check your import definitions!");
		}

		try (InputStream inputStream = encodedResource.getResource().getInputStream()) {
			InputSource inputSource = new InputSource(inputStream);
			if (encodedResource.getEncoding() != null) {
				inputSource.setEncoding(encodedResource.getEncoding());
			}
            ///获取到读取xml配置文件的InputStream流后，进行BeanDefinitions的加载
			return doLoadBeanDefinitions(inputSource, encodedResource.getResource());
		}
		catch (IOException ex) {
			throw new BeanDefinitionStoreException(
					"IOException parsing XML document from " + encodedResource.getResource(), ex);
		}
		finally {
			currentResources.remove(encodedResource);
			if (currentResources.isEmpty()) {
				this.resourcesCurrentlyBeingLoaded.remove();
			}
		}
	}



/**
	 * 实际上是从指定的XML文件加载bean定义。
 */
	protected int doLoadBeanDefinitions(InputSource inputSource, Resource resource)
			throws BeanDefinitionStoreException {

		try {
            //获取xml的配置信息并封装为Document对象
			Document doc = doLoadDocument(inputSource, resource);
			int count = registerBeanDefinitions(doc, resource);
			if (logger.isDebugEnabled()) {
				logger.debug("Loaded " + count + " bean definitions from " + resource);
			}
			return count;
	
	}

```



#### registerBeanDefinitions()





所在位置类XmlBeanDefinitionReader

![image-20201220165540504](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201220165540504.png)



时序图

![image-20201220165701645](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201220165701645.png)



1.XmlBeanDefinitionReader类

```java
	/**
	注册给定DOM文档中包含的bean定义。
	 */
	public int registerBeanDefinitions(Document doc, Resource resource) throws BeanDefinitionStoreException {
		BeanDefinitionDocumentReader documentReader = createBeanDefinitionDocumentReader();
		int countBefore = getRegistry().getBeanDefinitionCount();
		// 进入这个方法registerBeanDefinitions
		documentReader.registerBeanDefinitions(doc, createReaderContext(resource));
		return getRegistry().getBeanDefinitionCount() - countBefore;
	}
```



2.DefaultBeanDefinitionDocumentReader类



```java

	 //*此实现根据“ spring-beans” XSD（历史上称为DTD）解析bean定义。
	@Override
	public void registerBeanDefinitions(Document doc, XmlReaderContext readerContext) {
		this.readerContext = readerContext;
		//进入doRegisterBeanDefinitions方法
        doRegisterBeanDefinitions方法(doc.getDocumentElement());
	}




//	在给定的根{@code <beans>}元素中注册每个bean定义。
	@SuppressWarnings("deprecation")  // for Environment.acceptsProfiles(String...)
	protected void doRegisterBeanDefinitions(Element root) {
		//任何嵌套的<beans>元素都将导致此方法中的递归。
		// 为了正确传播和保留<beans> default-属性，
		// 请跟踪当前（父）委托，该委托可以为null。
		// 创建一个新的（子）委托，并带有对父参考的引用以进行回退，然后最终将this.delegate重置回其原始（父）参考。
		// 此行为模拟了一组委托，而实际上没有必要。
		 BeanDefinitionParserDelegate parent = this.delegate;
		this.delegate = createDelegate(getReaderContext(), root, parent);

		if (this.delegate.isDefaultNamespace(root)) {
			String profileSpec = root.getAttribute(PROFILE_ATTRIBUTE);
			if (StringUtils.hasText(profileSpec)) {
				String[] specifiedProfiles = StringUtils.tokenizeToStringArray(
						profileSpec, BeanDefinitionParserDelegate.MULTI_VALUE_ATTRIBUTE_DELIMITERS);
//			我们无法使用Profiles.of（...），因为XML配置不支持配置文件表达式。有关详细信息，请参见SPR-12458。
				if (!getReaderContext().getEnvironment().acceptsProfiles(specifiedProfiles)) {
					if (logger.isDebugEnabled()) {
						logger.debug("Skipped XML bean definition file due to specified profiles [" + profileSpec +
								"] not matching: " + getReaderContext().getResource());
					}
					return;
				}
			}
		}

		preProcessXml(root);
		//解析beanDefinitions信息，经过这个方法，beanFactory中就会保存从xml配置文件中解析而来的信息
		parseBeanDefinitions(root, this.delegate);
		postProcessXml(root);

		this.delegate = parent;
	}


// 上面的parseBeanDefinitions方法



/**
	在文档的根级别上解析元素：“ import”，“ alias”，“ bean”。
	 */
	protected void parseBeanDefinitions(Element root, BeanDefinitionParserDelegate delegate) {
		if (delegate.isDefaultNamespace(root)) {
			NodeList nl = root.getChildNodes();
			for (int i = 0; i < nl.getLength(); i++) {
				Node node = nl.item(i);
				if (node instanceof Element) {
					Element ele = (Element) node;
					if (delegate.isDefaultNamespace(ele)) {
						parseDefaultElement(ele, delegate);
					}
					else {
						delegate.parseCustomElement(ele);
					}
				}
			}
		}
		else {
			delegate.parseCustomElement(root);
		}
	}


private void parseDefaultElement(Element ele, BeanDefinitionParserDelegate delegate) {
		if (delegate.nodeNameEquals(ele, IMPORT_ELEMENT)) {
			importBeanDefinitionResource(ele);
		}
		else if (delegate.nodeNameEquals(ele, ALIAS_ELEMENT)) {
			processAliasRegistration(ele);
		}
		else if (delegate.nodeNameEquals(ele, BEAN_ELEMENT)) {
			//读取xml配置文件 <bean>节点
			processBeanDefinition(ele, delegate);
		}
		else if (delegate.nodeNameEquals(ele, NESTED_BEANS_ELEMENT)) {
			// 递归
			doRegisterBeanDefinitions(ele);
		}
	}


//处理给定的bean元素，解析bean定义并将其注册到注册表中。
	protected void processBeanDefinition(Element ele, BeanDefinitionParserDelegate delegate) {
		//解析提供的<bean>元素。 	
        BeanDefinitionHolder bdHolder = delegate.parseBeanDefinitionElement(ele);

```



3 .BeanDefinitionParserDelegate 类



```java
/**
	解析提供的{@code <bean>}元素。如果解析期间发生错误，则可能返回{@code null}。错误报告给
	 */
	@Nullable
	public BeanDefinitionHolder parseBeanDefinitionElement(Element ele, @Nullable BeanDefinition containingBean) {
		String id = ele.getAttribute(ID_ATTRIBUTE);
		String nameAttr = ele.getAttribute(NAME_ATTRIBUTE);

		List<String> aliases = new ArrayList<>();
		if (StringUtils.hasLength(nameAttr)) {
			String[] nameArr = StringUtils.tokenizeToStringArray(nameAttr, MULTI_VALUE_ATTRIBUTE_DELIMITERS);
			aliases.addAll(Arrays.asList(nameArr));
		}

		String beanName = id;
		if (!StringUtils.hasText(beanName) && !aliases.isEmpty()) {
			beanName = aliases.remove(0);
			if (logger.isTraceEnabled()) {
					}
		}

		if (containingBean == null) {
			checkNameUniqueness(beanName, aliases, ele);
		}
		//这里要解析beanDefinition了
		AbstractBeanDefinition beanDefinition = parseBeanDefinitionElement(ele, beanName, containingBean);
		if (beanDefinition != null) {
			if (!StringUtils.hasText(beanName)) {
				try {
					if (containingBean != null) {
						beanName = BeanDefinitionReaderUtils.generateBeanName(
								beanDefinition, this.readerContext.getRegistry(), true);
					}
					else {
						beanName = this.readerContext.generateBeanName(beanDefinition);
						// Register an alias for the plain bean class name, if still possible,
						// if the generator returned the class name plus a suffix.
						// This is expected for Spring 1.2/2.0 backwards compatibility.
						String beanClassName = beanDefinition.getBeanClassName();
						if (beanClassName != null &&
								beanName.startsWith(beanClassName) && beanName.length() > beanClassName.length() &&
								!this.readerContext.getRegistry().isBeanNameInUse(beanClassName)) {
							aliases.add(beanClassName);
						}
					}
					if (logger.isTraceEnabled()) {
							}
				}
				catch (Exception ex) {
					error(ex.getMessage(), ele);
					return null;
				}
			}
            // 别名数组
			String[] aliasesArray = StringUtils.toStringArray(aliases);
			return new BeanDefinitionHolder(beanDefinition, beanName, aliasesArray);
		}

		return null;
	}


//	解析bean定义本身，而不考虑名称或别名。如果在bean定义的解析过程中发生问题，则可能返回{@code null}。
	@Nullable
	public AbstractBeanDefinition parseBeanDefinitionElement(
			Element ele, String beanName, @Nullable BeanDefinition containingBean) {

		this.parseState.push(new BeanEntry(beanName));

		String className = null;
		if (ele.hasAttribute(CLASS_ATTRIBUTE)) {
			className = ele.getAttribute(CLASS_ATTRIBUTE).trim();
		}
		String parent = null;
		if (ele.hasAttribute(PARENT_ATTRIBUTE)) {
			parent = ele.getAttribute(PARENT_ATTRIBUTE);
		}

		try {
			// 创建 BeanDefinition
			AbstractBeanDefinition bd = createBeanDefinition(className, parent);

			parseBeanDefinitionAttributes(ele, beanName, containingBean, bd);
			bd.setDescription(DomUtils.getChildElementValueByTagName(ele, DESCRIPTION_ELEMENT));

			parseMetaElements(ele, bd);
			parseLookupOverrideSubElements(ele, bd.getMethodOverrides());
			parseReplacedMethodSubElements(ele, bd.getMethodOverrides());
			// 通过构造器解析参数值
			parseConstructorArgElements(ele, bd);
			//解析给定bean元素的属性子元素。
			// 通过property的value解析，本文的程序xml就是通过property属性设置bean的值的，最终被这一方法所解析出来。
			parsePropertyElements(ele, bd);
			parseQualifierElements(ele, bd);

			bd.setResource(this.readerContext.getResource());
			bd.setSource(extractSource(ele));

			return bd;
		}
。。。。。。。}
		finally {
			this.parseState.pop();
		}

		return null;
	}


//	解析给定bean元素的属性子元素。
	public void parsePropertyElements(Element beanEle, BeanDefinition bd) {
		NodeList nl = beanEle.getChildNodes();
		for (int i = 0; i < nl.getLength(); i++) {
			Node node = nl.item(i);
			if (isCandidateElement(node) && nodeNameEquals(node, PROPERTY_ELEMENT)) {
				//解析参数值，这里就真正的讲age的23，和name的bruis值解析出来并防止在一个组装的类里面存放着。因为这里有两个bean，所以要循环调用两次parsePropertyElement()方法
				parsePropertyElement((Element) node, bd);
			}
		}
	}


//	解析属性元素。
	public void parsePropertyElement(Element ele, BeanDefinition bd) {
		String propertyName = ele.getAttribute(NAME_ATTRIBUTE);
		if (!StringUtils.hasLength(propertyName)) {
			error("Tag 'property' must have a 'name' attribute", ele);
			return;
		}
		//将新的ParseState.Entry添加到LinkedList 。
		this.parseState.push(new PropertyEntry(propertyName));
		try {
			if (bd.getPropertyValues().contains(propertyName)) {
				error("Multiple 'property' definitions for property '" + propertyName + "'", ele);
				return;
			}
			Object val = parsePropertyValue(ele, bd, propertyName);
			PropertyValue pv = new PropertyValue(propertyName, val);
			parseMetaElements(ele, pv);
			pv.setSource(extractSource(ele));
			//将key为age  name 值分别为18，xiaobo的 key-value存放在spring容器
            //添加 进BeanDefinition()的kv
			bd.getPropertyValues().addPropertyValue(pv);
		}
		finally {
			//从LinkedList删除一个ParseState.Entry 
			this.parseState.pop();
		}
	}
```



4 ， 回到 refresh() 方法的 loadBeanDefintion()，



![image-20201220192159891](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201220192159891.png)



**在这里就完成了spring容器的初始化，我们定义的Bean->BeanDefinition加载到容器中，并在ioc中创建了对应的数据结构，也就是上面的map，这样我们就可以以AbstractBeanDefinition为入口，操作ioc容器中的对象**



### 总结：

- 1.第一个就是资源定位，主要是对 BeanDefinition的定位，由ResourceLoader统一的接口Resource接口来完成。
- 2.加载BeanDefinition,这个是把我们自定义的bean转成spring的特殊数据结构，这个BeanDefinition相当于是从spring容器中的角度对我们的POJO的抽象，主要是分别ioc管理
- 3.向ioc容器去注册这些 BeanDefinition,主要是调用 BeanDefinitionRegistry接口来实现的，就是把解析后得到的BeanDefinition注册进去，注册到哪呢，就是我们最后一个的ConcurrentHashMap()



## 初始化总结



完成了BeanDefinition的注册，ioc容器的初始化就完成了，

现在在容器中就有了这些bean的信息了，在DefaultListableBeanFactory中创建了整个bean的配置信息，容器也可以使用这些BeanDefinition，他们可以在一个bean map中被查询使用，容器就是对他们统一管理和维护，这些信息是DI的基础，



# 补充



## ioc容器的创建和关闭

![image-20201220221652687](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201220221652687.png)

> 关闭bean也是 先发一个关闭的信号，一个一个的把bean关闭，如何关闭容器



