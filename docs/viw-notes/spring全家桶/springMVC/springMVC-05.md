[TOC]

# 1.拦截器





> lSpring MVC也可以使用拦截器对请求进行拦截处理，用户可以自定义拦截器来实现特定的功能，自定义的拦截器必须实现HandlerInterceptor接口
>
> 

![image-20200908000709749](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200908000709749.png)

```java
public interface HandlerInterceptor {
// 目标方法执行之前 调用，
    // 在业务处理器处理请求之前被调用，在该方法中对用户请求 request 进行处理。
default boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
			throws Exception {

		return true; // 通过 
	}
	// 目标方法执行以后 调用，目标方法调用之后
    //在业务处理器处理完请求后，但是DispatcherServlet 向客户端返回响应前被调用，在该方法中对用户请求request进行处理。
default void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
			@Nullable ModelAndView modelAndView) throws Exception {
	}
	// 在整个方法执行完成后， 跳转 目标页面 之后  资源响应之后
    //在 DispatcherServlet 完全处理完请求后被调用，可以在该方法中进行一些资源清理的操作。
	default void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler,
			@Nullable Exception ex) throws Exception {
	}
}
```



 ## 1.单拦截器



> 实现接口

```java
/**
 * 1.实现  接口
 * 2. 注册组件
 * @Author: xiaobo
 * @Date: 2020/9/8 0:50
 */

public class MyInterctptor implements HandlerInterceptor {

```



> 配置拦截器，注册组件

```xml

<mvc:interceptors>
    <!--默认拦截所有 请求-->
    <bean class="com.study.interceptor.MyInterctptor"></bean>

    <!--具体拦截某一个-->
 <!--   <mvc:interceptor>
        <mvc:mapping path="/handle01"/>
        <bean class="com.study.interceptor.MyInterctptor"></bean>
    </mvc:interceptor>-->
</mvc:interceptors>
```



> 拦截流程
>
> prehandle---目标方法--postHandle----页面----afterComple
>
> prehandle 不通过，即返回 false 后面流程就不执行了，
>
> 只要放行  就会执行到 afterComple

![image-20200908005945313](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200908005945313.png)

## 2.多拦截器

> 和单拦截器一样，实现接口
>
> 然后注册组件，
>
> 如果两个组件同时工作 作用流程如下
>
> 谁在前面谁优先，
>
> - 第一个拦截器不放行， 后续都不执行
> - 某一个拦截器不执行拦截，那这个拦截器的前面那个拦截器的 afterCompletion总会执行(前面所有拦截器的afterCompletion都执行)
> - preHandle 顺序执行
> - postHandle 逆序执行
> - afterCompletion  逆序执行

```xml
<mvc:interceptors>
    <!--默认拦截所有 请求-->
    <bean class="com.study.interceptor.MyInterctptor"></bean>

    <!--具体拦截某一个-->
    <mvc:interceptor>
        <mvc:mapping path="/handle01"/>
        <bean class="com.study.interceptor.MyInterceptors"></bean>
    </mvc:interceptor>
</mvc:interceptors>
```



```java
preHandle===        // 单拦截器
preHandle			// 多拦截器
handle01 invoke		// 目标方法
postHandle			// 多拦截器
postHandle			// 单拦截器
页面调用了handle01	// 页面调用
afterCompletion		// 多拦截器
afterCompletion		// 多拦截器
```

# 2.国际化

## 1.快速国际化

> 写国际化配置文件

![image-20200908011712968](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200908011712968.png)

> 注册组件

```xml
<bean id="messageSource" class="org.springframework.context.support.ResourceBundleMessageSource">
        <property name="basename" value="conf.i18n.loginpage.login"></property>
    </bean>
```

> 取值

```html
<fmt:message key="" />
```

## 2.国际化区域信息解析器

> 在页面展示的效果是 由浏览器带来的。
>
>  区域信息由区域信息解析器得到的

```java
/** LocaleResolver used by this servlet */
	@Nullable
	private LocaleResolver localeResolver;
```

> 获取localResolver

```java
LocaleResolver localResolver = request.getLoacl();
```



> 在springMVC中的默认使用区域信息解析器的是 `AcceptHeaderLocalResolver`  里面有  getLocale();

```java
@Override
	public Locale resolveLocale(HttpServletRequest request) {
		Locale defaultLocale = getDefaultLocale();
		if (defaultLocale != null && request.getHeader("Accept-Language") == null) {
			return defaultLocale;
		}
		Locale requestLocale = request.getLocale();
		if (isSupportedLocale(requestLocale)) {
			return requestLocale;
		}
		Locale supportedLocale = findSupportedLocale(request);
		if (supportedLocale != null) {
			return supportedLocale;
		}
		return (defaultLocale != null ? defaultLocale : requestLocale);
	}
```



## 3，程序中获取国际化信息

> Locale 根据浏览器国际化不同来获取不同的国际化信息

```java
    @RequestMapping("/local")
    public void hello(Locale locale){
        System.out.println(locale);
    }
    
    // console --》 zh_CN
```



> 程序中 获取国际化

![image-20200908225258194](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200908225258194.png)

```java
 @RequestMapping("/local")
    public void hello(Locale locale){
        System.out.println(locale);
        // 程序中获取国际化信息
        String username = rs.getMessage("username", null, locale);
        System.out.println(username);
    }
```

![image-20200908225544072](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200908225544072.png)



## 4. 点击链接切换国际化

> 先看一下springMVC的区域信息解析器

![image-20200908225939930](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200908225939930.png)

> 自定义 locale

```java
package com.study.controller;

import org.springframework.web.servlet.LocaleResolver;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.util.Locale;

/**
 * @Author: xiaobo
 * @Date: 2020/9/8 22:57
 */
public class MyLocalResolver implements LocaleResolver {
    /**
     * 解析返回locale
     */
    @Override
    public Locale resolveLocale(HttpServletRequest req) {
        // zh_CN
        Locale l = null;
        String localeStr = req.getParameter("locale");
        //如果带了locale参数就用参数指定的区域信息，如果没带就用请求头的
        if (localeStr != null && !"".equals(localeStr)) {
            l = new Locale(localeStr.split("_")[0], localeStr.split("_")[1]);
        } else {
            l = req.getLocale();
        }
        return l;
    }
    /**
     * 修改locale
     */
    @Override
    public void setLocale(HttpServletRequest arg0, HttpServletResponse arg1,
                          Locale arg2) {
        throw new UnsupportedOperationException(
                "Cannot change HTTP accept header - use a different locale resolution strategy");
    }
}

```

> 注册组件

在 `DispatcherServlet`中到 localResolver 的init

```java
/**
	 * Initialize the LocaleResolver used by this class.
	 * <p>If no bean is defined with the given name in the BeanFactory for this namespace,
	 * we default to AcceptHeaderLocaleResolver.
	 */
	private void initLocaleResolver(ApplicationContext context) {
		try {
            // 从ioc中获取 LcoalResolver
			this.localeResolver = context.getBean(LOCALE_RESOLVER_BEAN_NAME, LocaleResolver.class);
			if (logger.isDebugEnabled()) {
				logger.debug("Using LocaleResolver [" + this.localeResolver + "]");
			}
		}
		catch (NoSuchBeanDefinitionException ex) {
			// We need to use the default.
			this.localeResolver = getDefaultStrategy(context, LocaleResolver.class);
			if (logger.isDebugEnabled()) {
				logger.debug("Unable to locate LocaleResolver with name '" + LOCALE_RESOLVER_BEAN_NAME +
						"': using default [" + this.localeResolver + "]");
			}
		}
	}

```

```xml
<!----> 
<!--自定义区域信息-->
    <bean id="localResolver" class="com.study.controller.MyLocalResolver">

```

## 5, 使用 SessionLocalResolver 实现点击链接



> `SessionLocaleResolver`  区域信息是从 sesion中 获取的，下面`SessionLocaleResolver`  中 resolverLocale的源码

```java
@Override
	public Locale resolveLocale(HttpServletRequest request) {
		Locale locale = (Locale) WebUtils.getSessionAttribute(request, this.localeAttributeName);
		if (locale == null) {
			locale = determineDefaultLocale(request);
		}
		return locale;
	}
```

> 提前在sesioni域中放置信息

 

```java
   @RequestMapping("/tologinpage")
    public void tologinPage(@RequestParam(value = "locale", defaultValue = "zh_CN") String localeStr, Locale locale, Model model, HttpSession session) {
        //  从请求域中获取
        System.out.println(locale);
        // key是 	public static final String LOCALE_SESSION_ATTRIBUTE_NAME = SessionLocaleResolver.class.getName() + ".LOCALE"; 中的值，公元前 key一样
        String welcomeinfo = messageSource.getMessage("welcomeinfo", null,
                locale);
        System.out.println(welcomeinfo);
        model.addAttribute("msg", "哈哈哈");
    }
```

## 6.通过拦截器实现

> LocaleChangeInterceptor 在方法执行之前，获取区域信息  设置存进  区域信息解析器中

```xml
<bean class="org.springframework.web.servlet.i18n.SessionLocaleResolver" id="localeResolver"></bean>
<mvc:interceptors>
    <bean class="org.springframework.web.servlet.i18n.LocaleChangeInterceptor"></bean>
</mvc:interceptors>
```

> 工作流程

![image-20200908232622535](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200908232622535.png)

# 3. filter和拦截器

> filter:如果不需要配合其他组件使用，filter和拦截器差不多，依赖servlet容器
>
> 拦截器： 基于反射，强于filter，拦截器还可以在目标方法执行之前执行,可以友好的配合其他组件使用，拦截器可以获取 ioc容器中的各个bean





# 4.异常处理



### 1. `@ExceptionHandler` 异常处理

> Spring MVC 通过 HandlerExceptionResolver  处理程序的异常，包括 Handler 映射、数据绑定以及目标方法执行时发生的异常

![image-20200909224326549](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200909224326549.png)

```java
public interface HandlerExceptionResolver {
@Nullable
	ModelAndView resolveException(
			HttpServletRequest request, HttpServletResponse response, @Nullable Object handler, Exception ex);

}

```

> 页面

```html
<a href="${ctp }/handle02?username=admin">handle02</a><br/>
```

> 处理方法

```java
    @RequestMapping("/handle02")
    public String handle02(@RequestParam("username") String username) throws UserNameNotFoundException {
        if (!"admin".equals(username)) {

            System.out.println("登陆失败....");
            throw new UserNameNotFoundException();
        }
        System.out.println("登陆成功！。。。");
        return "success";
    }
```

```java
 /**
     * 告诉SpringMVC这个方法专门处理这个类发生的异常 1、给方法上随便写一个Exception，用来接受发生的异常
     * 2、要携带异常信息不能给参数位置写Model； 3、返回ModelAndView就行了；
     * 4、如果有多个@ExceptionHandler都能处理这个异常，精确优先 5、全局异常处理与本类同时存在，本类优先；
     */
	@ExceptionHandler(value = { Exception.class }) // 可以写多个exception 数组形式的
	public ModelAndView handleException01(Exception exception) {


		System.out.println("本类的：handleException01..." + exception);
		//
		ModelAndView view = new ModelAndView("myerror");
		view.addObject("exce", exception);
		// 视图解析器拼串
		return view;
	}
```

> 异常类

```java
@ResponseStatus(reason="用户被拒绝登陆",value= HttpStatus.NOT_ACCEPTABLE)
public class UserNameNotFoundException extends  Exception {

    private static final long serialVersionUID = 1L;
}
```

> console

```
preHandle===
登陆成功！。。。
postHandle
页面调用了handle01
afterCompletion
09-Sep-2020 22:55:12.562 淇℃伅 [localhost-startStop-1] org.apache.catalina.startup.HostConfig.deployDirectory 鎶妛eb 搴旂敤绋嬪簭閮ㄧ讲鍒扮洰褰� [D:\apache-tomcat-8.5.50\webapps\manager]
09-Sep-2020 22:55:12.607 淇℃伅 [localhost-startStop-1] org.apache.catalina.startup.HostConfig.deployDirectory Deployment of web application directory [D:\apache-tomcat-8.5.50\webapps\manager] has finished in [44] ms
preHandle===
登陆失败....
本类的：handleException01...com.study.exception.UserNameNotFoundException
afterCompletion

```

![image-20200909225839022](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200909225839022.png)



### 2. `@ExceptionHandler` 全局异常处理

> 每一个类都可能发生exception
>
> 所以集中处理所有异常
>
> 如果有多个@ExceptionHandler都能处理这个异常，精确优先 
>
> 全局异常处理与本类同时存在，本类优先

```java
package com.study.controller;

import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.servlet.ModelAndView;
/**
 * 集中处理所有exception
 *
 *
 * 1、集中处理所有异常的类加入到ioc容器中
 * 2、@ControllerAdvice专门处理异常的类
 * @Author: xiaobo
 * @Date: 2020/9/9 23:02
 */
@ControllerAdvice
public class MyAllExceptionController {
    // 处理数字
    @ExceptionHandler(value={ArithmeticException.class})
    public ModelAndView handleException01(Exception exception){
        System.out.println("全局的：handleException01..."+exception);
        ModelAndView view = new ModelAndView("myerror");
        view.addObject("ex", exception);
        //视图解析器拼串
        return view;
    }
    // 处理空指针exception
    @ExceptionHandler(value={NullPointerException.class})
    public ModelAndView handleException02(Exception exception){
        System.out.println("全局的：handleException02..."+exception);
        ModelAndView view = new ModelAndView("myerror");
        view.addObject("ex", exception);
        //视图解析器拼串
        return view;
    }
}
```

### 3. `@ResponseStatus` 标记自定义异常

> 给自定义异常处理上标记，当你不需要把异常响应在页面上，可以自定义异常打印在控制台中，查看
>
> 不能标记在method 上 如下 返回的是 自定义的异常状态值和 异常信息 

```java
 // @ResponseStatus(reason="反正我错误了。。。",value=HttpStatus.NOT_EXTENDED)
    @RequestMapping("/handle01")
    public String handle01(Integer i) {
        System.out.println("handle01....");
        System.out.println(10 / i);
        return "success";
    } // @ResponseStatus(reason="反正我错误了。。。",value=HttpStatus.NOT_EXTENDED)
    @RequestMapping("/handle01")
    public String handle01(Integer i) {
        System.out.println("handle01....");
        System.out.println(10 / i);
        return "success";
    }
```



> 自定义一个异常处理

```java
package com.study.exception;

import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.ResponseStatus;

/**
 * @Author: xiaobo
 * @Date: 2020/9/9 22:50
 */
@ResponseStatus(reason="用户被拒绝登陆",value= HttpStatus.NOT_ACCEPTABLE)
public class UserNameNotFoundException extends  Exception {

    private static final long serialVersionUID = 1L;
}

```

> console

```
preHandle===
登陆失败....
afterCompletion
preHandle===
登陆成功！。。。
```



### 4.springMVC默认异常处理



> spring内部异常，有负责的异常信息和异常页面
>
> `DefaultHandlerExceptionResolver` 判断自定义异常处理

```java
@Override
	@Nullable
	protected ModelAndView doResolveException(HttpServletRequest request, HttpServletResponse response,
			@Nullable Object handler, Exception ex) {

....
}
```



![image-20200909233240081](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200909233240081.png)

> console

```t
org.springframework.web.servlet.mvc.support.DefaultHandlerExceptionResolver.handleHttpRequestMethodNotSupported Request method 'GET' not supported
```

