[TOC]



# 1.构建spring 5.x源码环境



## 环境

> 默认已下载好了spring源码压缩包

 1.解压缩和 spring 以后，大概目录如下

![image-20201209011725637](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201209011725637.png)



2. 修改如上几个文件，

一个是settings,gradle

```groovy
pluginManagement {
	repositories {
		maven { url "https://maven.aliyun.com/repository/spring-plugin" }
    	maven{ url "https://maven.aliyun.com/nexus/content/repositories/spring-plugin"}
		gradlePluginPortal()
		maven { url 'https://repo.spring.io/plugins-release' }
	}
}

plugins {
	id "com.gradle.enterprise" version "3.2"
	id "io.spring.gradle-enterprise-conventions" version "0.0.2"
}

include "spring-aop"
include "spring-aspects"
include "spring-beans"
include "spring-context"
include "spring-context-support"
include "spring-context-indexer"
include "spring-core"
include "kotlin-coroutines"
project(':kotlin-coroutines').projectDir = file('spring-core/kotlin-coroutines')
include "spring-expression"
include "spring-instrument"
include "spring-jcl"
include "spring-jdbc"
include "spring-jms"
include "spring-messaging"
include "spring-orm"
include "spring-oxm"
include "spring-test"
include "spring-tx"
include "spring-web"
include "spring-webmvc"
include "spring-webflux"
include "spring-websocket"
include "framework-bom"
include "integration-tests"

rootProject.name = "spring"
rootProject.children.each {project ->
	project.buildFileName = "${project.name}.gradle"
}

settings.gradle.projectsLoaded {
	gradleEnterprise {
		buildScan {
			if (settings.gradle.rootProject.hasProperty('customJavaHome')) {
				value("Custom JAVA_HOME", settings.gradle.rootProject.getProperty('customJavaHome'))
			}
			if (settings.gradle.rootProject.hasProperty('customJavaSourceVersion')) {
				value("Custom Java Source Version", settings.gradle.rootProject.getProperty('customJavaSourceVersion'))
			}
			File buildDir = settings.gradle.rootProject.getBuildDir()
			buildDir.mkdirs()
			new File(buildDir, "build-scan-uri.txt").text = "(build scan not generated)"
			buildScanPublished { scan ->
				if (buildDir.exists()) {
					new File(buildDir, "build-scan-uri.txt").text = "${scan.buildScanUri}\n"
				} 
			}
		}
	}
}
include 'spring-viw'


```

一个是 bulid.gradle文件

部分内容如下，主要是增加阿里源

```groovy
buildscript {
	repositories {
		maven {
			url 'https://maven.aliyun.com/nexus/content/groups/public/' }
		maven { url "https://maven.aliyun.com/repository/public/" }
		maven { url "https://maven.aliyun.com/repository/spring/" }
		maven { url "https://repo.spring.io/plugins-release" }
		mavenLocal()
		mavenCentral()
	}
	dependencies {
		classpath("io.spring.gradle:propdeps-plugin:0.0.9.RELEASE")
		classpath("org.asciidoctor:asciidoctorj-pdf:1.5.0-alpha.16")
	}
	allprojects {
		repositories {
			maven {
				url 'https://maven.aliyun.com/repository/public/'
			}
			mavenLocal()
			mavenCentral()
		}
	}
}
```

还要一个配置文件 gradle.properties

这个文件一开始我是改了的，比如指向我的 jdk目录，因为报了个错 找不到我的jdk目录，后面又查询检查了一遍，然后修改了下，就没有做改变，网上很多都改了这个文件，也有注释

```groovy
version=5.3.0-SNAPSHOT
org.gradle.jvmargs=-Xmx1536M
org.gradle.caching=true
org.gradle.parallel=true

```





> 安装配置好了gradle 这里简单放两张图，

1.下载好以后随便放个目录解压缩，然后配置环境变量

![image-20201209010659284](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201209010659284.png)

2.

![image-20201209010722405](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201209010722405.png)

3.然后如果你不去修改的话 其实他是下载你工程中的gradle版本，会很慢，

![image-20201209011017074](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201209011017074.png)

也可以你先下载好gradle-6.5-bin.zip，然后放到你指定的目录中，然后修改红框中的内容指到你下载的目录中就可以了。

因为我这里其实一会就下载好了(改一些源，可能是突然网速比较快)，所以我也没改，我本地也有这个版本，



4.如果你下载好了的好 进去修改一下 init.gradle文件，增加这个

5.2.1是我本地版本，然后它自己又下了个 6.5的版本，大概目录如下

![image-20201209011408158](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201209011408158.png)

```groovy
allprojects{
   repositories {
       def REPOSITORY_URL = 'http://maven.aliyun.com/nexus/content/groups/public/'
       all { ArtifactRepository repo ->
           def url = repo.url.toString()
           if ((repo instanceof MavenArtifactRepository) && (url.startsWith('https://repo1.maven.org/maven2') || url.startsWith('https://jcenter.bintray.com'))) {
               project.logger.lifecycle 'Repository ${repo.url} replaced by $REPOSITORY_URL .'
               remove repo            }
       }
       maven {
           url REPOSITORY_URL        }
   }}

```



> IDEA中的gradle配置



![image-20201209012650935](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201209012650935.png)



## 构建



> 前面的改完以后，然后直接把spring 源码拖到IDEA中就可以了

![image-20201209012341146](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201209012341146.png)



> 然后进去以后你会发现这几个文件会有错误，不用管，

![image-20201209012427148](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201209012427148.png)



> 然后你构建的时侯会有很多小问题，这个网上都有，你可能需要构建好几次，可能还要网络的原因，
>
> 我花了一个晚上弄好了，然后八点半下班的，一边下一边看看博客，听听歌 看看面试题就过去了，
>
> 最后构建好是这个样子。然后可以测试一下，就可以通过自己看视频看书打断点，写注释把代码过一遍。
>
> 等明天我把这个传到gitee上面，看一会面试题睡觉了，

![image-20201209012959669](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201209012959669.png)