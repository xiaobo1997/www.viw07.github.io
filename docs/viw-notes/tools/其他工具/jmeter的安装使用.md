[TOC]



# jmeter



## 安装

1.下载

https://jmeter.apache.org/download_jmeter.cgi

win选择zip

## 使用

- 在系统变量classpath后面追加

  ```
  ;%JMETER_HOME%\lib\ext\ApacheJMeter_core.jar;%JMETER_HOME%\lib\jorphan.jar
  ```

- 在系统变量新超级

  ```
  变量名：JMETER_HOME
  变量值：D:\tools\apache-jmeter-5.4.1\apache-jmeter-5.4.1
  ```

- 在系统变量path中增加

  ```
  %JMETER_HOME%\bin
  ```



cmd命令输入jmeter -v 查看版本 

cmd输入jmeter 使用jmeter



==命令行形式启动==

案例：

```shell
jmeter -n -t [jmx file] -l [results file] -e -o [Path to web report folder]
```

- jmx file:压力测试文件
- results file:结果输出文件
- Path to web report folder:生成测试报告

```
jmeter -n -t D:\tools\apache-jmeter-5.4.1\temp\0122\seckill0126.jmx -l D:\tools\apache-jmeter-5.4.1\temp\0122\seckill0126.txt -e -o D:\tools\apache-jmeter-5.4.1\temp\0122\html
```

![image-20210126005219499](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210126005219499.png)

```shell
jmeter -n -t D:\tools\apache-jmeter-5.4.1\temp\0122\seckill0126.jmx
```

