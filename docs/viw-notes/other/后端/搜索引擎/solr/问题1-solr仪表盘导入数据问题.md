[TOC]



# solr 仪表盘导入数据问题

## 使用solr 仪表盘导入我数据库表的数据，怎么也导入不了







## db-data-confgi.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<dataConfig>
  <dataSource type="JdbcDataSource"
              driver="com.mysql.jdbc.Driver" 
              url="jdbc:mysql://localhost:3306/db_xmshop?useSSL=true&amp;characterEncoding=UTF-8" 
              user="root" 
              password="root"/>
  <document>
    <!--指定需要导入的数据库表名，以及数据  -->
    <entity name="address" dataSource="JdbcDataSource"
            query="select * from address">
            <!-- 每一个field映射着数据库中列与文档中的域，column是数据库列，name是solr的域(必须是在managed-schema文件中配置过的域才行) -->            
       			<field name="id" column="a_id" />
       			<field name="u_id" column="u_id" />
       			<field name="a_name" column="a_name" />
       			<field name="a_phone" column="a_phone" />
       			<field name="a_detail" column="a_detail" />  	
       			<field name="a_state" column="a_state" />  	
       			
    </entity>
  </document>
</dataConfig>
```



## 部分managed-schema文件内容

```xml
	<!--分词器-->
	<fieldType name="text_ik" class="solr.TextField">
	<analyzer type="index" class="org.wltea.analyzer.lucene.IKAnalyzer"/>
    <analyzer type="query" class="org.wltea.analyzer.lucene.IKAnalyzer"/>
	</fieldType>
	
	<!-- fieldType：可以自己定义type的类型，比如中文的分词器IKAnalyzer
field域：主要是用于数据存取的域,里面使用key,value存储数据。
name:类似于key，存储的文本名称
type：存储的数据类型
indexed：是否建立索引，也就是说能不能使用该域进行搜索查找。
stored:是否存储数据，一般对象的基本属性是存储的
multiValued:可以是多个值，和数组基本类似，格式[]-->

<field name="u_id" type="string" indexed="true" stored="true" />
<field name="a_name" type="text_ik" indexed="true" stored="true" />
<field name="a_phone" type="string" indexed="true" stored="true" />
<field name="a_detail" type="text_ik" indexed="true" stored="true" />
<field name="a_state" type="text_ik" indexed="true" stored="true" />
<field name="ik_keywords" type="text_ik" indexed="true" stored="false" required="true" multiValued="true"/>
<!-- copyField：复制域，主要是将指定的数据复制到某一个域对象中，以至于一个域可以存储多个域的信息，这样就方便构建默认搜索域就行搜索，非常的方便。-->
<copyField source="a_phone" dest="ik_keywords"/>
<copyField source="a_name" dest="ik_keywords"/>
<copyField source="a_detail" dest="ik_keywords"/>
```



## 解决

在solrconfig.xml中的 `requestHandler` 标签中  使用绝对路径 数据源配置文件的

```xml
	<!--配置的数据源信息-->
<requestHandler name="/dataimport" 
   class="org.apache.solr.handler.dataimport.DataImportHandler">
		<lst name="defaults">
		  <str name="config">D:\codingtools\solr\solr-7.7.3\server\solr\core1\conf\db-data-config.xml</str>
		</lst>
</requestHandler>
```



![image-20210116023402475](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210116023402475.png)