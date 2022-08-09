


# fastjson


> 特点就是快，快就完事了，虽然有bug
> https://github.com/alibaba/fastjson/wiki/Quick-Start-CN



## object=>json



## json => object



## 获取json中数据

```java
// 获取对象数据
JSONObject jsonObject = JSONObject.parseObject(response);
// 获取array数据
JSONObject jsonObject = JSONObject.parseObject(response);
JSONArray jsonArray = (JSONArray) ((JSONObject) jsonObject.get("content")).get("rows");
 List<Map<String, Object>> result = JSONArray.parseObject(jsonArray.toJSONString(), List.class);
```



## 序列化和反序列化



### 序列化对象

```java
//序列化
String serializedStr = JSON.toJSONString(user1);
String serializedStr1 = JSON.toJSONString(user1,SerializerFeature.WriteClassName);
System.out.println("serializedStr="+serializedStr);

```



### 反序列化

```java

//通过parse方法进行反序列化
User user2 = (User)JSON.parse(serializedStr1);
System.out.println(user2.getUsername());
System.out.println();

//通过parseObject方法进行反序列化  通过这种方法返回的是一个JSONObject
Object obj = JSON.parseObject(serializedStr1);
System.out.println(obj);
System.out.println("obj name:"+obj.getClass().getName()+"\n");

//通过这种方式返回的是一个相应的类对象，返回的是一个实际类型
Object obj1 = JSON.parseObject(serializedStr1,Object.class);
System.out.println(obj1);
System.out.println("obj1 name:"+obj1.getClass().getName());


```


## TypeReference处理泛型


### 使用

> DTO 如返回的类型如下

```
List<DTO>

Map<String,DTO>

Map<String,List<DTO>>

```

> TypeReference

```java

List<DTO>  xxx = JSONObject.parseObject(JSONString,new TypeReference<List<DTO>>() {});

Map<String,DTO> xxx = JSONObject.parseObject(JSONString,new TypeReference<Map<String,DTO>>() {});
 
```






