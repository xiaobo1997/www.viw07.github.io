[TOC]



# Lucene



## 基础

### 为什么学习Lucene

1.以前的搜索实现

​	在没有使用搜索组件之前，我们查询的过程大概是这样的

客户端请求=》项目入口=》controller=》service=》dao=》数据库服务器=》响应结果给客户端

数据量大我们的服务器可能无法承受大量请求，而原查询大多是都是模糊查询之类的。

2.使用lucene

![image-20210115221656472](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210115221656472.png)



lucene 有自己的索引库。可以减少数据库的压力和速度。

索引库的数据来自我们的文档导入，互联网的导入数据，数据库导入的数据







### 数据查询方法

​	lucene底层使用了一下数据查询算法，所以检索是很快的



#### 顺序扫描

​	相当于遍历查询完，从开始查询到结束

优点：

- 查询准确率可以

缺点：

- 数据大，查询时间长

场景：

- 数据库中的like关键字模糊查询

- 文本编辑器的ctrl+f查询

#### 倒排索引

​	lucene使用的方法，查询比较快。

​	查询之前先提取文档内容组成文档，把文档切分词组成目录（索引），

切分词：切分成一个一个词，去掉空格，等标点符号，去除重复。

优点：

- 查询准确率高
- 速度快，不被内容大小影响

缺点：

- 索引文件占磁盘

场景：

- 海量数据查询



==这种算法是空间换时间==



### 什么是全文检索

​	通过扫描把文档切分单元为一个词。为这些每一个单词创建索引，我们查询时，会表面这个词出现位置和次数。我们查询就可以根据提取创建好的这些词的索引去查询，把结果返回



### 全文检索场景

- 1.站内搜索
- 2.垂直领域
- 3.搜索引擎公司
- 4.等







### Lucene是什么



### lucene索引和搜索流程



流程图

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210116162945285.png" alt="image-20210116162945285" style="zoom:50%;" />



#### 1.创建文档，

对要搜索的原始文档进行索引构建成==索引库==：

- 获取数据，可以是文档，可以是数据库数据，可以是互联网数据(通过爬虫)
  - 获取数据是为创建文档。
- 创建文档，创建出文档对象
  - 文档中分为域，在域中存储内容，如下<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210116163759432.png" alt="image-20210116163759432" style="zoom:50%;" />
  - 文档id是lucene为我们自动创建的。![image-20210116171541453](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210116171541453.png)
  - 
- 分析文档，对文档进行切分词
- 对所有切分词得到的词单元组成索引文档，对文档索引。搜索时通过被索引的词找到文档。
  - 而倒排索引就是通过被索引的词找到文档。类似于下<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210116172006814.png" alt="image-20210116172006814" style="zoom:50%;" />
  - 文档号是lucene为我们创建的。

#### 2.搜索过程：

- 调用api查询接口
- 创建查询对象
- 设置查询对象
- 执行查询对象，查询索引库
- 渲染结果
- 返回结果



#### 底层存储结构

![image-20210116172238310](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210116172238310.png)



### 搜索过程

1.创建分词后，词的关系有and or区分。 and是都需要满足查询条件才显示，Or满足任意条件显示



## 分词器(jieba分词)

### 1.依赖

```xml
 <!--        jieba分词-->
        <dependency>
            <groupId>com.huaban</groupId>
            <artifactId>jieba-analysis</artifactId>
            <version>1.0.2</version>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.10</version>
        </dependency>
```

### 2.测试

```java
public class jiebatest1 {
    private static JiebaSegmenter jiebaSegmenter = new JiebaSegmenter();//分词对象

    public static void main(String[] args) {
        String contents = "小米手机 小米9 超级大屏幕手机 黑色  988旗舰机 游戏机 联通移动电信 全网通 超级受欢迎";
        /**
         * 1.contents 分词内容
         * 2.JiebaSegmenter.SegMode.SEARCH 分词模式
         */
        List<SegToken> segTokens = jiebaSegmenter.process(contents, JiebaSegmenter.SegMode.SEARCH);
        for (SegToken segToken : segTokens) {
            System.out.println(segToken.word);
        }
        System.out.println("\n"+segTokens.size());// 27
    }
}
// console
小米
手机
 
小米
9
 
超级
大屏幕
手机
 
黑色
 
 
988
旗舰机
 
游戏机
 
联通
移动
电信
 
全
网通
 
超级
受欢迎

27
Process finished with exit code 0
```



## 搭建环境

### 基础工程环境



### 数据库数据

​	新创建了表，存储了接近百万数据，做测试

后面是读取出来后面切分词组成索引文档存储。

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210116180008868.png" alt="image-20210116180008868" style="zoom:50%;" />

## 索引工程



