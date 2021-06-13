[TOC]

#  SpringMVC

## 1.springMVC Restful风格案例

基本环同之前一样 springmvc-01 中的环境

- 1. 构建项目基本骨架  编写 `web.xml` , 然后里面编写我们的 dispatcherServlet 分发器 ，然后是我们的字符编码 ， 然后是增加  REST FILTER

     ```XML
      <!--REST 风格的 filter-->
         <filter>
             <filter-name>hiddenHttpMethodFilter</filter-name>
             <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
         </filter>
         <filter-mapping>
             <filter-name>hiddenHttpMethodFilter</filter-name>
             <url-pattern>/*</url-pattern>
         </filter-mapping>
     ```

     

  2. 编写我们的  `dispatcher-servlet.xml` ，然后编写我们的基本视图解析器 `InternalResourceViewResolver` 

### 1.Rest ful

- REST ful :四个请求发送 `GET（查询）  POST (增加) DELETE（删除）  PUT（更新）`

大概流程：

1.首页：

> 启动服务--> 跳转列表页面--> 遍历展示所有数据在页面

编写controller  发送emps 请求  跳转 list.html 展示所有数据

```java
/**
 * @Author: xiaobo
 * @Date: 2020/9/3 23:38
 */
@Controller //控制器
public class EmpController {
    @Autowired
    EmployeeDao employeeDao; 
    @RequestMapping("/emps")
    public String list(ModelMap modelMap){ //  ModelMap存放我们的 的数据给页面取出展示
       Collection<Employee> emps=  employeeDao.getAll(); // 获取所有数据
       modelMap.addAttribute("emps",emps); // 赋给 emps  页面提供 emps 取出数据
        return "list"; // 跳转 list.html 页面
    }
}
```

在 list.html 取出数据

```xml
<!--JSTL标签库-->
    <!-- https://mvnrepository.com/artifact/javax.servlet/jstl -->
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>jstl</artifactId>
      <version>1.2</version>
    </dependency>
```

```jsp
<!--遍历取出-->
<tr>.......</tr>
<tr>
        <c:forEach items="${emps}" var="emp">
        <td>${emp.id}</td>
            <td>.....</td>
            ......
        </c:forEach>
    </tr>
```



2.发送 POST 请求添加

规定 以POST请求方法 发送  `/emp`  添加

> 点击员工添加--(查出来所有部门)-> 跳转 添加页面---> 提交表单保存--> 服务端保存-->回去 列表页面



**使用 springMVC 表单标签来实现表单数据绑定**

```xml
<%@taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
```

```jsp
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>
</head>
<body>
<h1>员工添加</h1>
<!-- 
表单标签； 
	通过 SpringMVC的表单标签可以实现将模型数据中的属性和 HTML 表单元素相绑定，
	以实现表单数据更便捷编辑和表单值的回显
	1）、SpringMVC认为，表单数据中的每一项最终都是要回显的, 比如说我们这里的lastName 是不需要回显的，都是 用了 表单标签 我们的请求域编写有 lastName 属性，否则报错
	
		path指定的是一个属性；这个属性是从隐含模型（请求域中取出的某个对象中的属性）
		path指定的每一个属性，请求域中必须有一个对象，拥有这个属性
				这个对象就是请求域中的command
		modelAttribute=""：
		1)、以前我们表单标签会从请求域中获取一个command对象；把这个对象中的每一个属性对应的显示出来
		2)、让SpringMVC不要去取command的值了，我放了一个modelAttribute指定的值
			取对象用的key就用自定义modelAttribute指定的employee
-->
<%
	pageContext.setAttribute("ctp", request.getContextPath());
%>
<form:form action="${ctp }/emp" modelAttribute="employee" method="POST">
	<!-- path就是原来html-input的name项：需要写 
		path：
			1）、当做原生的name项
			2）、自动回显隐含模型中某个对象对应的这个属性的值
	-->
	lastName:<form:input path="lastName"/><br/>
	email:<form:input path="email"/><br/>
	gender:<br/>
		男：<form:radiobutton path="gender" value="1"/><br/>
		女：<form:radiobutton path="gender" value="0"/><br/>
	dept:
		<!-- 
		items=""：指定要遍历的集合 ；自动遍历；遍历出的每一个元素是一个department对象
		itemLabel="属性名"：指定遍历出的这个对象的哪个属性是作为option标签体的值
		itemValue="属性名"：指定刚才遍历出来的这个对象的哪个属性是作为要提交 的value值
		 -->
		<form:select path="department.id" 
			items="${depts }" 
			itemLabel="departmentName" 
			itemValue="id"></form:select><br/>
	<input type="submit" value="保存"/>
</form:form>
<!--原生-->
<!-- (Employee) -->
<%-- <form action="">
	lastName：<input type="text" name="lastName"/><br/>
	email:<input type="text" name="email"/><br/>
	gender：  <br/>
		男：<input type="radio" name="gender" value="1"/><br/>
		女：<input type="radio" name="gender" value="0"><br/>
	dept:
		<select name="department.id">
			<c:forEach items="${depts }" var="deptItem">
				<!-- 标签体中的是在页面的提示选项信息，value才是真正提交的值 -->
				<option value="${deptItem.id }">${deptItem.departmentName }</option>
			</c:forEach>
		</select>
	<input type="submit" value="提交"/>
</form> --%>
</body>
</html>
```

controller

```java

    /**
     * 去员工添加页面，去页面之前需要查出所有部门信息，进行展示的
     *
     * @return
     */
    @RequestMapping("/toaddpage")
    public String toAddPage(Model model) {
        // 1、先查出所有部门
        Collection<Department> departments = departmentDao.getDepartments();
        // 2、放在请求域中
        model.addAttribute("depts", departments);
        model.addAttribute("employee", new Employee());
        // 3、去添加页面
        return "add";
    }
    @RequestMapping(value = "/emp", method = RequestMethod.POST)
    public String addEmp(Employee employee) {
        System.out.println("要添加的员工：" + employee);
        employeeDao.save(employee);
        // 返回列表页面；重定向到查询所有员工的请求
        return "redirect:/emps";
    }
```



3.修改操作

点击修改操作--->  根据id查出数据放到请求域中  发送 GET  `/emp/{id}`--> 数据在页面回显--> 



> 编辑之前，先查出数据 放入隐藏域中，然后在页面取出来，编辑完提交时 以 `PUT` 方式提交，需要 <input type="hidden" name="_method" value="put"/> 来 提交编辑表单。springMVC帮助我们以 PUT方式 发送 提交表单 ，从而实现了我们的 Rest ful 风格的 修改操作 ，

```java
    // 在跳转编辑页面之前 查询数据放到请求域
@RequestMapping(value = "/emp/{id}", method = RequestMethod.GET)
public String getEmp(@PathVariable("id") Integer id, Model model) {
    // 1、查出员工信息
    Employee employee = employeeDao.get(id);
    // 2、放在请求域中
    model.addAttribute("employee", employee);
    // 3、继续查出部门信息放在隐含模型中
    Collection<Department> departments = departmentDao.getDepartments();
    model.addAttribute("depts", departments);
    return "edit";
}
```

> 编辑页面，我们以 PUT发送提交数据，满足我们的Rest ful 风格请求，为了防止在编辑以后提交表单 id =ull 的情况，增加了一个隐藏域  `<input type="hidden" name="id" value="${employee.id }"/>` 把在编辑之前查询处理的 emp对象的id属性也放到请求域中，然后把放入了隐藏域中，那么我在编辑操作以后提交表单时 就不会出现id为 null的情况。 查出 emp对象的 dept 对象的name属性，提交值以 dept 的id 提交 也就是部门信息，遍历取出部门信息填充进页面 

```xml
<!-- modelAttribute：这个表单的所有内容显示绑定的是请求域中 employee的值-->
<form:form action="${ctp }/emp/${employee.id }" 
   modelAttribute="employee" method="post">
   <input type="hidden" name="_method" value="put"/>
   <input type="hidden" name="id" value="${employee.id }"/>
   email:<form:input path="email"/><br/>
   gender:&nbsp;&nbsp;&nbsp;
      男:<form:radiobutton path="gender" value="1"/>&nbsp;&nbsp;&nbsp;
      女:<form:radiobutton path="gender" value="0"/><br/>
   dept:
      <form:select path="department.id" items="${depts }"
         itemLabel="departmentName" itemValue="id"></form:select>
         <br/>
   <input type="submit" value="修改"/>
</form:form>
```

> 需要在编辑之前先查询出数据

```java
 // 经过查询出来的数据在页面回显后，进行编辑操作，要 使用 @ModelAttribute 查询在 请求域中的emp对象信息，
    // @ModelAttribute 标记的参数会先与该类的所有方法运行，
    @RequestMapping(value = "/emp/{id}", method = RequestMethod.PUT)
    public String updateEmp(@ModelAttribute("employee")Employee employee/* ,@PathVariable("id")Integer id */) {
        System.out.println("要修改的员工：" + employee);
        // xxxx 更新保存二合一；
        // 提交编辑后的表单 保存 再回到 列表页
        employeeDao.save(employee);
        return "redirect:/emps";
    }
```

> 所有 `@ModelAttribute` 先于该类所有方法之前先执行，

```java
 //  @ModelAttribute 标记的参数会先与该类的所有方法运行， 参数是请求域的id ， required 是否一定存在
    @ModelAttribute
    public void myModelAttribute(
            @RequestParam(value = "id", required = false) Integer id, Model model) {
        // id为null 不查询，是 /emps 请求 ，带id 的 是GET  DELETE PUT 请求
        // 这些都带 id  ,防止 空指针异常，如果没有这个判断 在add 时 因为这个方法
        // 会先于 该类所有方法之前执行，而在添加操作之前 请求域中是没有这个emp对象
        if (id != null) {
            // 在编辑表单 提交之前 根据 发送的  value = "/emp/{id}", method = RequestMethod.PUT
            // 中带的id(这个id也就是我们之前放入隐藏域中的) 查询出 emp对象
            Employee employee = employeeDao.get(id);
            // key : employee   value:  employee 我们提前查询出来的数据 放入model中， 这样就实现了 在编辑提交表单 时 不丢数据，主要是id
            model.addAttribute("employee", employee);
        }
    }
```



4.删除操作

> 以 DELETE 发送 `/emp/{id}`  删除

这些涉及到 引入 静态资源时 被  `DispatcherServlet` 拦截，

在 中 加入 `<mvc:default-servlet-handler/>` 和 `<mvc:annotation-driven></mvc:annotation-driven>` 这样就让 springMVC 无法处理的请求 提交 tomcat容器处理

> 删除

```java
	@RequestMapping(value="/emp/{id}",method=RequestMethod.DELETE)
	public String deleteEmp(@PathVariable("id")Integer id){
		employeeDao.delete(id);
		return "redirect:/emps";
	}
```

> 页面中 

```jsp
<c:forEach items="${emps }" var="emp">
		<tr>
			<td>${emp.id }</td>
			<td>${emp.lastName}</td>
			<td>${emp.email }</td>
			<td>${emp.gender==0?"女":"男" }</td>
			<td>${emp.department.departmentName }</td>
			<td><a href="${ctp }/emp/${emp.id }">edit</a></td>
			<td><a href="${ctp }/emp/${emp.id }" class="delBtn">delete</a></td>
		</tr>
	</c:forEach>
</table>
<a href="${ctp }/toaddpage">添加员工</a>
<!--抽取出来 删除事件-->
<form id="deleteForm" action="${ctp }/emp/${emp.id }" method="post">
	<input type="hidden" name="_method" value="DELETE" /> 
</form>
<script type="text/javascript">
	$(function(){
		$(".delBtn").click(function(){
			
			//1、改变表单的action指向
			$("#deleteForm").attr("action",this.href);
			//2、提交表单
			$("#deleteForm").submit();
			return false;
		});
	});
</script>
```



## 2.数据绑定&数据转换& 数据校验

### 1.问题

页面在提交数据时(比如表单提交添加数据)的数据类型和javaBean的数据类型要一一绑定，就涉及到以下几个方面：

- 1.数据绑定期间的数据类型转换
- 2.数据绑定期间的数据格式化问题
- 3.数据校验(添加的数据需要要合法性)
  - 1）前端检验， js+正则表达式
  - 2）后端检验

> 重点关注 `ModelAttributeMethodProcessor` 中的 `resolveArgument`方法
>
> ```java
> if (bindingResult == null) {
> 			// Bean property binding and validation;
> 			// skipped in case of binding failure on construction.
>     		// 数据绑定factoty 创建的databinder对象
> 			WebDataBinder binder = binderFactory.createBinder(webRequest, attribute, name);
> 			if (binder.getTarget() != null) {
> 				if (!mavContainer.isBindingDisabled(name)) {
> 					bindRequestParameters(binder, webRequest);
> 				}
> 				validateIfApplicable(binder, parameter);
> 				if (binder.getBindingResult().hasErrors() && isBindExceptionRequired(binder, parameter)) {
> 					throw new BindException(binder.getBindingResult());
> 				}
> 			}
> 			// Value type adaptation, also covering java.util.Optional
> 			if (!parameter.getParameterType().isInstance(attribute)) {
> 				attribute = binder.convertIfNecessary(binder.getTarget(), parameter.getParameterType(), parameter);
> 			}
> 			bindingResult = binder.getBindingResult();
> 		}
> ```
>
> `ConversionService` 接口 ，这是一个数据类型转换 service，默认的是 `DefaultFormattingConversionService` ，我们自定义实现接口就使用  `DefaultConversionService`
>
> ![image-20200906151036451](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200906151036451.png)
>
> 

涉及到的几个重要组件：

-  `WebDataBinder`  ： 数据绑定器复制数据绑定操作
- `ConversionService` : 负责数据类型的转换以及格式化功能
- `validatoes` ： 数据校验
- `bingResult` 负责保存数据检验期间的错误

### 2.流程

① Spring MVC 将 ServletRequest  对象及目标方法的入参实例传递给 WebDataBinderFactory 实例，以创建 ***\*DataBinder\**** 实例对象

② DataBinder 调用装配在 Spring MVC 上下文中的 ***\*ConversionService\**** 组件进行***\*数据类型转换、数据格式化\****工作。将 Servlet 中的请求信息填充到入参对象中

③ 调用 ***\*Validator\**** 组件对已经绑定了请求消息的入参对象进行数据合法性校验，并最终生成数据绑定结果 ***\*BindingData\**** 对象

④ Spring MVC 抽取 ***\*BindingResult\**** 中的入参对象和校验错误对象，将它们赋给处理方法的响应入参



Spring MVC 通过反射机制对目标处理方法进行解析，将请求消息绑定到处理方法的入参中。

最重要的是 `DataBinder`![image-20200906152332646](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200906152332646.png)

### 3.自定义类型转换

场景：把指定的自定义字符串解析成javaBean对象，

因为在 `ConversionService` 组件中有很多的  converter， 不同的类型转换和格式化就用不同的 convert，

Spring 定义了 3 种类型的转换器接口，实现任意一个转换器接口都可以作为自定义转换器注册到 ConversionServiceFactroyBean 中：

**实现Converter接口步骤：**

- 1.实现converter接口，写自定义类型的转换器

  ```java
  public class StringToEmpConverter implements Converter<S, T> {
       @Override
      public T convert(S source) {
          return null;
      }
  }
  ```

  

- 2. converter是 conversionService中的组件（需要把converter放入 conversionService）配置conversionService 织件(将 webDataBinder中的 conversionService设置成我们自定义类型的转换器 conversionService)

     ```xml
      
     <!--
         <mvc:annotation-driven conversion-service=“conversionService”/>
         会将自定义的 ConversionService 注册到 Spring MVC 的上下文中
         -->
         <mvc:annotation-driven conversion-service="conversionService"></mvc:annotation-driven>
     <!--配置conversionService-->
         <bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
             <property name="converters">
                     <set>
                         <!--自定义类型转换器注册到容器中-->
                         <bean class="com.study.component.StringToEmpConverter"></bean>
                     </set>
             </property>
         </bean>
     ```



案例代码：

> 页面

```jsp
   <form action="${ctp}/quickadd">
        <!--value就是我们自定义输入的值,自动封装成javaBean-->
        <input name="empinfo" value="empname-admin@qq.com-1-101"/>
        <input type="submit" value="快速添加"/>
    </form>
```



> 处理请求方法

```java
/**
 * 自定义我们的数据转换器   把我们在页面输入的 empinfo参数 name-email-gender-deptId 这一串字符串转化成 javaBean属性
 * @param employee
 * @return
 */
@RequestMapping("/quickadd")
public String quickAdd(@RequestParam("empinfo") Employee employee) { //  @RequestParam("empinfo") 绑定请求区中的empinfo属性
    // System.out.println("封装：" + employee);
    employeeDao.save(employee);
    return "redirect:/emps";
}
```

> 注册到IOC容器 

```xml
 
<!--
    <mvc:annotation-driven conversion-service=“conversionService”/>
    会将自定义的 ConversionService 注册到 Spring MVC 的上下文中
    -->
    <mvc:annotation-driven conversion-service="conversionService"></mvc:annotation-driven>

<!--配置conversionService-->
    <bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
        <property name="converters">
                <set>
                    <!--自定义类型转换器注册到容器中-->
                    <bean class="com.study.component.StringToEmpConverter"></bean>
                </set>
        </property>
    </bean>
```



总结：1.实现converter接口，做一个自定义类型的转换器

​			2.将这个converter配置在 conversionService

​			3.加载到容器中，让MVC加载使用 我们自定义的 converter

​		

### 4. `annotation-driven标签解析`

作用：

- `<mvc:annotation-driven />` 会自动注册RequestMappingHandlerMapping 、RequestMappingHandlerAdapter 与 ExceptionHandlerExceptionResolver  三个bean。

- 支持使用 ConversionService 实例对表单参数进行类型转换
- 支持使用 @NumberFormat annotation、@DateTimeFormat 注解完成数据类型的格式化
- 支持使用 @Valid 注解对 JavaBean 实例进行 JSR 303 验证
- 支持使用 @RequestBody 和 @ResponseBody 注解



![image-20200906205323108](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200906205323108.png)



> `AnnotationDrivenBeanDefinitionParser` bean标签解析，添加组件到容器中

```java
class AnnotationDrivenBeanDefinitionParser implements BeanDefinitionParser {
    @Override
	@Nullable
	public BeanDefinition parse(Element element, ParserContext parserContext) {
		......

		RootBeanDefinition bindingDef = new RootBeanDefinition(ConfigurableWebBindingInitializer.class);
		bindingDef.setSource(source);
		bindingDef.setRole(BeanDefinition.ROLE_INFRASTRUCTURE);
		bindingDef.getPropertyValues().add("conversionService", conversionService);
		bindingDef.getPropertyValues().add("validator", validator);
		bindingDef.getPropertyValues().add("messageCodesResolver", messageCodesResolver);
        .....

  
```

 

**功能分析：**

1.比如  （1）表示 `<mvc:default-servlet-handler/>` + （2）表示`<mvc:annotation-driven></mvc:annotation-driven>`

- 1.如果都没有 只有被 `@RequestMapping映射的资源可以访问，静态资源不可以访问`，`重点关注HandlerMapping`和 `handlerAdapter`
  - 动态资源可以访问，是因为 `DefaultAnnotationHandlerMapping`中的 handlerMap中保存了每一个资源的映射信息
  - 静态资源不可以访问， handlerMap 没保存静态资源的映射请求
  - `HandlerAdapter` 执行目标方法适配器，
- 2. （1）加上以后，我们通过断点调试，`SimpleUrlHandlerMapping` 生效，这个 handlerMapping 把请求转给 tomcat处理，而我们在web.xml中 写的是 所有请求放行，所以我们的静态资源才可以访问，而因为 原 `DefaultAnnotionHandlerMapping` 不生效所有动态资源不可以访问，而 `AnnotationMethodHandlerAdapter` 在处理请求过程中不存在，
- 3. （1）（2）都加上，都可以访问，
     1. 在handleMapping中，新出现 `RequestMappingHandlerMapping`,它的 handlerMethods 保存了每一个请求所处理的方法的信息(处理请求相当于的方法)
     2. 而且 `RequestMappingHandlerMapping` 的 handlerAdapter 中 使用了 `RequestMappingHandlerAdapter` 



### 5.日期格式化

场景：如果提交的表单日期数据类型不正确，就提示数据不正确，400

对属性对象的输入/输出进行格式化，从其本质上讲依然属于 “类型转换” 的范畴。
Spring 在格式化模块中定义了一个实现 ConversionService 接口的 FormattingConversionService 实现类，该实现类扩展了 GenericConversionService，因此它既具有类型转换的功能，又具有格式化的功能
FormattingConversionService 拥有一个 FormattingConversionServiceFactroyBean 工厂类，后者用于在 Spring 上下文中构造前者

FormattingConversionServiceFactroyBean 内部已经注册了 :
NumberFormatAnnotationFormatterFactroy：支持对数字类型的属性使用 @NumberFormat 注解
JodaDateTimeFormatAnnotationFormatterFactroy：支持对日期类型的属性使用 @DateTimeFormat 注解
装配了 FormattingConversionServiceFactroyBean 后，就可以在 Spring MVC 入参绑定及模型数据输出时使用注解驱动了。<mvc:annotation-driven/> 默认创建的 ConversionService 实例即为 FormattingConversionServiceFactroyBean 



@DateTimeFormat 注解可对 java.util.Date、java.util.Calendar、java.long.Long 时间类型进行标注：
pattern 属性：类型为字符串。指定解析/格式化字段数据的模式，如：”yyyy-MM-dd hh:mm:ss”
iso 属性：类型为 DateTimeFormat.ISO。指定解析/格式化字段数据的ISO模式，包括四种：ISO.NONE（不使用） -- 默认、ISO.DATE(yyyy-MM-dd) 、ISO.TIME(hh:mm:ss.SSSZ)、  ISO.DATE_TIME(yyyy-MM-dd hh:mm:ss.SSSZ)
 style 属性：字符串类型。通过样式指定日期时间的格式，由两位字符组成，第一位表示日期的格式，第二位表示时间的格式：S：短日期/时间格式、M：中日期/时间格式、L：长日期/时间格式、F：完整日期/时间格式、-：忽略日期或时间格式



> 重点关注 `conversionServiceFactoryBean` ，创建的 conversionService 使用了大量的数据格式化（format）和数据转换器(converter)



> 使用 `FormattingConversionServiceFactoryBean`

```xml
 <!--
    <mvc:annotation-driven conversion-service=“conversionService”/>
    会将自定义的 ConversionService 注册到 Spring MVC 的上下文中
    -->
    <mvc:annotation-driven conversion-service="conversionService"></mvc:annotation-driven>
    <!-- 告诉SpringMVC别用默认的ConversionService，
             而用我自定义的ConversionService、可能有我们自定义的Converter； -->
    <!-- 以后写自定义类型转换器的时候，就使用FormattingConversionServiceFactoryBean来注册；
    既具有类型转换还有格式化功能 -->
    <bean id="conversionService" class="org.springframework.format.support.FormattingConversionServiceFactoryBean">

    <property name="converters">
                <set>
                    <!--自定义类型转换器注册到容器中-->
                    <bean class="com.study.component.StringToEmpConverter"></bean>
                </set>
        </property>
    </bean>
```



> 规定页面提交的日期格式

```java
@DateTimeFormat( pattern = "yyyy-MM-dd")
	private  Date birth;
```



### 6.数值格式化

@NumberFormat 可对类似数字类型的属性进行标注，它拥有两个互斥的属性：
style：类型为 NumberFormat.Style。用于指定样式类型，包括三种：Style.NUMBER（正常数字类型）、 Style.CURRENCY（货币类型）、 Style.PERCENT（百分数类型）
pattern：类型为 String，自定义样式，如patter="#,###"；

> 把页面提交的数值格式化

```java
	@NumberFormat(pattern = "#,###.##")
	private Double sale;
```



### 7. 数据校验

1.前端数据校验：
使用JS代码进行的前端数据校验
可以使用前端数据校验的框架：

2.后端数据校验
主要使用JAVA代码进行数据校验
后端数据校验是有框架的：Java自己做了一个后端数据校验的规则——BeanValidation
BeanValidation后端数据校验规则由Hibernate所在公司实现

SpringMVC中使用BeanValidation进行后端校验的，BeanValidation是Java中定义的一种后端数据校验的规范，这个规范由Hibernate实现。



JSR303:

JSR 303 (Java Specification Requests意思是Java 规范提案)通过***\*在 Bean 属性上标注\****类似于 @NotNull、@Max 等标准的注解指定校验规则，并通过标准的验证接口对 Bean 进行验证

![img](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/wps1.jpg)

***\*Hibernate Validator\**** 是 JSR 303 的其中一个参考实现，除支持所有标准的校验注解外，它还支持以下的扩展注解

![img](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/wps2.jpg)

#### 使用 Hibernate Validator数据校验

> 1.pom.xml 导入依赖

```xml
    <!-- https://mvnrepository.com/artifact/org.hibernate.validator/hibernate-validator -->
    <dependency>
      <groupId>org.hibernate.validator</groupId>
      <artifactId>hibernate-validator</artifactId>
      <version>6.0.14.Final</version>
    </dependency>
```



> 实例化，注册组件

```xml
 <!--开启spring mvc 注解启动， 开启mvc组件-->
    <mvc:annotation-driven validator="hibernateValidator"></mvc:annotation-driven>
    <!--实例化hibernateValidator-->
    <bean id="hibernateValidator" class="org.springframework.validation.beanvalidation.LocalValidatorFactoryBean">
        <property name="providerClass">
            <value>org.hibernate.validator.HibernateValidator</value>
        </property>
    </bean>
```



> javaBean属性添加注解

```java
	@NotNull
	@Length(min = 2,max = 18)
	private String lastName;

	@Email
	private String email;
```



> 给需要验证的处理请求的方法中的参数添加  `@Valid` 表明 在封装该对象时按照指定的数据校验规则校验

```java
 public String addEmp(@Valid Employee employee) {
 }
```

> 给需要校验的javaBean紧跟（之间不要有其他参数隔开） `BindingResult` 封装数据前面javaBean校验结果

```java
 public String addEmp(@Valid Employee employee, BindingResult result) {
 }
```



> 在bindingResult中有以下方法判断是否有输入错误

![image-20200906223223346](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200906223223346.png)

>处理结果

```java
@RequestMapping(value = "/emp", method = RequestMethod.POST)
public String addEmp(@Valid Employee employee, BindingResult result) {
    System.out.println("要添加的员工：" + employee);
    boolean hasErrors = result.hasErrors();
    if (!hasErrors) {
        employeeDao.save(employee);
        // 返回列表页面；重定向到查询所有员工的请求
        return "redirect:/emps";
    } else {
        System.out.println("数据校验错误");
        return "redirect:add";
    }
}
```

> 前端页面 数据校验提示信息 `<form:errors path="xxx"/>` 提示你输入数据不合法，比如输入的字段太长，

```jsp
lastName:<form:input path="lastName"/><br/><form:errors path="lastName"/>
```

#### 原生表单数据校验显示错误信息

> 在处理方法中 取出所有错误信息 ，放到请求域中

```java
.... 
List<FieldError> allErrors = result.getFieldErrors();
        for ( FieldError fieldError :  allErrors){
            System.out.println(fieldError.getField());
            System.out.println(fieldError.getDefaultMessage());
            System.out.println(fieldError.getArguments());
              modelMap.addAttribute("error"+fieldError.getDefaultMessage());
        }
.....
```

> 在页面中取出

```jsp
${error}
```



#### 自定义国际化错误信息

l 每个属性在数据绑定和数据校验发生错误时，都会生成一个对应的 FieldError 对象。

当一个属性校验失败后，校验框架会为该属性生成 4 个消息代码\****，这些代码以***\*校验注解类名为前缀，结合 ***\*modleAttribute、属性名及属性类型名\****生成多个对应的消息代码：例如 User 类中的 password 属性标注了一个 @Pattern 注解，当该属性值不满足 @Pattern 所定义的规则时, 就会产生以下 4 个错误代码：

- Pattern.user.password

- Pattern.password

- Pattern.java.lang.String

- Pattern

当使用 Spring MVC 标签显示错误消息时， Spring MVC 会查看 WEB 上下文是否装配了对应的国际化消息，如果没有，则显示默认的错误消息，否则使用国际化消息。

若数据类型转换或数据格式转换时发生错误，或该有的参数不存在，或调用处理方法时发生错误，都会在隐含模型中创建错误消息。其错误代码前缀说明如下：

-  ***\*required\****：必要的参数不存在。如 @RequiredParam(“param1”) 标注了一个入参，但是该参数不存在

-  ***\*typeMismatch\****：在数据绑定时，发生数据类型不匹配的问题

-  ***\*methodInvocation\****：Spring MVC 在调用处理方法时发生了错误



hibernateValidator 封装了自己的国际化错误信息，我们使用自定义来定制

> 编写自己的 国际化错误文件

![image-20200906225700983](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200906225700983.png)

> 注意错误 key ，错误代码，每一个错误发生都会有自己的错误代码，国际化文件中错误信息的key必须对应一个错误代码![image-20200906230209003](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200906230209003.png)



```properties
# key= value   key 就是错误代码。 value 就是提示
# 只要是所有邮箱不合法都提示
Email.email= email error !!
# 被标记不能为空的为空 就提示
NotEmpty =  not emoty !!
# 类型错误提示，
Length.java.lang.String= length error!!
....
```

> springMVC管理国际化资源文件

```xml
<!--管理国际化资源文件-->
<bean id="messageSource" class="org.springframework.context.support.ResourceBundleMessageSource">
    <property name="basename" value="conf.error"></property>
</bean>
```



小贴士： 如果不知错误的key写什么，就写把 错误信息打印到 console 中，然后找一个key就行



> 动态传入错误信息参数

```properties
Length.java.lang.String= length error!! {0} {1} {2}
```



> message 指定错误信息  注解的属性 指定错误信息

```java
	@NotNull(message = "不能为空")
	@Length(min = 2,max = 18)
	private String lastName;

```

