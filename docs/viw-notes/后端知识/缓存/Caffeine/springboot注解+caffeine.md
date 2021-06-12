[TOC]

# springboot+caffeine(注解)



## 概念



> caffeine

https://github.com/ben-manes/caffeine/wiki/Cleanup-zh-CN

## 使用



> pom.xml

```java

  <!-- https://mvnrepository.com/artifact/com.github.ben-manes.caffeine/caffeine -->
        <dependency>
            <groupId>com.github.ben-manes.caffeine</groupId>
            <artifactId>caffeine</artifactId>
            <version>2.9.0</version>
        </dependency>

```



> Application.yml
>
> 这里需要注意 后面使用的 `Cacheable(value="xxx",key="xxx")` 的value 需要在  下面配置文件中 cache-names中存在。

```yaml
spring:
  cache:
    type: caffeine
    cache-names: types,allfunds,total
    caffeine:
      spec: initialCapacity=50,maximumSize=100,expireAfterWrite=10s
```



> Configuration

```Java

@EnableCaching
@Configuration
public class LocalCache {

//    @Bean
    public Caffeine<Object, Object> caffeineCache() {
        return Caffeine.newBuilder()
                // 设置最后一次写入或访问后经过固定时间过期
                .expireAfterWrite(60, TimeUnit.SECONDS)
                // 初始的缓存空间大小
                .initialCapacity(1000)
                // 缓存的最大条数
                .maximumSize(2000)
                ;
    }

    @Bean
    public CacheManager cacheManager() {
        CaffeineCacheManager caffeineCacheManager = new CaffeineCacheManager("types","allfunds","total");
        caffeineCacheManager.setCaffeine(caffeineCache());
        return caffeineCacheManager;
    }


}
```



> controller

```java
    @GetMapping("/allfund")
    public R getAllFundInfo(@RequestParam(required = false) Integer fundtypecode, @RequestParam(required = false) String recentTime,
                            @RequestParam(required = false) String isDesc, @RequestParam(required = false) String isAsc) {
       // to something
        return R.ok().put("total", res.size()).setData(map);
    }

```



> impl
>
> - 因为 fundtypecode 是 Integer 类型，需要转 string, 然后使用 `concat()` 连接
> - 某些参数可能为null 需要判空，这里使用spel 表达式的 elivs 操作符，  `tital?:0000` 如果，tital为null 就赋默认值 `0000`  这里需要处理。
> - Spel表达式 链接  https://juejin.cn/post/6844903679984664584#heading-9
>
> ```java
> //key="#param1.concat(‘-’).concat(#param2)")
> ```



```java
 //key="concat(#param1).concat(‘-’).concat(#param2)")
    @Cacheable(value = "allfunds",key = "T(String).valueOf(#fundtypecode?:000000).concat('-').concat(#recentTime).concat('-').concat(#isDesc?:'false').concat('-').concat(#isAsc?:'false')")
    @Override
    public List<FundSearchTo> getAllFundInfo(Integer fundtypecode, String recentTime,String isDesc,String isAsc) {
     
     // do something
     
     }


```



## 测试



```
// ......... 开启 sql打印，多次请求看缓存是否生效

```





## 参考



- https://www.cnblogs.com/ejiyuan/p/11014765.html
- https://www.javadevjournal.com/spring-boot/spring-boot-with-caffeine-cache/
- http://www.mydlq.club/article/56/
- https://blog.csdn.net/weixin_38361347/article/details/100087223
- https://bbs.huaweicloud.com/blogs/145798
- https://blog.csdn.net/weixin_44488164/article/details/89479170
- https://blog.csdn.net/qq_24084605/article/details/81003369

