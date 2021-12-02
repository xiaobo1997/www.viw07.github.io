[TOC]



# SpringMVC

## 视图和视图解析

### 1.forward前面缀指定一个转发操作

经过 `forward`:不会由我们配置的视图解析器拼串

```java
/**
     * forward:转发到一个页面
     * /hello.jsp：转发当前项目下的hello；
     * 一定加上/，如果不加/就是相对路径。容易出问题；
     * forward:不会由我们配置的视图解析器拼串
     *  forward:/hello.jsp
     * @return
     */
    @RequestMapping("/handle02")
    public  String handle02(){
        return "forward:/hello.jsp";
    }
    @RequestMapping("/handle03")
    public String handle03(){
        System.out.println("handle03");
        return "forward:/handle02"; // 收到handle03的请求 转发给handle02处理
    }
```

### 2.redirect前缀重定向到页面

`redirect:/xxx`

```java

/**
     * 重定向到hello.jsp页面
     * 注意：：：有前缀的转发和重定向操作，配置的视图解析器就不会进行拼串处理
     *
     * 转发	forward:转发的路径
     * 重定向	redirect:重定向的路径
     *          /hello.jsp:代表就是从当前项目下开始；SpringMVC会为路径自动的拼接上项目名
     *
     *
     * @return
     */
    @RequestMapping("/handle04")
    public String handle04(){
        System.out.println("04");
        return "redirect:/hello.jsp";
    }
    @RequestMapping("/handle05")
    public String handle05(){
        System.out.println("05");
        return "redirect:/handle03.jsp"; //重定向 handle03处理   handle03转发 handle02
    }
```

```java
//  RedirectView 源码  视图解析方法
/**
 	即模型数据转成 请求参数，转发提交给url
	 * Convert model to request parameters and redirect to the given URL.
	 * @see #appendQueryProperties
	 * @see #sendRedirect
	 */

	@Override
	protected void renderMergedOutputModel(Map<String, Object> model, HttpServletRequest request,
			HttpServletResponse response) throws IOException {

		String targetUrl = createTargetUrl(model, request);
		targetUrl = updateTargetUrl(targetUrl, model, request, response);

		// Save flash attributes
		RequestContextUtils.saveOutputFlashMap(targetUrl, request, response);

		// Redirect
		sendRedirect(request, response, targetUrl, this.http10Compatible);
	}
```



### 3.SpingMVC视图解析器

SpringMVC 为逻辑视图名的解析提供了不同的策略，可以在 Spring WEB 上下文中***\*配置一种或多种解析策略\****，***\*并指定他们之间的先后顺序\****。每一种映射策略对应一个具体的视图解析器实现类。

 视图解析器的作用比较单一：将逻辑视图解析为一个具体的视图对象。

所有的视图解析器都必须实现 ViewResolver 接口

---

**视图解析器的实现类：**

![image-20200903005055881](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200903005055881.png)

每个视图解析器都实现了 Ordered 接口并开放出一个 order 属性，可以通过 order 属性指定解析器的优先顺序，order  越小优先级越高。



---



**流程：**

![image-20200903002129578](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200903002129578.png)

在控制器 请求处理方法执行完  返回类型是 string, View , ModelAndView 都会被装配成一个 `ModelAndView `对象，通过 视图解析器 `ViewResolver` 解析 ，返回渲染页面，被解析的可能是JSP ,Excel 等

`View`和 `ViewResolver`都是接口  ，是视图解析器中 非常重要的两个接口

视图解析器只是为了得到 视图对象， 视图对象来渲染页面

![image-20200903003013713](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200903003013713.png)

![image-20200903002959896](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200903002959896.png)

`InternalResourceViewResolver` 的关系图(我们在dispactservlet中写的)

```xml
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/pages/"></property>
        <property name="suffix" value=".jsp"></property>
    </bean>
```

![image-20200903003115065](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200903003115065.png)

**视图解析器流程图**

![image-20200903003512325](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200903003512325.png)

总结：

不管什么请求处理方法，Spring MVC 也会在内部将它们装配成一个 ModelAndView 对象

springMVC 用视图解析器 `ViewResolver` 得到视图对象（view）渲染页面，实现接口为了解耦合



### 4.视图对象

**视图**的作用是渲染模型数据，将模型里的数据以某种形式呈现给客户。

l为了实现视图模型和具体实现技术的解耦，Spring 在 org.springframework.web.servlet 包中定义了一个高度抽象的 ***\*View\**** 接口：

视图对象由视图解析器 `ViewResolver` 实例化(视图解析器根据视图名创建对象)， 视图是无状态，没有线程安全问题

我们可以自定义实现View  编写自己的 视图解析类 。



常用的视图实现类：

![image-20200903004706897](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200903004706897.png)

`InternalResourceView` 转发页面，把模型数据放到请求域中

### 5. jstlView国际化功能

`JstlView` 类关系图： 是InternalResourceView的一个 子类

![image-20200903005442577](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200903005442577.png)

可以通过在 dispaccherservlet 指明 view的类型

```xml
  <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/pages/"></property>
        <property name="suffix" value=".jsp"></property>
        <!--指明  view 的类型-->
        <property name="viewClass" value="org.springframework.web.servlet.view.JstlView"></property>
    </bean>


```



JstlView 可以实现我们的国际化功能：



```xml
  <!-- 配置让springMVC管理的国际化资源文件管理器 id要为messageSource-->
    <bean id="messageSource" class="org.springframework.context.support.ResourceBundleMessageSource">
        <!-- 指明 基础名-->
        <property name="basename" value="conf.i18n"></property>
    </bean>
```



![image-20200903011349842](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200903011349842.png)

页面上  `<fmt:message>` 直接使用取值即可

```xml
<fmt:message key="username"/>
```

`JstlView` 源码方法

```java
/**
	 * Exposes a JSTL LocalizationContext for Spring's locale and MessageSource.
	 * @see JstlUtils#exposeLocalizationContext
	 */
	@Override
	protected void exposeHelpers(HttpServletRequest request) throws Exception {
		if (this.messageSource != null) {
            // 根据请求带来的区域信息去寻找 匹配的区域信息 
			JstlUtils.exposeLocalizationContext(request, this.messageSource);
		}
		else {
			JstlUtils.exposeLocalizationContext(new RequestContext(request, getServletContext()));
		}
	}
```

创建国际化信息 需要让springMVC加载，不能先于 springMVC解析之前 加载否则无法使用国际化信息， 相应的 需要经过SpringMVC的视图解析流程，springMVC才会创建一个JstlView来使用国际化组件，不能写 `forward`

### 6. view-controller 映射请求到页面

场景： 有些方法只是为了跳转 ，没有业务层，因为我们只是为了发送请求 跳转页面

```xml
 <!-- path="":指定哪个请求 
	    view-name：指定映射给哪个视图；
	    走了SpringMVC的整个流程；视图解析。。。。
	   直接把请求映射到页面
	   相当于我们在 controller 中写 的  @RequesrMapping(/xxx) return  "xxxxx";
	 -->
    <mvc:view-controller path="/toLoginPage" view-name="login"></mvc:view-controller>
    <!--开启 mvc注解驱动-->
    <mvc:annotation-driven></mvc:annotation-driven>

```

> 上面的作用相当于我们写的 
>
> ```java
>     @RequestMapping("/login")
>     public String handle06(){
>         System.out.println("06");
>         return "login"; 
>     }
> ```
>

**在dispatcherServlet中 使用 注解驱动模式**

```xml
<mvc:annotation-driven></mvc:annotation-driven>
```



### 7. 自定义视图和自定义视图解析器

> 视图解析器根据方法的返回值得到视图对象
>
> 视图对象不同具有不同渲染功能，功能也不同

场景：

- 1.让我们的自定义视图解析器工作 得到视图对象
- 2.得到我们视图对象，然后模型数据自定义渲染页面

步骤：

- 1.编写视图解析器
- 2.使加载到 `IOC` 容器中

实现：

- 我们自定义的视图解析器  实现`ViewResolver` 接口

  ```java
  /**
   * 自定义 视图解析器
   *
   * @Author: xiaobo
   * @Date: 2020/9/3 22:21
   */
  
  public class MyViewRes implements ViewResolver {
      @Override
      public View resolveViewName(String viewName, Locale locale) throws Exception {
          // 根据视图名返回视图对象 解析我们自定义的 myViewTag
          if (viewName.startsWith("myViewTag:")) {
              return new MyView();
          } else {
              return null;
          }
      }
  }
  ```

  



- 自定义视图对象

  ```java
  /**
   * 自定义的视图对象
   * @Author: xiaobo
   * @Date: 2020/9/3 22:25
   */
  public class MyView implements View {
      /**
       * 返回的数据类型
       * @return
       */
      @Override
      public String getContentType() {
          return "text/html";
      }
      @Override
      public void render(Map<String, ?> model, HttpServletRequest request, HttpServletResponse response) throws Exception {
          System.out.println("我的ModelMap 模型数据 "+model.toString());
          response.setContentType("text/html");
          response.getWriter().write("<h2>然后展示模型数据到页面</h2>");
          List<Object> list = (List<Object>) model.get("myAttr"); //取出模型数据
          response.getWriter().write("在这里我们可以编写js代码" +
                  "<br/><h1>ha</h1>");
      }
  }
  ```

  



- 自定义的controller

  ```java
  @Controller
  public class MyViewResovlerController {
      @RequestMapping("/handle06")
      public  String handle06(ModelMap modelMap){
          //模型数据 给视图解析器
          List<Object> list = new ArrayList<>();
          list.add("hello");
          modelMap.addAttribute("myAttr",list);
          return "myViewTag:/hello.jsp";
      }
  }
  ```

自定义的视图解析器功能 得到自定义的视图对象生效：

![image-20200903225415311](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200903225415311.png)



> 注意
>
> ```xml
>     <!--自定义视图解析器-->
>     <bean class="com.study.View.MyViewRes"></bean>
> 
>     <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
>         <property name="prefix" value="/WEB-INF/pages/"></property>
>         <property name="suffix" value=".jsp"></property>
>         <!--指明  view 的类型-->
>         <property name="viewClass" value="org.springframework.web.servlet.view.JstlView"></property>
>     </bean>
> ```
>
> 我们的自定义视图解析器是在  InternalResourceViewResolver 前面 ，或者就是在 InternalResourceViewResolver后面解析，或者 让实现ordered接口
>
> 因为  InternalResourceViewResolver  在springMVC 默认的优先级是最低的(order 越大 优先级越低)
>
> ```xml
> <!--自定义视图解析器 优先级最大-->
> <bean class="com.study.View.MyViewRes" >
>     <property name="order" value="1"></property>
> </bean>
> ```

页面：

![image-20200903230925890](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200903230925890.png)

