[TOC]



# 1.servlet



## 概念

> Servlet 运行在服务端的Java小程序，是sun公司提供一套规范（接口），**用来处理客户端请求、响应给浏览器的动态资源**。但servlet的实质就是java代码，通过java的API 动态的向客户端输出内容。
>
> 　　Servlet是Java Web的三大组件（**Servlet,Filter,Listener**）之一，属于动态资源 ,运行在 Web 服务器或应用服务器上的程序作用为处理请求，服务器会把接收的请求交给Servlet来处理，在Servlet中通常需要：
>
> 接受请求数据、处理请求、完成响应



servlet容器是web服务器的一部分，可以单独在进程中运行

servlet是java EE规范之一



> 实现servlet的发生

- 1.实现servlet接口
- 2.继承GenericServlet类
- 3.继承HttpServelt类(一般选这个)



> servlet与线程安全

- 1.不要在servlet中创建成员变量，使用局部变量
- 2.创建无状态成员
- 3.创建有状态成员，但是状态为只读



### servlet api

> servlet中经常用的两个包 
>
> - 1.javax.servlet:这个是不用认证任何协议的
> - 2.javax.servlet.http：这个是在http协议下的



> 在servlet package下的接口和实现类

| Interfaces                      | Classes                      |
| ------------------------------- | ---------------------------- |
| Servlet                         | GenericServlet               |
| ServletRequest                  | ServletInputStream           |
| ServletResponse                 | ServletOutputStream          |
| RequestDispactcher              | ServletRequestWrapper        |
| ServletConfig                   | ServletResponseWrapper       |
| ServletContext                  | ServletRequestEvent          |
| SingleThreadModel               | ServletContextEvent          |
| Filter                          | ServletRequestAttributeEvent |
| FilterConfig                    | ServletContextAttributeEvent |
| FilterChain                     | ServletException             |
| ServletRequestListener          | UnavalibleException          |
| ServletRequestAttrbuteListener  |                              |
| ServletContextListener          |                              |
| ServletContextAttributeListener |                              |



> javax.servlet.http 下的package and class

| interface                     | class                      |
| ----------------------------- | -------------------------- |
| HttpServletRequest            | HttpServlet                |
| HttpServletResponse           | Cookie                     |
| HttpSession                   | HttpServletRequestWrapper  |
| HttpSessionListener           | HttpServletRequestWrapper  |
| HttpSessionAttributeListener  | HttpSessionEvent           |
| HttpSessionBindingListener    | HttpSessionBindingEvent    |
| HttpSessionActivationListener | HttpUtils (deprecated now) |
| HttpSessionContext            |                            |



### servlet 接口

> servlet接口是一种规范，定义了所有实现 servlet接口的类都要实现他的方法
>
> 你可以在创建一个类实现接口，并实现他接口中的方法，来对servlet的创建初始化，服务处理，和销毁



> 接口中的method

| Method                                                       | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| **public void init(ServletConfig config)**                   | 初始化servlet。 它是Servlet的生命周期方法，仅由Web容器启动时调用一次。 |
| **public void service(ServletRequest request,ServletResponse response)** | 提供对传入请求的响应。 Web容器在每次请求时都会调用它。       |
| **public void destroy()**                                    | 仅被调用一次，当servlet被销毁时。                            |
| **public ServletConfig getServletConfig()**                  | 返回ServletConfig的对象。                                    |
| **public String getServletInfo()**                           | 返回有关servlet的信息                                        |



> example--1

```java
public class MyServlet1 implements Servlet {
    @Override
    public void init(ServletConfig servletConfig) throws ServletException {
        System.out.println("servlet被初始化了");
    }

    @Override
    public ServletConfig getServletConfig() {
        return null;
    }

    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        servletResponse.setContentType("text/html");

        PrintWriter writer = servletResponse.getWriter();
        writer.println("<h4>hello这里是处理你的请求</h4>");
    }

    @Override
    public String getServletInfo() {
        return null;
    }

    @Override
    public void destroy() {
        System.out.println("servlet destory");
    }
}
```

### GenericServlet 类

> 这个类提供了 除 service 以外其他方法的实现



> 类中的方法

1. **public void init(ServletConfig config)** 用于初始化servlet。
2. **public abstract void service(ServletRequest request, ServletResponse response)** 为传入的请求提供服务。 每当用户请求servlet时都会调用它。
3. **public void destroy()** i在整个生命周期中仅被调用一次，表示servlet被破坏。
4. **public ServletConfig getServletConfig()** 返回ServletConfig的对象。
5. **public String getServletInfo()** returns information about servlet such as writer, copyright, version etc.
6. **public void init()** 子类的init方法实现
7. **public ServletContext getServletContext()** 返回ServletContext的对象。
8. **public String getInitParameter(String name)** r返回给定参数名称的参数值。
9. **public Enumeration getInitParameterNames()** 返回web.xml文件中定义的所有参数。
10. **public String getServletName()** 返回servlet对象的名称。
11. **public void log(String msg)** 将给定消息写入servlet日志文件。
12. **public void log(String msg,Throwable t)** 将说明性消息写入servlet日志文件和堆栈跟踪中。

> example--1

```java
/**
 * @Author: xiaobo
 * @Date: 2020/12/2 0:44
 */
public class GenericServlet1 extends GenericServlet {
    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        servletResponse.setContentType("text/html");
        PrintWriter writer = servletResponse.getWriter();
        writer.println("<h2>GenericServlet1</h2>");
    }
}
```

### HttpServlet

> HttpServlet继承了GenericServlet



> 方法

1. **public void service(ServletRequest req,ServletResponse res)** 通过将请求和响应对象转换为http类型，将请求分配到受保护的服务方法。
2. **protected void service(HttpServletRequest req, HttpServletResponse res)** 从service方法接收请求，然后根据传入的http请求类型将请求分派到doXXX（）方法。
3. **protected void doGet(HttpServletRequest req, HttpServletResponse res)** 处理GET请求。 它由Web容器调用。
4. **protected void doPost(HttpServletRequest req, HttpServletResponse res)** 处理POST请求。 它由Web容器调用。
5. **protected void doHead(HttpServletRequest req, HttpServletResponse res)** handles the HEAD request. It is invoked by the web container.
6. **protected void doOptions(HttpServletRequest req, HttpServletResponse res)** handles the OPTIONS request. It is invoked by the web container.
7. **protected void doPut(HttpServletRequest req, HttpServletResponse res)** handles the PUT request. It is invoked by the web container.
8. **protected void doTrace(HttpServletRequest req, HttpServletResponse res)** handles the TRACE request. It is invoked by the web container.
9. **protected void doDelete(HttpServletRequest req, HttpServletResponse res)** handles the DELETE request. It is invoked by the web container.
10. **protected long getLastModified(HttpServletRequest req)**返回自格林尼治标准时间1970年1月1日午夜以来最后一次修改HttpServletRequest的时间。

> example--1

```java
public class MyServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println(getServletContext().getInitParameter("port"));
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("doPost");
    }
}
```



### servletRequest接口

> ServletRequest的对象用于向Servlet提供客户端请求信息，例如内容类型，内容长度，参数名称和值，标头信息，属性等。



> 类声明

```java
public interface ServletRequest 
```

> 方法

| Method                                                       | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| **public String getParameter(String name)**                  | 用于通过名称获取参数的值。                                   |
| **public String[] getParameterValues(String name)**          | 返回一个String数组，其中包含给定参数名称的所有值。 它主要用于获取“多重选择”列表框的值。 |
| **java.util.Enumeration getParameterNames()**                | 返回所有请求参数名称的枚举。                                 |
| **public int getContentLength()**                            | 返回请求实体数据的大小；如果未知，则返回-1。                 |
| **public String getCharacterEncoding()**                     | 返回此请求输入的字符集编码。                                 |
| **public String getContentType()**                           | 返回请求实体数据的Internet媒体类型；如果未知，则返回null。   |
| **public ServletInputStream getInputStream() throws IOException** | 返回用于读取请求正文中的二进制数据的输入流。                 |
| **public abstract String getServerName()**                   | 返回接收请求的服务器的主机名。                               |
| **public int getServerPort()**                               | 返回接收到该请求的端口号。                                   |



### ServletResponse接口





## servlet核心类库



![image-20201203130650531](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201203130650531.png)





## 使用步骤

### 创建一个简单的servlet

步骤：

- 1.编写一个一个具体实现类实现servlet接口

  - ```java
    public class MyServlet extends HttpServlet {
        @Override
        protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
            String string = "viw";
            System.out.println(getServletContext().getInitParameter("port"));
            PrintWriter writer = resp.getWriter();
            writer.println("<h1>"+string+"</h1>");
        }
        @Override
        protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
            System.out.println("doPost");
        }
    }
    
    ```

    

- 2.实现接口中的方法，

  - ```java
     @Override
        protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
            String string = "viw";
            System.out.println(getServletContext().getInitParameter("port"));
            PrintWriter writer = resp.getWriter();
            writer.println("<h1>"+string+"</h1>");
        }
        @Override
        protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
            System.out.println("doPost");
        }
    ```

    

- 3.在web.xml中注册这个实现类

  - ```java
     <servlet>
            <!--servlet的名字，与你要注册的servlet名字尽量保持一致-->
            <servlet-name>MyServlet</servlet-name>
            <!--当前servlet所在类的全限定类名-->
            <servlet-class>com.viw.servlet.MyServlet</servlet-class>
            <!--定义的参数名和参数值-->
            <init-param>
                <param-name>username</param-name>
                <param-value>xiaobo</param-value>
            </init-param>
            <!--servlet在工程启动时的加载顺序，越小越先加载-->
            <load-on-startup>1</load-on-startup>
        </servlet>
        <servlet-mapping>
            <!--约定与上面保持一致，-->
            <servlet-name>MyServlet</servlet-name>
            <!--当前servlet的访问路径，http://ip:port/工程名/myservlet1  访问这个注册的servlet-->
            <url-pattern>/myservlet</url-pattern>
        </servlet-mapping>
    ```

- 4.

  - ![image-20201202011156587](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201202011156587.png)



## 2.目录结构

> 目录结构定义了放置不同类型文件的位置，以便Web容器可以获取信息并响应客户端。

![image-20201202011519266](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201202011519266.png)

![image-20201202012023897](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201202012023897.png)



## 3.关于servlet线程安全问题



> 单线程环境不需要考虑线程安全问题

因为在单线程环境下，对服务端的servlet Thread 执行了init()方法时，client的 servlet therad 无法执行该实例的service方法，也没有线程可以去执行 destory()方法

>多线程环境下协议考虑线程安全问题

因为服务端处理客户端多个请求,服务器会在单独的Client Service Thread线程中执行Servlet实例的service()方法服务于每个客户端。此时会有多个线程同时执行同一个Servlet实例的service()方法，因此必须考虑线程安全的问题。

```
1. 如果service()方法没有访问Servlet的成员变量也没有访问全局的资源比如静态变量、文件、数据库连接等，而是只使用了当前线程自己的资源，比如非指向全局资源的临时变量、request和response对象等。该方法本身就是线程安全的，不必进行任何的同步控制。

2. 如果service()方法访问了Servlet的成员变量，但是对该变量的操作是只读操作，该方法本身就是线程安全的，不必进行任何的同步控制。

3. 如果service()方法访问了Servlet的成员变量，并且对该变量的操作既有读又有写，通常需要加上同步控制语句。

4. 如果service()方法访问了全局的静态变量，如果同一时刻系统中也可能有其它线程访问该静态变量，如果既有读也有写的操作，通常需要加上同步控制语句。

5. 如果service()方法访问了全局的资源，比如文件、数据库连接等，通常需要加上同步控制语句。
```



![image-20201203101733615](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201203101733615.png)

