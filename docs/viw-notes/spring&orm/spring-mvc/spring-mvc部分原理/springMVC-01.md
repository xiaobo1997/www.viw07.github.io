[TOC]

 # springMVC

## 1.概述和整体架构

###  **1.spring 整体结构图：**

![image-20200830220826658](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200830220826658.png)

### **2.springMVC简单介绍&&MVC和MVVM的区别：**

现在更多使用的是前后端分离的MVVM来构建web应用，后端开发REST方式开发业务功能接口，提供给前端调用

> `MVVM`:(Model-View-ViewModel) :M:数据模型(具体的业务模型service和数据模型bean) V:用户界面 VM: ViewModel 是一个同步View 和 Model的对象。View 和 Model 之间并没有直接的联系，而是通过ViewModel进行交互。ViewModel 通过双向数据绑定把 View 层和 Model 层连接了起来，而View 和 Model 之间的同步工作完全是自动的，无需人为干涉，因此开发者只需关注业务逻辑，不需要手动操作DOM, 不需要关注数据状态的同步问题，复杂的数据状态维护完全由 MVVM 来统一管理。
>
> VM双向绑定：在 MVVM 框架中，View(视图) 和 Model(数据) 是不可以直接通讯的，在它们之间存在着 ViewModel 这个中间介充当着观察者的角色。当用户操作 View(视图)，ViewModel 感知到变化，然后通知 Model 发生相应改变；反之当 Model(数据) 发生改变，ViewModel 也能感知到变化，使 View 作出相应更新。这个一来一回的过程就是我们所熟知的双向绑定
>
> MVVM  核心是提供对View 和 ViewModel 的双向数据绑定，View和Model之间并没有直接的联系，而是通过ViewModel进行交互，View的变动，自动反映在ViewModel上，反之亦然，这样就保证视图和数据的一致性。
>
> ---
>
> 
>
> `MVC`:(Model-View-Controller) M:用于存放数据模型 V:用户界面 C:Controller是Model和View的协调者(控制整个网站的跳转逻辑)，Controller把Model中的数据拿过来给View使用。Controller可以直接与Model和View进行通信，而View不能与Controller直接通信。
>
> MVC流程：　用户操作> View (负责接受用户的输入操作)>Controller（业务逻辑处理）>Model（数据持久化）>View（将结果通过View反馈给用户）
>
> MVC 提倡 每一层只编写自己的代码，而不编写其他代码 ，比如 controller 就调用sevice业务逻辑处理  跳转或重定向 其他页面
>
> 
>
> 缺点：1.所有业务逻辑都在Controller里操作，逻辑复杂且不利于维护，
> 　　　　　2.大量的DOM 操作使页面渲染性能降低，加载速度变慢，影响用户体验。
> 　　　　  3.当 Model 频繁发生变化，需要主动更新到View ；当用户的操作导致Model发生变化，同样需要将变化的数据同步到Model中， 这样的工作不仅繁琐，而且很难维护复杂多变的数据状态。
>
> ---
>
> 
>
> MVC和MVVM的区别：它实现了View和Model的自动同步，也就是当Model的属性改变时，我们不用再自己手动操作Dom元素，来改变View的显示，而是改变属性后该属性对应View层显示会自动改变。简化了业务与界面的依赖，还解决了数据频繁更新的问题，不用再用选择器操作DOM元素。因为在MVVM中，View不知道Model的存在，Model和ViewModel也观察不到View，这种低耦合模式提高代码的可重用性。

springMVC是一款基于MVC的web层应用框架

通过MVC注解 可以让POJO成为处理请求的控制器 而不用实现多余接口

支持REST 风格的URL请求，采用了松散耦合可插拔组件结构，比其他 MVC 框架更具扩展性和灵活性

![image-20200830222930997](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200830222930997.png)

![image-20200830223040271](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200830223040271.png)

### 3.spirngMVC完整请求处理流程

浏览器发起http请求然后接收返回结果：

![image-20200830223825554](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200830223825554.png)

​	



### 4.构建基本环境



构建maven开发环境，

选择项目基本架构webapp(下面打错了，是sources root 和 resources root)![image-20200831003816983](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200831003816983.png)

创建maven标准目录结构![image-20200831004034425](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200831004034425.png)



pom.xml依赖

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--编译版本 1.8-->
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <spring.version>5.0.2.RELEASE</spring.version>
  </properties>

  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-web</artifactId>
      <version>${spring.version}</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>${spring.version}</version>
    </dependency>
 </dependencies>
```

最重要的前端控制器 `DispatcherServlet` 用来拦截所有请求，并智能分发

```xml
  <servlet>
    <servlet-name>dispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!--指定springmvc.xml 路径-->
   <!-- <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:springmvc.xml</param-value>
    </init-param>-->
    <!--服务器启动创建对象 值小优先级越高 越先创建对象 -->
   <!-- <load-on-startup>1</load-on-startup>-->
  </servlet>
  <servlet-mapping>
    <servlet-name>dispatcherServlet</servlet-name>
    <!--/  和 /*(会拦截*.jsp *.js) 拦截所有请求-->
    <url-pattern>/</url-pattern>
  </servlet-mapping>
```

然后就是 resource项下的 springmvc.xml配置文件(`resource--右键--new--XML configuration file--spring config`)![image-20200831005130098](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200831005130098.png)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
    <!--扫描组件位置-->
    <context:component-scan base-package="com.study"></context:component-scan>
    <!--配置视图解析器-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/pages"></property>
        <!--后面springMVC推荐使用thymeleaf-->
        <property name="suffix" value=".jsp"></property>
    </bean>
</beans>
```

配置应用服务器![image-20200831005245570](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200831005245570.png)

#### 1.一个最简单的controller示例

工程所需基本包：

![image-20200831125259855](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200831125259855.png)

工程目录：

![image-20200831125130751](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200831125130751.png)

controller:

```java
@Controller
public class MyController {
    @RequestMapping("/hello")//处理当前项目下的hello请求
    public  String myRequ(){
       /* return "/WEB-INF/pages/hello.jsp";*/
        return "hello";
    }
}
```

#### 2.分析示例的运行流程

处理请求的大概过程

1.客户端发送request请求

2.request请求进入tomcat服务器

3.DispatcherServlet拦截所有请求，寻找并且匹配被注解 `@RequestMapping` 标记的方法

4.返回匹配正确的具体类的具体方法，发射执行目标方法

5.执行方法体方法  后 返回值就是页面地址，

6.经过视图解析器拼串 得到完整的页面地址

7.获取页面地址以后，DispatcherServlet 帮我们跳转到最终页面

#### 3. `DispatcherServlet`的读取路径

指定 DispatcherServlet的位置：

![image-20200831191526706](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200831191526706.png)

使用默认的位置

![image-20200831192248255](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200831192248255.png)

####   4. `/`和   `/*`  的区别

/：拦截所有请求，不拦截jsp页面，*.jsp请求

/*：拦截所有请求，拦截jsp页面，*.jsp请求

tomcat服务器中 有一个默认的setvlet处理静态资源 是  `DefaultServlet` 这个servlet的处理方式就是 `/`  而我们使用 `/` 就可以覆盖默认的 处理方式 `/`

 `/*`  直接就是拦截所有请求；我们写  `/` ；也是为了迎合后来Rest风格的URL地址

#### 5 解决请求处理的乱码问题：

提交的数据可能有乱码：

请求乱码：

- GET 请求 ：改 server.xml   `URIEncoding="UTF-8"`

- POST：在第一次获取请求之前 设置  `request.setCharacterEncoding("UTF-8")`

  ```xml
  <!-- 配置一个字符编码的Filter；一定注意：字符编码filter一般都在其他Filter之前； -->
  	<filter>
  		<filter-name>CharacterEncodingFilter</filter-name>
  		<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
  		<!-- encoding：指定解决POST请求乱码 -->
  		<init-param>
  			<param-name>encoding</param-name>
  			<param-value>UTF-8</param-value>
  		</init-param>
  		<init-param>
  			<!-- forceEncoding:顺手解决响应乱码；response.setCharacterEncoding(this.encoding); -->
  			<param-name>forceEncoding</param-name>	
  			<param-value>true</param-value>
  		</init-param>
  		
  	</filter>
  	<filter-mapping>
  		<filter-name>CharacterEncodingFilter</filter-name>
  		<url-pattern>/*</url-pattern>
  	</filter-mapping>
  ```

  

响应乱码:

`response.setContentType("text/html;charset=utf-8")`

#### 6. 解决maven项目创建web-app 骨架 过慢的问题

增加一定键值对

archetypeCatalog
internal

![image-20200901013446861](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200901013446861.png)

## 2.常用注解

### 1. `@RequestMapping` 

`@RequestMapping` 是一个用来为 控制器 处理请求地址映射的注解 , dispatcherServlet 拦截请求后，通过 控制器上的  `@RequestMapping` 提供的映射信息确定请求所对应的处理方法

可以作用在 方法和 类上

- 方法上：如果在该上类没有定义该注解，则被标记的作为父路径。![image-20200831193720970](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200831193720970.png)
- 类上:该类中的所有响应请求的方法都是以该地址作为父路径   ![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200831193736219.png)

**六个属性(分三类)：**

- value, method

  - value:指定请求的地址
  - method: 指定请求方法类型，如 GET ,POST,DELETE,PUT 等![image-20200831195355050](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200831195355050.png)

- consumes, produces

  - consumes：指定处理请求的提交内容类型(Context-Type)，只接收内容类型是哪种的请求 如 application/json，test/html
  - produces: 指定浏览器返回的内容类型， 仅当request请求头中的 (Accept)类型中包含该指定类型才返回

- params , headers

  - params: 规定请求参数 比如（`params={"username"}:发送请求的时候必须带上一个名为username的参数；没带都会404`） 或（ `params={"!username"} 发送请求的时候必须不携带上一个名为username的参数；带了都会404` ） 或 （ `{“param1=value1”, “param2”}: 请求必须包含名为 param1 和param2 的两个请求参数，且 param1 参数的值必须为 value1` ）

  - headers：规定请求头；也和params一样能写简单的表达式 如一个简单的 请求头：

    ```java
    	/**
    	 * User-Agent：浏览器信息；
    	 * 让火狐能访问，让谷歌不能访问
    	 * 
    	 * 谷歌：
    	 * User-Agent:Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.36
    	 * 火狐;
    	 * User-Agent	Mozilla/5.0 (Windows NT 6.3; WOW64; rv:34.0) Gecko/20100101 Firefox/34.0
    	 * @return
    	 * 
    	 */
    	@RequestMapping(value="/handle04",headers={"User-Agent=Mozilla/5.0 (Windows NT 6.3; WOW64; rv:34.0) Gecko/20100101 Firefox/34.0"})
    	public String handle04(){
    		System.out.println("handle04....");
    		return "success";
    	}
    ```

    

`RequestMapping` 可以模糊匹配 

- URL地址可以写模糊的通配符：

  - ？：能替代任意一个字符

  - *：能替代任意多个字符，和一层路径

  - **：能替代多层路径

  - ```java
       //@RequestMapping("/antTest0?")?匹配一个字符,0个多个都不行;
        // *匹配任意多个字符 @RequestMapping("/antTest0*")
        // *：匹配一层路径 @RequestMapping("/a/*/antTest01")
        //@RequestMapping("/a/**/antTest01")  匹配多层路径
        public  String myRequ(){
            return "hello";
        }
    ```

    

#### 1. 1`@PathVariable`  映射URL绑定的占位符

这个注解的作用是将请求URL中的模板变量映射到功能处理方法的参数上，即取出URL模板中的变量作为参数

如：![image-20200831204000990](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200831204000990.png)

```java
@Controller
@RequestMapping("hello02")
public class ReqMappingURLController {
    //路径上可以有占位符：  占位符 语法就是可以在任意路径的地方写一个{变量名}
    //   /user/xiaobo01    /user/xiaobo02
    // 路径上的占位符只能占一层路径
    @RequestMapping("/user/{userId}")
    public String pathVariableTest(@PathVariable("userId")String id){
        System.out.println("路径上的占位符的值"+userId);
        return "hello";
    }
}
```

#### 1.2 `@RequestParam` 获取请求参数的值

 SpringMVC如何获取请求带来的各种信息 默认方式获取请求参数： 直接给方法入参上写一个和请求参数名相同的变量。

这个变量就来接收请求参数的值；带：有值，没带：null；

如

```xml
<a href="hello01?username=viw">hello</a>
```

```java
@RequestMapping("/hello")
public String hello01(String username){
	System.out.println(username);
	return "";
}
```

这样就可以获取到请求参数



或者 使用 `@RequestParam`：获取请求参数的；参数默认是必须带的 不带就404；

如：

url： http:localhost:8080/user?user=aaa

```
@RequestMapping("/hello")
public String hello01(@RequestParam("user") String username){
	System.out.println(username);
	return "";
}
```

这样我们就可以获取到  user=aaa 的 值 `aaa`



`@RequestParam`有三个参数

![image-20200831221335282](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200831221335282.png)

- value：表示接受的传入参数类型
- required: 通过boolean 的值设置是否必须要传入参数
- defaultValue:表示设置默认值,没带是null  所以赋默认值

> 注意区别
>
>  `/book/【{user}pathvariable?【user=admin(requestparam)`
>
> @RequestParam("user") : 获取 ? 后 user的值 的admin
>
> @PathVariable("user")   ： 获取 路径中的 /book 后的 user

#### 1.3  ` @RequestHeader`

@RequestHeader：获取请求头中某个key的值；

```java
@RequestHeader(value = "User-Agent", required = false, defaultValue = "aaa") String userAgent
```

如 获取浏览器的值  request.getHeader("User-Agent")；

 @RequestHeader("User-Agent")String userAgent   等同于  userAgent = request.getHeader("User-Agent")

 如果请求头中没有这个值就会报错；

#### 1.4  `@CookieValue`

@CookieValue：获取某个cookie的值；

```java
@CookieValue(value="JSESSIONID",required=false)String jid
```

三个属性(和前面一样)：

![image-20200831222720906](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200831222720906.png)

#### 1.5 SpringMVC支持对 `POJO` 自动封装



当我们的请求参数是一个 POJO时， spinrgMVC自动为我们封装提交数据

对应POJO对象：

```java
public class Book{

}
```

表单：

```jsp
<form action="book" method="post">
	书名:<input type="text" name="bookName"/><br/>
	作者:<input type="text" name="author"/><br/>
	价格:<input type="text" name="price"/><br/>
	库存:<input type="text" name="stock"/><br/>
	销量:<input type="text" name="sales"/><br/>
	<hr/>
	省：<input type="text" name="address.province"/>
	市：<input type="text" name="address.city"/>
	街道:<input type="text" name="address.street"/><br/>
	<input type="submit"/>
</form>

```



```java
	/**
	 * 如果我们的请求参数是一个POJO；
	 * SpringMVC会自动的为这个POJO进行赋值？
	 * 1）、将POJO中的每一个属性，从request参数中尝试获取出来，并封装即可；
	 * 2）、还可以级联封装；属性的属性
	 * 3）、请求参数的参数名和对象中的属性名一一对应就行
	 * 
	 * 
	 * 提交的数据可能有乱码：
	 * 请求乱码：
	 * 		GET请求：改server.xml；在8080端口处URIEncoding="UTF-8"
	 * 		POST请求：
	 * 			在第一次获取请求参数之前设置
	 * 			request.setCharacterEncoding("UTF-8");
	 * 			自己写一个filter；SpringMVC有这个filter
	 * 			
	 * 响应乱码：
	 * 		response.setContentType("text/html;charset=utf-8")
	 * @param book
	 * @return
	 */
	@RequestMapping("/book")
	public String addBook(Book book){
		System.out.println("我要保存的图书："+book);
		return "success";
	}
```



**也可以使用原生API 进行处理(HttpServletRequest,HttpSession.....)**

如：

```java
@RequestMapping("handle01"){
	pulbic String handle01(HttpSession ses,HttpServletRequest req){
	req.setAttribute("requValue","请求值");
	ses.setAttribute("sesValue","session域值");
	return  "hello";
	}
}
```

`${requestScope.req}` && `${sessionScope.ses}` 取出即可测试

支持的原生API有

```java
/**
	 * SpringMVC可以直接在参数上写原生API;
	 * 
	 * HttpServletRequest
	 * HttpServletResponse
	 * HttpSession
	 * 
	 * 
	 * java.security.Principal
	 * Locale：国际化有关的区域信息对象
	 * InputStream：
	 * 		ServletInputStream inputStream = request.getInputStream();
	 * OutputStream：
	 * 		ServletOutputStream outputStream = response.getOutputStream();
	 * Reader：
	 * 		BufferedReader reader = request.getReader();
	 * Writer：
	 * 		PrintWriter writer = response.getWriter();
	 * 		
	 * @throws IOException 
	 * 
	 * 
	 */
```



###  2  `@ModelAndView`

![image-20200901200457631](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200901200457631.png)

## 3.REST 风格

### 介绍：

REST：即 Representational State Transfer。**（资源）表现层状态转化。**

-  **资源（Resources）**：网络上的一个实体，或者说是网络上的一个具体信息。

  它可以是一段文本、一张图片、一首歌曲、一种服务，总之就是一个具体的存在。

  可以用一个URI（统一资源定位符）指向它，每种资源对应一个特定的 URI 。

  获取这个资源，访问它的URI就可以，因此 URI 即为每一个资源的独一无二的识别符。

- **·** ***\*表现层（Representation）\****：把资源具体呈现出来的形式，叫做它的表现层（Representation）。比如，文本可以用 txt 格式表现，也可以用 HTML 格式、XML 格式、JSON 格式表现，甚至可以采用二进制格式。

- ***\*状态转化（State Transfer）\****：每发出一个请求，就代表了客户端和服务器的一次交互过程。HTTP协议，是一个无状态协议，即所有的状态都保存在服务器端。因此，如果客户端想要操作服务器，必须通过某种手段，让服务器端发生“状态转化”（State Transfer）

### 示例

对于相同的资源，就使用相同的URI，对资源的不同操作就使用不同的请求方式(如GET、POST、UPDATE和DELETE)。

相当于你发送的 `/user` 请求 ，请求方式是   GET  提交 就默认是查询操作 其他类似于

- **创建资源 ：** 使用**POST**去创建新的资源。
- **读取资源 ：** 使用**GET**去读取资源。 一定记住GET 请求永远不会改变资源的状态，GET方法具有**只读**语义。
- **更新资源 ：** 使用**PUT**替换资源。
- **删除资源 ：** 使用**DELETE**删除现有资源

```java
url地址起名 /资源名/资源标识符

/order/1  HTTP GET ：得到 id = 1 的 order   
/order/1  HTTP DELETE：删除 id = 1的 order   
/order/1  HTTP PUT：更新id = 1的 order   
/order     HTTP POST：新增 order 
```

### 问题：

浏览器 form 表单只支持 GET 与 POST 请求，而DELETE、PUT 等 method 并不支持，

Spring3.0 添加了一个过滤器，可以将这些请求转换为标准的 http 方法，使得支持 GET、POST、PUT 与 DELETE 请求。

### 简单入门测试

####  web.xml配置

```xml
<!-- 支持Rest风格的Filter -->
	<filter>
		<filter-name>HiddenHttpMethodFilter</filter-name>
		<filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
	</filter>
	<filter-mapping>
		<filter-name>HiddenHttpMethodFilter</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
```



#### 搭建环境

和上面一样

#### 编写对应页面

```jsp

<!-- 发起图书的增删改查请求；使用Rest风格的URL地址；
请求url	请求方式	表示含义
/book/1	GET：	查询1号图书
/book/1	DELETE：	删除1号图书
/book/1	PUT：	更新1号图书
/book	POST：	添加1号图书

从页面发起PUT、DELETE形式的请求?Spring提供了对Rest风格的支持
1）、SpringMVC中有一个Filter；他可以把普通的请求转化为规定形式的请求；配置这个filter;
	<filter>
		<filter-name>HiddenHttpMethodFilter</filter-name>
		<filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
	</filter>
	<filter-mapping>
		<filter-name>HiddenHttpMethodFilter</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
2）、如何发其他形式请求？
	按照以下要求；1、创建一个post类型的表单 2、表单项中携带一个_method的参数，3、这个_method的值就是DELETE、PUT

 -->
<a href="book/1">查询图书</a><br/>
<form action="book" method="post">
    <input type="submit" value="添加1号图书"/>
</form><br/>


<!-- 发送DELETE请求 -->
<form action="book/1" method="post">
    <!--_method 把post转换为delete-->
    <input name="_method" value="delete"/>
    <input type="submit" value="删除1号图书"/>
</form><br/>

<!-- 发送PUT请求 -->
<form action="book/1" method="post">
    <input name="_method" value="put"/>
    <input type="submit" value="更新1号图书"/>
</form><br/>
</body>
```

#### controller

```java
package com.study.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

/**
 * @Author: xiaobo
 * @Date: 2020/8/31 21:30
 */
@Controller
public class BookController {
    /**
     * 查询
     *
     * @param
     * @return
     */
    @RequestMapping(method = RequestMethod.POST, value = "/book")
    public String addBook() {
        System.out.println("addBook");
        return "hello";
    }
    @RequestMapping(method = RequestMethod.DELETE, value = "/book/{id}")
    public String delBook(@PathVariable("id") Integer id) {
        System.out.println("delBook" + id);
        return "hello";
    }
    @RequestMapping(method = RequestMethod.PUT, value = "/book/{id}")
    public String upBook(@PathVariable("id") Integer id) {
        System.out.println("upBook" + id);
        return "hello";
    }
    @RequestMapping(method = RequestMethod.GET, value = "/book/{id}")
    public String getBook(@PathVariable("id") Integer id) {
        System.out.println("upBook" + id);
        return "hello";
    }
}

```

#### 异常

tomcat版本问题可能导致 delete  和 put请求 不能处理  添加  `isErrorPage=true`

![image-20200831215544820](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200831215544820.png)



### 解决 `DELETE` & `PUT` 请求问题

 HiddenHttpMethodFilter

浏览器 form 表单只支持 GET 与 POST 请求，而DELETE、PUT 等 method 并不支持，

Spring3.0 添加了一个过滤器，可以将这些请求转换为标准的 http 方法，使得支持 GET、POST、PUT 与 DELETE 请求。

```java
public class HiddenHttpMethodFilter extends OncePerRequestFilter {
	
    //获取表单的值
	/** Default method parameter: {@code _method} */
	public static final String DEFAULT_METHOD_PARAM = "_method";
	
	private String methodParam = DEFAULT_METHOD_PARAM;
    
    @Override
	protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain)
			throws ServletException, IOException {

		HttpServletRequest requestToUse = request;
		// 为POST exception为null
		if ("POST".equals(request.getMethod()) && request.getAttribute(WebUtils.ERROR_EXCEPTION_ATTRIBUTE) == null) {
			String paramValue = request.getParameter(this.methodParam);
			if (StringUtils.hasLength(paramValue)) {
                // HttpMethodRequestWrapper 重写了getMethod
				requestToUse = new HttpMethodRequestWrapper(request, paramValue);
			}
		}

		filterChain.doFilter(requestToUse, response);
	}
    
    private static class HttpMethodRequestWrapper extends HttpServletRequestWrapper {

		private final String method;

		public HttpMethodRequestWrapper(HttpServletRequest request, String method) {
			super(request);
			this.method = method.toUpperCase(Locale.ENGLISH);
		}

		@Override
		public String getMethod() {
			return this.method;
		}
	}
```

所有我们的 l 参数名称必须叫做 `_method`

## 4.数据输出

### 1. Model & ModelMap & Map & ModelAndView

SpringMVC除过在方法上传入原生的request和session外还能使用 Model & ModelMap & Map & ModelAndView 数据带给页面



关系继承图：

![image-20200901195914336](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200901195914336.png)



#### 1 .第一种

可以在方法处传入Map、或者Model或者ModelMap

给这些参数里面保存的所有数据都会放在请求域中。可以在页面获取

Map，Model，ModelMap：最终都是BindingAwareModelMap在工作

相当于给BindingAwareModelMap中保存的东西都会被放在请求域中

#### 2.第二种

方法的返回值可以变为ModelAndView类型

既包含视图信息（页面地址）也包含模型数据（给页面带的数据）

而且数据是放在请求域（request）中





```java
/**
 * @Author: xiaobo
 * @Date: 2020/9/1 19:29
 */
@Controller
public class MyController {
    @RequestMapping("/hello1")
    public  String handle01(Map<String,Object> map){
        map.put("msg","map_output");
        System.out.println(MyController.class);
        return "success";
    }
    @RequestMapping("/hello2")
    public  String handle02(Model model){
        model.addAttribute("msg","model_output");
        System.out.println(MyController.class);
        return "success";
    }
    @RequestMapping("/hello3")
    public  String handle03(ModelMap map){
        map.addAttribute("msg","ModelMap_output");
        System.out.println(MyController.class);
        return "success";
    }
    /**
     * 返回值是ModelAndView;可以为页面携带数据
     * @return
     */
    @RequestMapping("/handle04")
    public ModelAndView handle04(){
        //之前的返回值我们就叫视图名；视图名视图解析器是会帮我们最终拼串得到页面的真实地址；
        //ModelAndView mv = new ModelAndView("success");
        ModelAndView mv = new ModelAndView();
        mv.setViewName("success");
        mv.addObject("msg", "你好哦！");
        return mv;
    }
}
```

#### 3第三种 给Session域中保存数据

使用 `@SessionAttributes` ,（只能标在类上）![image-20200901201040067](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200901201040067.png)



```java

 /*
 * SpringMVC提供了一种可以临时给Session域中保存数据的方式；
 * 	使用一个注解	@SessionAttributes(只能标在类上)
 * 	@SessionAttributes(value="msg"):
 * 		给BindingAwareModelMap中保存的数据，或者ModelAndView中的数据，
 * 		同时给session中放一份；
 * 		value指定保存数据时要给session中放的数据的key；
 * 
 * 	value={"msg"}：只要保存的是这种key的数据，给Session中放一份
 * 	types={String.class}：只要保存的是这种类型的数据，给Session中也放一份
 * 
 *  @SessionAttributes可能会引发异常；
 * 			给session中放数据请使用原生API；
 */
@SessionAttributes(value={"msg"},types={String.class})
@Controller
public class OutputController {
```

不建议的原因是因为你自定义类型取值 会从session取，导致可能有异常。` in sessoin not found`

### 2.  `@ModelAttribute` 注解

`@ModelAttribute`  也是 `BindingAwareModelMap`

l 在方法定义上使用 @ModelAttribute 注解：Spring MVC 在调用目标处理方法前，会先逐个调用在方法级上标注了 @ModelAttribute 的方法，相当于这个方法就会提前于目标方法先运行

```java
@ModelAttribute
	public void MyModelAttribute(Map<String, Object> map){
        map.put("bookMod", book);
 }    

```

l 在方法的入参前使用 @ModelAttribute 注解：可以从隐含对象中获取隐含的模型数据中获取对象，再将请求参数绑定到对象中，再传入入参

```java
@RequestMapping("/updateBook")
 	// @ModelAttribute("bookMod")  取出在前面保存的 bookMod 相当于 我们自己的实体类对象信息
	public String updateBook(@RequestParam(value="author")String author,
			Map<String, Object> model,
			HttpServletRequest request,
			@ModelAttribute("bookMod")Book book 
			){
        
    }
```



方法和类上标记的对象流程图

![image-20200901204853275](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200901204853275.png)



**处理全字段更新引发的问题**

- 1.表单更新时，有些属性不允许修改，用户只能修改部分字段，导致用户修改实体属性以后 原数据库字段数据被覆盖成null



**简单处理方法(保证全字段更新的时候，只更新了页面携带的数据)：**

1 让SpringMVC别创建book对象，直接从数据库中先取出一个要修改的对象的信息

2 使用刚才从数据库取出的book对象，给它 的里面设置值；（请求参数带了哪些值就覆盖之前的值）

3 带了的字段就改为携带的值，没带的字段就保持之前的值

4 将之前从数据库中查到的对象，并且封装了请求参数的对象。进行保存；

---

l 运行原理：

① 执行@ModelAttribute注解所修饰的方法，将从数据库中获取的对象存放到Map集合中，key为user

② SpringMVC从Map集合中获取 user对象，将表单数据封装到与参数名称对应的user对象属性上

③ SpringMVC将user对象作为参数，传递给目标方法。

④ 注意：@ModelAttribute 注解修饰的方法中，放入到Map集合中的key值，应该和目标方法参数类型的类名称首字母小写一致。

```java
  @ModelAttribute("hello") //返回值 Key 是 我们指定的 hello ，相当于这里我们可以自定义返回的key ，如果这里不指定 就使用 我们的返回值 首字母小写
    public void hahaMyModelAttribute(Map<String, Object> map){
......  
```



