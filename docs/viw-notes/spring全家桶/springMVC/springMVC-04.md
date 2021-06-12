[TOC]

# springMVC-04

## 1.使用springMVC快速响应ajax



对于服务端来说，响应ajax， 只要是返回的是json数据即可



> 依赖

```xml
<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core -->
<dependency>
  <groupId>com.fasterxml.jackson.core</groupId>
  <artifactId>jackson-core</artifactId>
  <version>2.10.2</version>
</dependency>
<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-annotations -->
<dependency>
  <groupId>com.fasterxml.jackson.core</groupId>
  <artifactId>jackson-annotations</artifactId>
  <version>2.10.2</version>
</dependency>
```



> 使用 ` @ResponseBody` 将返回的数据放在响应体，如果是对象，转为json格式

```java
/**
将返回的数据放在响应体中
如果是对象，自动转为 json格式
 * @Author: xiaobo
 * @Date: 2020/9/6 23:20
 */
@Controller
public class AjaxController {

    @Autowired
    EmployeeDao employeeDao;
    @ResponseBody
    @RequestMapping("/getall")
    public Collection<Employee> ajaxGetAll(){
        Collection<Employee> all = employeeDao.getAll();
        return  all;
    }
}

```



> 注解属性, 如 使用 `@JsonFormat(pattern = "yyyy-MM-dd")`来规定返回json数据的日期是什么格式的

```java
	@Past
	@DateTimeFormat( pattern = "yyyy-MM-dd")
	@JsonFormat(pattern = "yyyy-MM-dd")
	private  Date birth = new Date();
```



> 页面上取出数据
>
> ```js
> // 原生ajax
> function({
> $.ajax({
> url:"/getallajax",
> type:"GET",
> success:function(data){
> console.log(data);
> }
> })
> 
> })
> ```
>
> 

```jsp
<a href="${ctp }/getallajax">ajax获取所有员工</a><br/>

<div>
</div>
<script type="text/javascript">
	$("a:first").click(function(){
		//1、发送ajax获取所有员工上
		$.ajax({
			url:"${ctp}/getallajax",
			type:"GET",
			success:function(data){
				//console.log(data);
				$.each(data,function(){
					var empInfo = this.lastName+"-->"+this.birth+"--->"+this.gender;
					$("div").append(empInfo+"<br/>");
				});
			}
		});
		
		return false;
	});
</script>
```



> 处理json：1.引入依赖 2. 编写目标方法 3. 在方法上添加 `@ResponseBody`





## 2.  `@RequestBody` 获取请求体

 ` @ResponseBody`  返回json数据

`@requestBody` 获取一个请求的请求体

`@requestParam` 获取请求参数

> 页面表单 post请求

```jsp
<body>
	<form action="${ctp }/testRequestBody" method="post"
		enctype="multipart/form-data">
		<input name="username" value="tomcat" /> <input name="password"
			value="123456"> <input type="file" name="file" /> <input
			type="submit" />
	</form>
	<a href="${ctp }/testRequestBody">ajax发送json数据</a>
</body>
```



> 处理请求的controller

```java

	/**
	 *  @RequestBody:请求体；获取一个请求的请求体
	 *  @RequestParam:
	 *  
	 *  @ResponseBody：可以把对象转为json数据，返回给浏览器
	 *  
	 *  @RequestBody：接受json数据，封装为对象
	 * @return
	 */   
@RequestMapping("/testRequestBody")
    public String RequestBody(@RequestBody String body){
        System.out.println(body);
        return "success";
    }
```





## 3.发送json数据给服务器

发送json数据给服务器，通过 @RequestBody 映射 和封装成我们的javaBean对象

> 页面请求

```js
<script type="text/javascript">
	$("a:first").click(function() {
		//点击发送ajax请求，请求带的数据是json
		var emp = {
			lastName : "张三",
			email : "aaa@aa.com",
			gender : 0
		};
		//alert(typeof emp);
		//js对象
		var empStr = JSON.stringify(emp);
		//alert(typeof empStr);
		$.ajax({
			url : '${ctp}/testRequestBody',
			type : "POST",
			data : empStr,
			contentType : "application/json",
			success : function(data) {
				alert(data);
			}
		});
		return false;
	});
</script>
```

> 处理请求

```java
@RequestMapping("/testRequestBody")
    public String RequestBody(@RequestBody String body){
        System.out.println(body);
        return "success";
    }
```

## 4.HttpEntity获取请求头

在参数位置写  `HttpEntity<T>` 获取请求头

```java
	/**
	 * 如果参数位置写HttpEntity<String>  str;
	 * 比@RequestBody更强，可以拿到请求头；
	 *  @RequestHeader("") 只是获取每一个请求头
	 * 
	 * @param str
	 * @return
	 */
	@RequestMapping("/test02")
	public String test02(HttpEntity<String>  str){
		System.out.println(str);
		return "success";
	}
```

> `HttpEntity`  获取请求体还可以获取请求头



## 5. `ResponseEntity` 

使用 `@ResponseBody` 直接把数据返回在响应体中，

`ResponseEntity`  能返回响应数据和响应体

> 返回响应的数据内容，还有自定义的数据格式内容

```java
/**
 * 将返回数据放在响应体中
 * 
 * ResponseEntity<String>：响应体中内容的类型
 * @return
 */
//@ResponseBody
@RequestMapping("/haha")
public ResponseEntity<String> hahah(){
	MultiValueMap<String, String> headers = new HttpHeaders();
	String body = "<h1>success</h1>";
	headers.add("Set-Cookie", "username=hahahaha");//在浏览器的响应头中
	return new ResponseEntity<String>(body , headers, HttpStatus.OK);
}
```


## 6. 设置响应头实现文件下载

文件下载重点是在响应头，通过设置 `ResponseEntity` 设置响应头来完成

```java
/**
	 * SpringMVC文件下载需要设置相应响应内容
	 * `ResponseEntity`  响应数据还可以设置响应头
	 * @param request
	 * @return
	 * @throws Exception
	 */
	@RequestMapping("/download")
	public ResponseEntity<byte[]> download(HttpServletRequest request) throws Exception{
		
		//1、得到要下载的文件的流；
		ServletContext context = request.getServletContext();
		//找到要下载的文件的真实路径
        String realPath = context.getRealPath("/xxx");
		FileInputStream is = new FileInputStream(realPath);
		
		byte[] tmp = new byte[is.available()];
		is.read(tmp);
		is.close();
		
		//2、将要下载的文件流返回
        // 响应头实例化
		HttpHeaders httpHeaders = new HttpHeaders();
		// 设置响应头
        httpHeaders.set("Content-Disposition", "attachment;filename="+"xxx");
		
		return new ResponseEntity<byte[]>(tmp, httpHeaders, HttpStatus.OK);
	}
```



## 7. `HttpMessageConverter<T>`

 `HttpMessageConverter<T>`： 负责将请求信息转换为一个对象（类型为 T），将对象（类型为 T）输出为响应信息

```java
public interface HttpMessageConverter<T> {
    //指定转换器可以读取的对象类型，即转换器是否可将请求信息转换为 clazz 类型的对象，同时指定支持 MIME 类型(text/html,applaiction/json等),根据请求头类型判断是否可读取
    boolean canRead(Class<?> clazz, @Nullable MediaType mediaType);
    //指定转换器是否可将 clazz 类型的对象写到响应流中，响应流支持的媒体类型在MediaType 中定义。

    boolean canWrite(Class<?> clazz, @Nullable MediaType mediaType);
    // 该转换器支持的类型
    List<MediaType> getSupportedMediaTypes();
    
    // 可读就调用 将请求信息流转换为 T 类型的对象。 HttpInputMessage 内部是 inputStream 返回消息体主体作为输入流，浏览器提交的内容
    T read(Class<? extends T> clazz, HttpInputMessage inputMessage)
			throws IOException, HttpMessageNotReadableException;
    // 可写就调用 将T类型的对象写到响应流中，同时指定相应的MediaType类型为 contentType。  HttpOutputMessage 返回消息的主体作为输出流，发送给浏览器内容
    void write(T t, @Nullable MediaType contentType, HttpOutputMessage outputMessage)
			throws IOException, HttpMessageNotWritableException;
}
```

![image-20200907220520707](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200907220520707.png)



> 发送的请求是以报文形式，封装成输入流转换成 实体对象，（浏览器的流转为了对象流）
>
> 输出也是如此，

![image-20200907221753132](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200907221753132.png)



> 主要实现类，一个converter 处理一种类型的数据
>
> `MappingJackson2HttpMessageConverter` 可以处理对象类型，转为json格式数据，
>
> 如 
>
> ```java
> // 读取和写入字节数组
> public class ByteArrayHttpMessageConverter extends AbstractHttpMessageConverter<byte[]> {
> 
> 	/**
> 	 * Create a new instance of the {@code ByteArrayHttpMessageConverter}.
> 	 */
> 	public ByteArrayHttpMessageConverter() {
> 		super(new MediaType("application", "octet-stream"), MediaType.ALL);
> 	}
> 
> 	// 支持的类型
> 	@Override
> 	public boolean supports(Class<?> clazz) {
> 		return byte[].class == clazz;
> 	}
> 
> 	@Override
> 	public byte[] readInternal(Class<? extends byte[]> clazz, HttpInputMessage inputMessage) throws IOException {
> 		long contentLength = inputMessage.getHeaders().getContentLength();
> 		ByteArrayOutputStream bos =
> 				new ByteArrayOutputStream(contentLength >= 0 ? (int) contentLength : StreamUtils.BUFFER_SIZE);
> 		StreamUtils.copy(inputMessage.getBody(), bos);
> 		return bos.toByteArray();
> 	}
> 
> 	@Override
> 	protected Long getContentLength(byte[] bytes, @Nullable MediaType contentType) {
> 		return (long) bytes.length;
> 	}
> 
> 	@Override
> 	protected void writeInternal(byte[] bytes, HttpOutputMessage outputMessage) throws IOException {
> 		StreamUtils.copy(bytes, outputMessage.getBody());
> 	}
> 
> 
> ```
>
> 

![image-20200907222840000](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200907222840000.png)

![image-20200907222938693](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200907222938693.png)

> 不同converter处理不同类型

![image-20200907223632958](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200907223632958.png)



## 8. 文件上传

>  基础环境搭建

![image-20200907230025021](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200907230025021.png)

> 页面文件上传 表单 

![image-20200907230312397](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200907230312397.png)

> 相关依赖

```xml
    <!-- https://mvnrepository.com/artifact/commons-fileupload/commons-fileupload -->
    <dependency>
      <groupId>commons-fileupload</groupId>
      <artifactId>commons-fileupload</artifactId>
      <version>1.4</version>
    </dependency>

    <!-- https://mvnrepository.com/artifact/commons-io/commons-io -->
    <dependency>
      <groupId>commons-io</groupId>
      <artifactId>commons-io</artifactId>
      <version>2.6</version>
    </dependency>
```



> 上传表单

```html
<form action="${ctp}/upload" method="post" enctype="multipart/form-data">
    头像：<input type="file" name="headerimg"  /><br/>
    名字：<input type="text" name="username" /><br/>
    <input type="submit" />
</form>
```

>  配置文件上传解析器   `MultipartResolver` 9大组件之1

![image-20200907231235109](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200907231235109.png)

```xml
    <!--配置文件上传解析器-->
    <!-- id要为 multipartResolver  因为 初始化的 值是 public static final String MULTIPART_RESOLVER_BEAN_NAME = "multipartResolver"; -->
    <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
            <property name="maxUploadSize" value="#{1024*1024*10}">
            </property>
            <property name="defaultEncoding" value="utf-8"></property>
    </bean>

```

> 文件上传请求处理

```java
    /**
     * @param modelMap
     * @param username
     * @param file
     * @return
     * @RequestParam( "headerimg") MultipartFile file 封装读取文件的信息，直接保存
     */
    @RequestMapping("/upload")
    public String uploadFile(ModelMap modelMap, @RequestParam(value = "username", required = false) String username, @RequestParam("headerimg") MultipartFile file) {
        //  modelMap.addAttribute("msg", "messages!");
        // file info
        System.out.println("文件项的名字，就是在页面上的name" + file.getOriginalFilename());
        System.out.println("文件的名字" + file.getName());
        // file save
        try {
            // 上传文件， 入参 File  
            file.transferTo(new File("E:\\ecplise-workspace(2)\\springmvc\\springmvc05\\uploadTest\\" + file.getOriginalFilename()));
            System.out.println("Upload successfully");
        } catch (IOException e) {
            e.printStackTrace();
            System.out.println("fail" + e.getMessage());
        }
        return "forward:/index.jsp";
    }
}

```



## 9.多文件上传



> ` @RequestParam("headerimg") MultipartFile[] file`

```java
    /**
     * 多文件上传
     *
     * @param modelMap
     * @param username
     * @param file
     * @return
     * @RequestParam("headerimg") MultipartFile[] file   // MultipartFile[]数组
     * 遍历保存
     */
    @RequestMapping("/upload")
    public String uploadFile(ModelMap modelMap, @RequestParam(value = "username", required = false) String username, @RequestParam("headerimg") MultipartFile[] file) {
        //  modelMap.addAttribute("msg", "messages!");
        for (MultipartFile multipartFile : file) {
            // file info
            System.out.println("文件项的名字，就是在页面上的name" + multipartFile.getOriginalFilename());
            System.out.println("文件的名字" + multipartFile.getName());
            if (!multipartFile.isEmpty()) {
                try {
                    multipartFile.transferTo(new File("E:\\ecplise-workspace(2)\\springmvc\\springmvc05\\uploadTest\\" + multipartFile.getOriginalFilename()));
                    System.out.println("upload success");
                } catch (IOException e) {
                    e.printStackTrace();
                    System.out.println("upload fail");
                    System.out.println(e.getMessage());
                }
            }
        }
        return "forward:/index.jsp";
    }
```



