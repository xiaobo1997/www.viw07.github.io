[TOC]





参考 `http://www.yiidian.com/servlet/`

# 1.servletConfig

> Web容器为每个Servlet创建一个ServletConfig对象。该对象可用于从web.xml文件获取配置信息。
>
> 如果从web.xml文件修改了配置信息，则无需更改Servlet。因此，如果您可以把经常改变的参数配置到web.xml，通过ServletConfig对象来读取。



method:

1. **public String getInitParameter（String name）**：返回指定参数名称的参数值。
2. **public Enumeration getInitParameterNames（）**：返回所有初始化参数名称的枚举。
3. **public String getServletName（）**：返回Servlet的名称。
4. **public ServletContext getServletContext（）**：返回ServletContext的对象。

# 2.servletContext



> ServletContext的对象由web容器启动时创建，web容器会为某一个web程序创建一个servletContext对象，它代表了当前的web应用

- 1.比如说共享数据，在一个servlet保存的数据，在另外一个servlet可以拿到，
- 2.获取初始化配置。在web.xml中配置，全局作用
- 3.请求转发。 
  - ServletContext context = this.getServletContext();        RequestDispatcher requestDispatcher = context.getRequestDispatcher("/gp");//转发的请求路径        requestDispatcher.forward(req,resp);//转发
- 4.获取资源
  - 在Java目录下新建propreties（需要在pom.xml下配置resources）
  - 在resources目录下新建properties
  -  InputStream is = this.getServletContext().getResourceAsStream("/WEB-INF/classes/db.properties");        Properties properties = new Properties();        properties.load(is);        String username = properties.getProperty("username");



好处 ：

​		如果一些数据需要共享给所有Servlet使用，希望易于维护，那么最好将它们在web.xml文件中使用<context-param>来定义。而<context-param>的参数只能被ServletContext对象读取。这样的好处是，<context-param>参数如果更改了，则无需修改Servlet，维护起来方便许多。



常用方法：

1. **public String getInitParameter（String name）**：返回指定参数名称的参数值。
2. **public Enumeration getInitParameterNames（）**：返回上下文的初始化参数的名称。
3. **public void setAttribute（String name，Object object）**：在Web应用程序范围内设置给定的对象。
4. **public Object getAttribute（String name）**：返回指定名称的属性。
5. **public Enumeration getInitParameterNames（）**：以字符串对象的枚举形式返回上下文的初始化参数的名称。
6. **public void removeAttribute（String name）**：从ServletContext中删除具有给定名称的属性。