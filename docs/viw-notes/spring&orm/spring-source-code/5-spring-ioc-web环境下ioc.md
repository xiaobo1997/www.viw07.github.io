[TOC]



# web环境下的ioc



> 主要是web容器下如何创建和启动ioc容器，
>
> 如何创建，初始化，启动。
>
> 





## ContextLoaderListener



> ```java
> public class ContextLoaderListener extends ContextLoader implements ServletContextListener
> ```
>
> 在web开发环境中，ContextLoaderLister的作用就是 启动web容器,是spring mvc的启动类，如何自动加载ApplicationContext的信息。他实现了ServletContextListener接口，在web.xml中配置了这个监听器，启动容器时会去使用ServletContextListener接口,这样我们是可以在响应服务请求之前向ServletContext创建添加对象
>
> ioc容器创建的过程就是spring上下文的创建过程，和web mvc相关的上下文是WebApplicationContext.



**ContextLoaderListener启动的上下文为根上下文。和web mvc相关的上下文是root上下文的子上下文**



**ServletContextListener的核心就是初始化 WebApplicationContext**并放入ServletContext中。



**时序图：**

![image-20201220224938954](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201220224938954.png)



### ContextLoaderListener类

```java

public class ContextLoaderListener extends ContextLoader implements ServletContextListener {
    public ContextLoaderListener() {
    }

    public ContextLoaderListener(WebApplicationContext context) {
        super(context);
    }

// 初始化根Web应用程序上下文。
    // 建立WebApplicationContext的过程
    public void contextInitialized(ServletContextEvent event) {
        this.initWebApplicationContext(event.getServletContext());
    }
	// 关闭根Web应用程序上下文。
    public void contextDestroyed(ServletContextEvent event) {
        this.closeWebApplicationContext(event.getServletContext());
        ContextCleanupListener.cleanupAttributes(event.getServletContext());
    }
}
```





###  ContextLoader

```java
/**
使用构造时提供的应用程序上下文，或根据“ {@link CONTEXT_CLASS_PARAM contextClass}”和“ {@link CONFIG_LOCATION_PARAM contextConfigLocation}”上下文参数，
为给定的servlet上下文初始化Spring的Web应用程序上下文
*/
public WebApplicationContext initWebApplicationContext(ServletContext servletContext) {
	//判断在web容器中是否存在WebApplicationContext，因为在配置中只允许申明一次ServletContextListener，多
    //次声明会扰乱Spring的执行逻辑。	
    if (servletContext.getAttribute(WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE) != null) {
	..
        }

		servletContext.log("Initializing Spring root WebApplicationContext");
		Log logger = LogFactory.getLog(ContextLoader.class);
		if (logger.isInfoEnabled()) {
		}
		long startTime = System.currentTimeMillis();

		try {
			//将上下文存储在本地实例变量中，以确保它在ServletContext关闭时可用。
			if (this.context == null) {
                //初始化context
				this.context = createWebApplicationContext(servletContext);
			}
            // // 确保该容器是可配置的web容器
			if (this.context instanceof ConfigurableWebApplicationContext) {
				ConfigurableWebApplicationContext cwac = (ConfigurableWebApplicationContext) this.context;
				// 上下文尚未刷新 - >提供诸如设置父上下文，设置应用程序上下文ID等服务
                if (!cwac.isActive()) {
					//上下文尚未刷新->提供诸如设置父上下文，设置应用程序上下文ID等服务。
					if (cwac.getParent() == null) {
					  // 在Web容器中建立起双亲IOC容器
						ApplicationContext parent = loadParentContext(servletContext);
						cwac.setParent(parent);
					}
                    // 开始配置并初始化WebApplicationContext。
					configureAndRefreshWebApplicationContext(cwac, servletContext);
				}
			}
			servletContext.setAttribute(WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE, this.context);

		......
	}
```

> ioc的创建工程是在ContextLoader类中的initWebApplicationContext（）方法完成的。



### WebApplicationContext



```java
public interface WebApplicationContext extends ApplicationContext {

	/**
	 * 用于在成功启动时将根WebApplicationContext绑定到的Context属性。
	 */
	String ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE = WebApplicationContext.class.getName() + ".ROOT";

	/**
	 * 获取Web容器的上下文，也就是ServletContext对象，这里相当于提供了一个Web容器级别的全局变量。
	 */
	ServletContext getServletContext();

}

```



> WebApplicationContext接口的具体功能是由XmlWebApplicationContext实现的，然后再通过ApplicationContext和BeanFactory一起完成spring容器的功能创建，
>
> initWebApplicationContext()方法返回的是WebApplicationContext接口
>
> 在这里实际上返回的是XMLWebApplicationContext实现类，在原有基础上增加了 对web环境和 xml的处理。
>
> 在XmlWebApplicationContext初始化时，Web容器的ioc容器被创建起来，这样这个web程序就可以应用spring应用



### XmlWebApplicationContext 





```java
/** 默认读取Spring配置文件的根路径，如果指定其他配置文件，则从这个默认的根路径读取*/
	public static final String DEFAULT_CONFIG_LOCATION = "/WEB-INF/applicationContext.xml";

	/**默认的配置文件位置在/WEB-INF/目录下. */
	public static final String DEFAULT_CONFIG_LOCATION_PREFIX = "/WEB-INF/";

	/**默认的配置文件后缀.xml文件 */
	public static final String DEFAULT_CONFIG_LOCATION_SUFFIX = ".xml";


// 和之前的一样
@Override
	protected void loadBeanDefinitions(DefaultListableBeanFactory beanFactory) throws BeansException, IOException {
		XmlBeanDefinitionReader beanDefinitionReader = new XmlBeanDefinitionReader(beanFactory);
		beanDefinitionReader.setEnvironment(getEnvironment());
		beanDefinitionReader.setResourceLoader(this);
		beanDefinitionReader.setEntityResolver(new ResourceEntityResolver(this));
		initBeanDefinitionReader(beanDefinitionReader);
		loadBeanDefinitions(beanDefinitionReader);
	}
	protected void initBeanDefinitionReader(XmlBeanDefinitionReader beanDefinitionReader) {
	}
	protected void loadBeanDefinitions(XmlBeanDefinitionReader reader) throws IOException {
		String[] configLocations = getConfigLocations();
		if (configLocations != null) {
			for (String configLocation : configLocations) {
				reader.loadBeanDefinitions(configLocation);
			}
		}
	}
	@Override
	protected String[] getDefaultConfigLocations() {
		if (getNamespace() != null) {
			return new String[] {DEFAULT_CONFIG_LOCATION_PREFIX + getNamespace() + DEFAULT_CONFIG_LOCATION_SUFFIX};
		}
		else {
			return new String[] {DEFAULT_CONFIG_LOCATION};
		}
	}

```

> XMLWebApplicationContext中**成员变量**存放着默认的读取**Spring配置文件的根目录**，在生成IOC容器过程中，就会从默认路径/WEB-INF/applicationContext.xml配置文件中或者指定的配置文件路径获取，然后再通过熟悉的loadBeanDefinitions()方法来**获取Bean定义信息**，最终**完成整个上下文的初始化过程。**



### ContextLoader





```java
/**
实例化此加载程序的根WebApplicationContext，
可以是默认上下文类，也可以是自定义上下文类（如果已指定）。 <p>此实现期望自定义上下文能够实现{@link ConfigurableWebApplicationContext}接口。可以在子类中覆盖。 <p>此外，在刷新上下文之前会调用{@link customContext}，从而允许子类对上下文执行自定义修改。
*/
protected WebApplicationContext createWebApplicationContext(ServletContext sc) {
		Class<?> contextClass = determineContextClass(sc);
	// 	
    if (!ConfigurableWebApplicationContext.class.isAssignableFrom(contextClass)) {
			throw new ApplicationContextException("Custom context class [" + contextClass.getName() +
					"] is not of type [" + ConfigurableWebApplicationContext.class.getName() + "]");
		}
		return (ConfigurableWebApplicationContext) BeanUtils.instantiateClass(contextClass);
	}

/**
返回要使用的WebApplicationContext实现类，
如果指定，则为默认XmlWebApplicationContext或自定义上下文类。
*/
protected Class<?> determineContextClass(ServletContext servletContext) {
		String contextClassName = servletContext.getInitParameter(CONTEXT_CLASS_PARAM);
	// 判断是否存在指定的IOC	
    if (contextClassName != null) {
			try {
				return ClassUtils.forName(contextClassName, ClassUtils.getDefaultClassLoader());
			}
			catch (ClassNotFoundException ex) {
				
		}
		else {
            // 如果没有指定的IOC容器，则properties中获取默认的IOC容器，也就是XMLWebApplicationContext。
			contextClassName = defaultStrategies.getProperty(WebApplicationContext.class.getName());
			try {
				return ClassUtils.forName(contextClassName, ContextLoader.class.getClassLoader());
			}
			catch (ClassNotFoundException ex) {
			
		}
	}
```



```java
protected void configureAndRefreshWebApplicationContext(ConfigurableWebApplicationContext wac, ServletContext sc) {
		if (ObjectUtils.identityToString(wac).equals(wac.getId())) {
		//	应用程序上下文ID仍设置为其原始默认值->根据可用信息分配一个更有用的ID
			String idParam = sc.getInitParameter(CONTEXT_ID_PARAM);
			if (idParam != null) {
				wac.setId(idParam);
			}
			else {
				// Generate default id...
				wac.setId(ConfigurableWebApplicationContext.APPLICATION_CONTEXT_ID_PREFIX +
						ObjectUtils.getDisplayString(sc.getContextPath()));
			}
		}
		// 设置ServletContext的引用
		wac.setServletContext(sc);
		String configLocationParam = sc.getInitParameter(CONFIG_LOCATION_PARAM);
		if (configLocationParam != null) {
			wac.setConfigLocation(configLocationParam);
		}

		// The wac environment's #initPropertySources will be called in any case when the context
		// is refreshed; do it eagerly here to ensure servlet property sources are in place for
		// use in any post-processing or initialization that occurs below prior to #refresh
		ConfigurableEnvironment env = wac.getEnvironment();
		if (env instanceof ConfigurableWebEnvironment) {
			((ConfigurableWebEnvironment) env).initPropertySources(sc, null);
		}
		//// 自定义上下文
		customizeContext(sc, wac);
    // // 调用SpringIOC容器的refresh()方法。
		wac.refresh();
	}

```



## 总结

> web环境下的spring应用，可以指定在启动时侯加载ioc容器（WebApplicationContext），加载的主要实现是 	ContextLoaderListener,他是web容器的一个监听器，