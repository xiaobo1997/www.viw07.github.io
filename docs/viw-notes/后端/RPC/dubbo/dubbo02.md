# dubbo 高级特性







## 启动时检查



Dubbo 缺省会在启动时检查依赖的服务是否可用，不可用时会抛出异常，阻止 Spring 初始化完成，以便上线时，能及早发现问题，默认 `check="true"`。



可以通过 `check="false"` 关闭检查，比如，测试时，有些服务不关心，或者出现了循环依赖，必须有一方先启动。



> 标签 
>
> ```xml
> 针对某一个服务，真正调用时去检查
> <dubbo:reference interface="com.foo.BarService" check="false" />
> 针对所有服务
> <dubbo:consumer check="false" />
> 注册订阅失败时报错
> <dubbo:registry check="false" />
> 
> 以上转成properties的形式就是
> dubbo.reference.com.foo.BarService.check=false
> dubbo.reference.check=false
> dubbo.consumer.check=false
> dubbo.registry.check=false
> ```
>
> 注解
>
> ```java
> @DubboReference(check = false)
> 	public List<UserAddress> hello(String userId) {
> 		// TODO Auto-generated method stub
> 	
> 		return Arrays.asList(new UserAddress(10, "测试地址", "1", "测试", "测试", "Y"));
> 	}
> ```
>
> 

:bulb:  dubbo 2.7.7后面就推荐使用    `@DubboService` 和 `@DubboReference` 这两个注解 替换 原来的 `@Service` 和  `@Reference`





##  超时设置





consumer



xml

```xml
	<!-- timeout="0" 默认是1000ms-->
	<dubbo:reference interface="com.xxx.xxxx.service.xxxService" 
		id="userService" timeout="5000" retries="3" version="*">
		<!--方法级 <dubbo:method name="getUserAddressList" timeout="1000"></dubbo:method> -->
	</dubbo:reference>

	
```

配置和注解



```java

```

