[TOC]



# 1.PDF





```
package com.viw.util;

import com.itextpdf.text.*;
import com.itextpdf.text.Font;
import com.itextpdf.text.pdf.BaseFont;
import com.itextpdf.text.pdf.PdfWriter;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

import static com.itextpdf.text.BaseColor.RED;

public class PdfServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 设置响应头 MIME
        //设置响应类
        resp.setContentType("application/pdf");
        //设置响应头，用于浏览器弹出下载提示框
        resp.setHeader("Content-disposition", "attachment;filename=viw.pdf");

        Document document = new Document(PageSize.A4, 5, 5, 5, 5);
        try {
            PdfWriter.getInstance(document, resp.getOutputStream());
            document.open();
            // 设置字体
            BaseFont bfChinese = BaseFont.createFont("STSong-Light", "UniGB-UCS2-H", BaseFont.NOT_EMBEDDED);
            Font titleFont;
            titleFont = new Font(bfChinese, 10F, Font.NORMAL, RED);
            document.add(new Paragraph("hello", titleFont));
            document.close();
        } catch (DocumentException documentException) {
            documentException.printStackTrace();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doPost(req, resp);
    }
}

```



```xml
  <servlet>
        <servlet-name>PdfServlet</servlet-name>
        <servlet-class>com.viw.util.PdfServlet</servlet-class>
    </servlet>

<servlet-mapping>
        <servlet-name>PdfServlet</servlet-name>
        <url-pattern>/exportPDF</url-pattern>
    </servlet-mapping>
```



![image-20201211021155143](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201211021155143.png)



# 2.图片