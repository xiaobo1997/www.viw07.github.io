[TOC]



# 1.文件上传



## 概念



> 



## 使用



> 首先引入 依赖

```xml
 <dependency>
      <groupId>commons-io</groupId>
      <artifactId>commons-io</artifactId>
      <version>2.4</version>
    </dependency>
    <dependency>
      <groupId>commons-fileupload</groupId>
      <artifactId>commons-fileupload</artifactId>
      <version>1.3.1</version>
    </dependency>

```



> 前端页面代码

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<form action="uploadServlet" method="post"enctype="multipart/form-data">
    上传照片：<input  type="file" name="upzp" value="上传"/>
    <input  type="submit" value="提交" />
</form>
</body>
</html>

```



servlet代码：



```java
package com.viw.servlet;

import org.apache.commons.fileupload.FileItem;
import org.apache.commons.fileupload.FileItemFactory;
import org.apache.commons.fileupload.FileUploadException;
import org.apache.commons.fileupload.disk.DiskFileItemFactory;
import org.apache.commons.fileupload.servlet.ServletFileUpload;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.util.Iterator;
import java.util.List;
import java.util.UUID;

public class UploadServlet extends HttpServlet {
    private String filepath;

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doPost(req, resp);
    }


    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 设置编码
        req.setCharacterEncoding("utf-8");
        resp.setCharacterEncoding("utf-8");
        resp.setContentType("text/html;charset=utf-8");

        // 判断是否有mutipart属性
        boolean isMultipartContent = ServletFileUpload.isMultipartContent(req);
        if (isMultipartContent) {

            FileItemFactory factory = new DiskFileItemFactory();

            ServletFileUpload fileUpload = new ServletFileUpload(factory);

            fileUpload.setFileSizeMax(2 * 1024 * 1024);

            try {
                // 解析请求
                List<FileItem> fileItems = fileUpload.parseRequest(req);
                // 遍历集合
                for (FileItem f : fileItems
                ) {
                    // 获取文件名字
                    String name = f.getName();
                    // 文件输入流
                    InputStream inputStream = f.getInputStream();
                    // 获取工程相对路径
                    String realPath = this.getServletContext().getRealPath("/WEB-INF/upload");
                    // 防止文件名重复
                    String fileNameInFile = UUID.randomUUID().toString() + "_" + name;
                    // 实例化一个file 
                    File file = new File(realPath, fileNameInFile);
                    // 创建文件
                    if (!file.getParentFile().exists()) {
                        file.getParentFile().mkdirs();
                    }
                    // 输出流 写文件
                    FileOutputStream fileOutputStream = new FileOutputStream(file);
                    byte[] bytes = new byte[1024];
                    int length = 0;
                    while ((length = inputStream.read()) != -1) {
                        fileOutputStream.write(length);
                    }
                    // 刷新输出流
                    fileOutputStream.flush();
                    // 关闭资源
                    inputStream.close();
                    fileOutputStream.close();
                }
            } catch (FileUploadException e) {
                e.printStackTrace();
            }
        }
    }
}

```



> 通过  DiskFileItemFactory的构造器DiskFileItemFactory(int sizeThreshold, File repository) 来设置上传时的临时目录，如缓存大小，如
>
> `DiskFileItemFactory(2*1024,new File("D://xiaobo//temp"))`

# 2.下载



> MIME 

![image-20201207015231861](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201207015231861.png)



> 下载的要求：
>
> - 1.两个响应头
>   - Content-Type
>   - Content-Disposition: attachment;filename=xxx
> - 2.一个流
>   - response.getOutputStream;



```jsp
 下载文件：<a href="/downlevel?filename=1.jpg" >下载图片</a>
```



```java
 @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        req.setCharacterEncoding("utf-8");
        resp.setCharacterEncoding("utf-8");
        resp.setContentType("text/html;charset=utf-8");
        // 获取请求参数的值
        String filename = req.getParameter("filename");
        // 设置响应头
        resp.setHeader("content-type","image/jpeg");
        String uuid = UUID.randomUUID().toString();
        resp.setHeader("content-disposition","attachment;filename="+uuid+".jpg");
//        InputStream inputStream = getServletContext().getResourceAsStream("/view/1.jpg");
        FileInputStream inputStream = new FileInputStream(new File("D://download//chrome/1.jpg"));
        ServletOutputStream outputStream = resp.getOutputStream();
        IOUtils.copy(inputStream,outputStream);
//        byte[] bytes = new byte[1024];
//        int i = -1;
//        while ( (i= inputStream.read(bytes))!=-1) {
//            outputStream.write(bytes,0,i);
//        }
//        outputStream.flush();
//        inputStream.close();
//        outputStream.close();
    }
```

