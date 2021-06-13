[TOC]





# 1.过滤器



## 概念



> 过滤器（Filter）是web进行拦截的对象



> 过滤器一般是对发起请求之后，响应数据之前进行过滤器，可以对字符编码的转换，对输入登录验证的过滤



> 过滤器是可插拔的，相当于在web.xml中定义，删除其中的定义，过滤器也被删除了，不需要改变servlet。
>
> 过滤器不需要依赖其他资源



![image-20201211011904680](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201211011904680.png)



## 使用

> 过滤器接口
>
> - 1.Filter
>   - public void init(FilterConfig config):init在初始化调用一次，来初始化过滤器
>   - public void doFilter(HttpServletRequest request,HttpServletResponse response, FilterChain chain)：编写过滤逻辑，每一次调用都会执行里面的方法
>   - public void destory()：从服务端中删除filter，仅调用一次
> - 2.FilterChain：主要是负责调用下一个过滤器
>   - **public void doFilter（HttpServletRequest request，HttpServletResponse response）：把要过滤的传递给下一个
> - 3.FilterConfig：servlet 容器使用的过滤器配置对象，该对象在初始化期间将信息传递给过滤器。



```java

public class MyFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
            servletResponse.setContentType("text/html;charset=utf-8");
        PrintWriter writer = servletResponse.getWriter();
        writer.write("过滤之前");
        filterChain.doFilter(servletRequest,servletResponse);
        writer.write("过滤之后");

    }
    @Override
    public void destroy() {

    }
}

```





```xml
 <filter>
        <filter-name>MyFilter</filter-name>
        <filter-class>com.viw.filter.MyFilter</filter-class>
    </filter>

    <filter-mapping>
        <filter-name>MyFilter</filter-name>
        <url-pattern>/uploadServlet</url-pattern>
    </filter-mapping>
```

