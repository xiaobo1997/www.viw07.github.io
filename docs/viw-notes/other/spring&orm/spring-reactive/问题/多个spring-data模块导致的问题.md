
# 问题

一开始是因为使用spring-webflux 来构建project，引入了r2dbc和redis相关，(r2dbc类似于jpa一样)

然后我在controller调serviceImpl,serviceImpl实现service，serviceImpl中调dao，dao一直无法实例化，也就是无法注入

```
Required a bean of type that could not be found
```

## 问题1
dao无法注入


### 解决 
花了一天还没有解决，后面我rollback了提交的代码，一步一步的看，是因为我引入了spring-data-reactive-redis导致的，

## 问题2

```
Multiple Spring Data modules found, entering strict repository configuration mode!
```

![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20220415142736.png)


# 相关


https://tousu.in/?qa=244701/
https://stackoverflow.com/questions/47002094/spring-multiple-spring-data-modules-found-entering-strict-repository-configur
