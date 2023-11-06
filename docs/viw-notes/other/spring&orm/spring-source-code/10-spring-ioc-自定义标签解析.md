[TOC]



# 自定义标签解析



## 概念

![image-20201222223628322](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201222223628322.png)

> spring的标签是通过xml来实现的，通过XSD来 定义元素，属性，数据



**扩展spring自定义标签的步骤**

1. 创建一个需要扩展的组件
2. 定义一个XSD文件，用来描述组件内容
3. 创建一个实现AbstractSingleBeanDefinitionParser接口的类，或者创建一个实现实现BeanDefinitionParser接口的类，来解析XSD文件中定义和组件定义，这两个实现方法定义不同的XSD文件配置方式
4. 创建一个Handler，继承NamespaceHandlerSupport,将组件注册到spring容器
5. 编写spring.handlers和spring.schemas文件



## AbstractSingleBeanDefinitionParser



### 创建组件

```java
package com.viw.MyTag;

/**
 * @Author: xhb
 * @Date: 2020/12/22 22:45
 */
public class Car {
    private String color;

    public Car(){
        System.out.println("Car的无参构造方法");
    }

    public String getColor() {
        return color;
    }

    public void setColor(String color) {
        this.color = color;
    }

    @Override
    public String toString() {
        return "Car{" +
                "color='" + color + '\'' +
                '}';
    }
}

```



### 定义XSD文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<xsd:schema
        xmlns="http://hresh.com/schema/org"
            xmlns:xsd="http://www.w3.org/2001/XMLSchema"
            targetNamespace="http://hresh.com/schema/org"
            elementFormDefault="qualified">

    <xsd:element name="car">
    <xsd:complexType>
        <xsd:attribute name="id" type="xsd:string" />
        <xsd:attribute name="color" type="xsd:string"/>
    </xsd:complexType>
</xsd:element>
        </xsd:schema>

```



### 解析

```java
package com.viw.MyTag;

import org.springframework.beans.factory.support.BeanDefinitionBuilder;
import org.springframework.beans.factory.xml.AbstractSimpleBeanDefinitionParser;
import org.springframework.beans.factory.xml.ParserContext;
import org.springframework.util.StringUtils;
import org.w3c.dom.Element;

/**
 * @Author: xhb
 * @Date: 2020/12/22 23:00
 * 解析 XSD 文件中的定义和组件定义
 */
public class CarBeanDefinitionParser extends AbstractSimpleBeanDefinitionParser  {
    @Override
    protected void doParse(Element element, ParserContext parserContext, BeanDefinitionBuilder builder) {
        String color = element.getAttribute("color");
        if (StringUtils.hasText(color)) {
            builder.addPropertyReference("color",color);
        }
    }

    @Override
    protected Class<?> getBeanClass(Element element) {
        return Car.class;
    }
}

```



### Handler类



```java
package com.viw.MyTag;

import org.springframework.beans.factory.xml.BeanDefinitionDecorator;
import org.springframework.beans.factory.xml.NamespaceHandlerSupport;

/**
 * @Author: xhb
 * @Date: 2020/12/22 23:04
 */
public class MyNamespaceHandler extends NamespaceHandlerSupport {

    @Override
    public void init() {
        registerBeanDefinitionParser("car", new CarBeanDefinitionParser());
    }
}

```



### 编写 spring.handlers 和spring.schemas

spring.handlers

```xml
http\://hresh.com/schema/org=com.viw.MyTag.MyNamespaceHandler
```

spring.schemas

```
http\://hresh.com/schema/org.xsd=META-INF/org.xsd
```



### 测试

```java
package com.viw.MyTag;

import org.junit.jupiter.api.DynamicTest;
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

/**
 * @Author: xhb
 * @Date: 2020/12/22 23:36
 */

public class MyTagTest {

    public static void main(String[] args) {

        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        Car car =(Car) context.getBean("car1");
        System.out.println(car);
    }
}

```



## BeanDefinitionParser