

[TOC]





# centos部署springboot命令小结



## 单工程

### 编译打包

1.把包项目(jar或war)，这里选择的是jar

编译插件：

```
<build>
  	<plugins>
		<plugin>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-maven-plugin</artifactId>
			<configuration>
				<includeSystemScope>true</includeSystemScope>
			</configuration>
		</plugin>
	</plugins>
  </build>
```



直接输入 `mvn clean package`



### maven本地依赖问题

1.这里我工程中有些是本地依赖，然后是中央仓库无法下载下来。

2.选择在具体模块中根目录下创建一个 `lib` 文件夹

![image-20210310194639465](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210310194639465.png)

然后右键 add library 即可，然后在引用的地方修改一下引用路径

在当前工程模块下的 `pom.xml`中 ，修改如下

![image-20210310194913301](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210310194913301.png)



重新maven  reimport

其他问题没有遇到了。其实这个问题之前就遇到，但是又忘记了，不是这种方式，这次记录一下



### 运行事项

1. 根据你配置的 maven  编译插件  看你的jar在哪个target下，
2. 找到 `xxx.jar` 上传 服务器。
3. 切换用户 `su xx`
4. 赋权限 `chmod 770 xxxx.jar`
5. 执行  nohup java -jar xxx.jar >./xxxx.log &
6. 查看日志  `tail -200f xxx`
7. 查看端口 `netstat -nutlp`
8. kill进程  `kill -9  pid`




## 多工程打包子工程



### 1.pom.xml文件



> 1.在要打包的子工程中 加入下面标签

```xml
 <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <source>11</source>
                    <target>11</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <version>2.3.7.RELEASE</version>
                <configuration>
                  	<!--你的当前子工程要打包的启动类-->
                    <mainClass>com.xxx.xxx.xxxApplication</mainClass>
                </configuration>
                <executions>
                    <execution>
                        <id>repackage</id>
                        <goals>
                            <goal>repackage</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
```



> 2.进入项目工程下 打包，默认工程jar在当前工程下的target目录，java -jar 就可以启动。
>
> 3.如果找不到启动类，需要确认是否是目标工程pom.xml有错误，打的是子工程 而不是父工程

```shell
// 排除尝试类
mvn install -Dmaven.test.skip=true

mvn package -Dmaven.test.skip=true

```

