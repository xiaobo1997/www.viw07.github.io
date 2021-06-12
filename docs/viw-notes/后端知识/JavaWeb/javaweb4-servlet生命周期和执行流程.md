[TOC]



# 1.生命周期



> servlet的生命周期有五个步骤

- 1.加载servlet类，类加载器负责加载servlet类。 当Web容器收到对Servlet的第一个请求时，将加载Servlet类。
- 2.实例化，Web容器在加载servlet类之后创建servlet的实例。 Servlet实例在Servlet生命周期中仅创建一次。
- 3.初始化。Web容器在创建Servlet实例后仅调用一次init方法。 init方法用于初始化servlet。 它是javax.servlet.Servlet接口的生命周期方法。
- 4.处理服务：每次收到Servlet请求时，Web容器都会调用服务方法。 如果Servlet未初始化，则按照上述的前三个步骤进行，然后调用service方法。 如果servlet被初始化，它将调用service方法。 注意，servlet仅初始化一次。
- 5.销毁：Web容器在从服务中删除Servlet实例之前调用destroy方法。 它为Servlet提供了清理任何资源（例如内存，线程等）的机会。



![image-20201202013135965](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201202013135965.png)



# 2.执行流程



> client- tomcat-db

![image-20201202014903676](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201202014903676.png)



> 时序图

![image-20201202123416442](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201202123416442.png)



## web服务器如何处理请求的流程

- 1.将请求与web.xml文件中的servlet映射。
- 2.创建此请求的请求和响应对象
- 3.在线程上调用服务方法
- 4.公共服务方法在内部调用受保护的服务方法
- 5.受保护的服务方法根据请求的类型调用doGet方法。
- 6.doGet方法生成响应并将其传递给客户端。
- 7.发送响应后，Web容器将删除请求和响应对象。 线程是在线程池中还是被删除取决于服务器实现。



> 公共服务方法做了什么

公共服务方法将ServletRequest对象转换为HttpServletRequest类型，而ServletResponse对象转换为HttpServletResponse类型。 然后，调用传递这些对象的服务方法。 



> 自定义servlet受保护的方法写了什么

受保护的服务方法检查请求的类型，如果请求类型为get，则调用doGet方法，如果请求类型为post，则调用doPost方法，依此类推。