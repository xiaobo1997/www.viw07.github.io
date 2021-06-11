[TOC]

### 处理文件在windos和linux路径分隔符的问题



> windows

文件路径是： `\`， 一般要使用 `\\` 防止被转义

> linux

文件路径是：  `/`



> 处理兼容问题
>
> File.separator  根据系统的不同选择不同的分隔符

```java
// 
File file = new File(targetFolder + File.separator + sourceFileName+ File.separator, "test.txt");
```

