[TOC]



# 1.jsp



## 基本标签





## 语法



### 声明变量

```jsp
<!% int a=0; %>
<!% int a,b;%>
```

### 赋值语法

```
<%= 表达式%>
```

```jsp
<%=new Date() %>
```

```jsp
<%!
  static {
    System.out.println("loding...");
  }
  private int globalvar=0;
  public void jspInit(){
    System.out.println("进入了方法");
  }
%>

```



### 流程控制语句



### jsp操作

> JSP 操作使用 XML 语法结构来控制 servlet 引擎的行为。你可以动态地插入一个文件，重组 JavaBean 组件，将用户转换到另一个页面，或为 Java 插件生成 HTML。

| 语法            | 目的                                                       | 属性          | 解释和例                                                     |
| :-------------- | :--------------------------------------------------------- | ------------- | ------------------------------------------------------------ |
| jsp:include     | 当请求页面时，包含一个文件,可以来组装页面                  |               |                                                              |
| jsp:useBean     | 发现或实例化一个 JavaBean                                  |               | 它首先利用 id 和 scope 变量搜索现有对象。如果没有找到一个对象，那么它会试图创建指定的对象。 |
| jsp:setProperty | JavaBean 的属性集                                          |               | `<jsp:useBean id="myName" ... > ...    <jsp:setProperty name="myName" property="someProperty" .../> </jsp:useBean>` |
| jsp:getProperty | 将 JavaBean 的属性嵌入到输出中                             | name,property |                                                              |
| jsp:forward     | 将请求转发给一个新页面                                     |               |                                                              |
| jsp:plugin      | 生成浏览器-特定代码，为 Java 插件创建 OBJECT 或 EMBED 标签 |               |                                                              |
| jsp:element     | 动态的定义 XML 元素                                        |               |                                                              |
| jsp:attribute   | 定义了动态定义的 XML 元素的属性                            |               |                                                              |
| jsp:body        | 定义了动态定义 XML 元素的 body                             |               |                                                              |
| jsp:text        | 用于在 JSP 页面和文档中编写模板                            |               |                                                              |



## 九大内置对象



| 对象        | 描述                                                         |
| :---------- | :----------------------------------------------------------- |
| request     | 这是与请求关联的 **HttpServletRequest** 对象。               |
| response    | 这是与客户端响应关联的 **HttpServletResponse** 对象。        |
| out         | 这是用于向客户端发送输出的 **PrintWriter** 对象。            |
| session     | 这是与请求关联的 **HttpSession** 对象。                      |
| application | 这是与应用程序上下文关联的 **ServletContext** 对象。         |
| config      | 这是与页面关联的 **ServletConfig** 对象。                    |
| pageContext | 这个封装特使用特定服务器的特性，如更高的性能 **jspwriter**。 |
| page        | 这是 **this** 的一个简单的同义词，是用来调用由转换的 servlet 类定义的方法。 |
| Exception   | **Exception**对象允许指定的 JSP 访问异常数据。               |

## 四大作用域