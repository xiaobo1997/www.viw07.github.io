
# 1.说在前面

> 在公司中部署都是点点点，因为公司有运维的人在维护K8S那些东西，刚好阿里云的一台ECS要过期了拿来测试
> 我是先在本地 build image，然后push到docker hub中，


# 2.全部环境

- mac和mac本地docker(20.10.8)
- springboot 2.3.7(打成jar可以运行，但是放docker不一定可以运行了)
- maven
- jdk11
- 一台阿里云ecs服务器，docker(Docker version 1.13.1, build 64e9980/1.13.1)

# 3.全部过程

## 1.打包

```xml
<properties>
        <java.version>11</java.version>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <spring-boot.version>2.3.7.RELEASE</spring-boot.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
       
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>${spring-boot.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

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
                    <mainClass>com.viw.jdb.JdbApplication</mainClass>
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

```
mvn clean install

mvn compile com.google.cloud.tools:jib-maven-plugin:2.3.0:dockerBuild
```

看到success就可以了

![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20220407141248.png)


## 2.本地docker

可以看到images了

![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20220407141139.png)

可以直接点击运行，也可以命令行运行


```shell

docker run -p 9000:8090 -t --name jdb image_id

```
可以直接看到运行日志，没问题可以本地访问一下


## 3.上传远程hub

**首先你需要一个docker hub账号**



```
#查看所有image
docker images 
```



```
# 重新打一个image
docker tag local_images:local_tag_name new_image_name:local_tag_name

```
![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20220407141817.png)

看一下对比

![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20220407141853.png)


```
# push docker hub   docker push xiaobo/jdb:0.0.2-SNAPSHOT
docker push 你的dockerHub账号/jdb:0.0.2-SNAPSHOT
```
然后就会push到xiaobo的jdb这个仓库中，会自动创建repo

然后就可以pull 这个image了，注意外网访问需要暴露端口，




**参考:**

https://segmentfault.com/a/1190000038198513