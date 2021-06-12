[TOC]



# solr



## 下载安装

### solr 7.7

![image-20210116012431551](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210116012431551.png)

### 分词器

![image-20210116012439590](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210116012439590.png)

## 启动和基本结构

基本环境 jdk 1.8 tomcat 8+

![image-20210116012448750](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210116012448750.png)

访问 http://localhost:8983/solr/#/

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210116013210232.png" alt="image-20210116013210232" style="zoom:50%;" />

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210116013410234.png" alt="image-20210116013410234" style="zoom:50%;" />



## 基本命令



1.启动solr

bin目录下 

win-`solr start`  

centos `./Solr start`

2.停止

`./solr stop` 

3.其他端口启动

`./solr start -p 8081`

4.重新启动

`./solr restart`

5.帮助

`./solr -help`

6.状态信息

`./solr status`



## 字典树

​	可以解决hashmap数据量太大的一直扩容问题。

![image-20210116182042687](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210116182042687.png)

### 字典树实现倒排索引

## 索引库创建

1.界面创建

2.命令创建

solr bin目录下  创建核心索引库

![image-20210116183931080](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210116183931080.png)

## 控制台

1.模拟

![image-20210116184703599](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210116184703599.png)

==中文分词需要分词器==

2.创建filed

![image-20210116185111827](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210116185111827.png)

3.添加数据

需要和前面添加的filed name对应

![image-20210116185616515](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210116185616515.png)

![image-20210116212226461](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210116212226461.png)



4.查询

![image-20210116185954881](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210116185954881.png)

![image-20210116191041888](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210116191041888.png)

==如果查询不到很有可能是没有分词==

高亮结果：

![image-20210116225029809](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210116225029809.png)





## 分词和IK库



1.分词目的：

2.IK库：

​	主流分词库，可以自定义扩展词库，基于最大匹配分词

maven直接依赖：

```
 <!-- https://mvnrepository.com/artifact/com.github.magese/ik-analyzer -->
        <dependency>
            <groupId>com.github.magese</groupId>
            <artifactId>ik-analyzer</artifactId>
            <version>7.7.0</version>
        </dependency>
```

jar:

![image-20210116210359083](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210116210359083.png)

复制到WEB-INF下的lib小

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210116193624291.png" alt="image-20210116193624291" style="zoom:50%;" />



3.在 managed-schema中添加

```xml
	 <fieldType name="text_ik" class="solr.TextField">
      <analyzer type="index">
          <tokenizer class="org.wltea.analyzer.lucene.IKTokenizerFactory" useSmart="false" conf="ik.conf"/>
          <filter class="solr.LowerCaseFilterFactory"/>
      </analyzer>
      <analyzer type="query">
          <tokenizer class="org.wltea.analyzer.lucene.IKTokenizerFactory" useSmart="true" conf="ik.conf"/>
          <filter class="solr.LowerCaseFilterFactory"/>
      </analyzer>
    </fieldType>
```

![image-20210116211119457](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210116211119457.png)

支持中文分词

![image-20210116211421426](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210116211421426.png)



==索引的建议规则：1.索引创建分词粒度小，搜索数的分词尽量多==

所以最后一个的结果应该是这样的，修改配置

```xml
<fieldType name="text_ik" class="solr.TextField">
      <analyzer type="index">
          <tokenizer class="org.wltea.analyzer.lucene.IKTokenizerFactory" useSmart="true" conf="ik.conf"/>
          <filter class="solr.LowerCaseFilterFactory"/>
      </analyzer>
      <analyzer type="query">
          <tokenizer class="org.wltea.analyzer.lucene.IKTokenizerFactory" useSmart="false" conf="ik.conf"/>
          <filter class="solr.LowerCaseFilterFactory"/>
      </analyzer>
    </fieldType>
```

测试效果

![image-20210116211858667](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210116211858667.png)