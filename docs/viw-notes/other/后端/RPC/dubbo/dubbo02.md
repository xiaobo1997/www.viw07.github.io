
<!-- TOC -->

- [dubbo 高级特性](#dubbo-高级特性)
  - [启动时检查](#启动时检查)
  - [超时设置](#超时设置)
  - [重试次数](#重试次数)
  - [多版本](#多版本)
  - [本地存根](#本地存根)
  - [直连提供者](#直连提供者)
  - [负载均衡](#负载均衡)
    - [Random LoadBalance](#random-loadbalance)
    - [RoundRobin LoadBalance](#roundrobin-loadbalance)
    - [LeastActive LoadBalance](#leastactive-loadbalance)
    - [ConsistentHash LoadBalance](#consistenthash-loadbalance)
    - [配置](#配置)
      - [服务端服务级别](#服务端服务级别)
      - [客户端服务级别](#客户端服务级别)
      - [服务端方法级别](#服务端方法级别)
      - [客户端方法级别](#客户端方法级别)
  - [序列化](#序列化)
- [dubbo相关高可用场景](#dubbo相关高可用场景)
  - [zookeeper注册中心宕机，dubbo直连。](#zookeeper注册中心宕机dubbo直连)
  - [集群下dubbo负载均衡配置](#集群下dubbo负载均衡配置)
  - [服务熔断与降级处理](#服务熔断与降级处理)
    - [服务降级](#服务降级)
    - [集群容错(推荐Hystrix)](#集群容错推荐hystrix)
      - [容错策略](#容错策略)
        - [Failfast Cluster](#failfast-cluster)
        - [Failsafe Cluster](#failsafe-cluster)
        - [Failback Cluster](#failback-cluster)
        - [Forking Cluster](#forking-cluster)
        - [Broadcast Cluster](#broadcast-cluster)
      - [hystrix](#hystrix)
- [dubbo的原理设计简单介绍](#dubbo的原理设计简单介绍)
  - [框架设计](#框架设计)
  - [启动解析，加载配置信息](#启动解析加载配置信息)
  - [服务暴露](#服务暴露)
  - [服务引用](#服务引用)
  - [服务调用](#服务调用)

<!-- /TOC -->
# dubbo 高级特性





##　优先级



```xml
<!-- 
		1）、精确优先 (方法级优先，接口级次之，全局配置再次之)
		2）、消费者设置优先(如果级别一样（如一个是提供者的方法，一个是消费者全局，不是同一个级别），则消费方优先，提供方次之)
	-->
```

- 1.方法级 如 消费者的 reference 中的method配置  大于  提供者的 service 中的method
- 2.接口级别的 如 消费者的  reference 大于 提供者的 service 
- 3.全局设置  消费者 > 提供者

![image-20210710005645871](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210710005645871.png)





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
@DubboReference(loadbalance="random",timeout=1000) //dubbo直连
			UserService userService;
```



## 重试次数





Dubbo 服务在尝试调用一次之后，如出现非业务异常(服务突然不可用、超时等)，Dubbo 默认会进行额外的最多2次重试.

重试次数支持两种自定义配置: 1.通过注解/xml进行固定配置；2.通过上下文进行运行时动态配置



比如多个订单服务，1,2,3 调1的时候，失败会去调2，2失败调3 .....



```xml
<!-- timeout="0" 默认是1000ms-->
	<!-- retries="":重试次数，不包含第一次调用，0代表不重试-->
	<!-- 幂等（设置重试次数）【查询、删除、修改】、非幂等（不能设置重试次数）【新增】 -->
	<dubbo:reference interface="com.atguigu.gmall.service.UserService" 
		id="userService" timeout="5000" retries="3" version="*">
		<!-- <dubbo:method name="getUserAddressList" timeout="1000"></dubbo:method> -->
	</dubbo:reference>
```

我上面的版本和下面的版本不一样，默认值是不一样的

```java
	@DubboReference(loadbalance="random",timeout=1000,retries = 3) //dubbo直连  默认2次 dubbo v2.7.7
			UserService userService;
```



通过RpcContext进行运行时动态配置，优先级高于注解/xml进行的固定配置(两者都配置的情况下，以RpcContext配置为准).

```java
// dubbo服务调用前，通过RpcContext动态设置本次调用的重试次数
RpcContext rpcContext = RpcContext.getContext();
rpcContext.setAttachment("retries", 5);
```



## 多版本



在 Dubbo 中为同一个服务配置多个版本

当一个接口实现，出现不兼容升级时，可以用版本号过渡，版本号不同的服务相互间不引用。

可以按照以下的步骤进行版本迁移：

1. 在低压力时间段，先升级一半提供者为新版本
2. 再将所有消费者升级为新版本
3. 然后将剩下的一半提供者升级为新版本



xml

```xml
提供者：
<dubbo:service interface="com.foo.BarService" version="1.0.0" />
<dubbo:service interface="com.foo.BarService" version="2.0.0" />

消费者
<dubbo:reference id="barService" interface="com.foo.BarService" version="1.0.0" />
<dubbo:reference id="barService" interface="com.foo.BarService" version="2.0.0" />



不区分版本
<dubbo:reference id="barService" interface="com.foo.BarService" version="*" />
```

注解

```java
// consumer
@DubboReference(loadbalance="random",timeout=1000,retries = 3,version = "1") //dubbo直连
			UserService userService;
	

// provider

@DubboService(version = "1")
public class UserServiceImpl implements UserService {


```





## 本地存根



调用之前提前数据校验等

在 Dubbo 中**利用本地存根在客户端执行部分逻辑**

远程服务后，客户端通常只剩下接口，而实现全在服务器端，但提供方有些时候想在客户端也执行部分逻辑，比如：做 ThreadLocal 缓存，**提前验证参数**，调用失败后伪造容错数据等等，此时就需要在 API 中带上 Stub，客户端生成 Proxy 实例，会把 Proxy 通过构造函数传给 Stub ，然后把 Stub 暴露给用户，Stub 可以决定要不要去调 Proxy。



1. Stub 必须有可传入 Proxy 的构造函数。
2. 在 interface 旁边放一个 Stub 实现，它实现 BarService 接口，并有一个传入远程 BarService 实例的构造函数 



![image-20210713000250920](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210713000250920.png)



xml配置提供者

```xml
 <dubbo:service interface="com.foo.BarService" stub="true" />
或者是全限点类名
<dubbo:service interface="com.foo.BarService" stub="com.foo.BarServiceStub" />
```

服务提供者代码

```java
package com.foo;
public class BarServiceStub implements BarService {
    private final BarService barService;
    
    // 构造函数传入真正的远程代理对象
    public BarServiceStub(BarService barService){
        this.barService = barService;
    }
 
    public String sayHello(String name) {
        // 此代码在客户端执行, 你可以在客户端做ThreadLocal本地缓存，或预先验证参数是否合法，等等
        try {
            return barService.sayHello(name);
        } catch (Exception e) {
            // 可以容错，可以做任何AOP拦截事项
            return "容错数据";
        }
    }
}
```



##  直连提供者



在开发及测试环境下，经常需要绕过注册中心，只测试指定服务提供者，这时候可能需要点对点直连，点对点直连方式，将以服务接口为单位，忽略注册中心的提供者列表，A 接口配置点对点，不影响 B 接口从注册中心获取列表。



通过 XML 配置

如果是线上需求需要点对点，可在 `<dubbo:reference>` 中配置 url 指向提供者，将绕过注册中心，多个地址用分号隔开，配置如下：

```xml
<dubbo:reference id="xxxService" interface="com.alibaba.xxx.XxxService" url="dubbo://localhost:20890" />
```



## 负载均衡

Dubbo 提供的集群负载均衡策略

在集群负载均衡时，Dubbo 提供了多种均衡策略，缺省为 `random` 随机调用。



### Random LoadBalance

- **随机**，按权重设置随机概率。
- 在一个截面上碰撞的概率高，但调用量越大分布越均匀，而且按概率使用权重后也比较均匀，有利于动态调整提供者权重。

### RoundRobin LoadBalance

- **轮询**，按公约后的权重设置轮询比率。
- 存在慢的提供者累积请求的问题，比如：第二台机器很慢，但没挂，当请求调到第二台时就卡在那，久而久之，所有请求都卡在调到第二台上。

### LeastActive LoadBalance

- **最少活跃调用数**，相同活跃数的随机，活跃数指调用前后计数差。
- 使慢的提供者收到更少请求，因为越慢的提供者的调用前后计数差会越大。

### ConsistentHash LoadBalance

- **一致性 Hash**，相同参数的请求总是发到同一提供者。
- 当某一台提供者挂时，原本发往该提供者的请求，基于虚拟节点，平摊到其它提供者，不会引起剧烈变动。
- 算法参见：http://en.wikipedia.org/wiki/Consistent_hashing
- 缺省只对第一个参数 Hash，如果要修改，请配置 `<dubbo:parameter key="hash.arguments" value="0,1" />`
- 缺省用 160 份虚拟节点，如果要修改，请配置 `<dubbo:parameter key="hash.nodes" value="320" />`

### 配置

#### 服务端服务级别

```xml
<dubbo:service interface="..." loadbalance="roundrobin" />
```

#### 客户端服务级别

```xml
<dubbo:reference interface="..." loadbalance="roundrobin" />
```

#### 服务端方法级别

```xml
<dubbo:service interface="...">
    <dubbo:method name="..." loadbalance="roundrobin"/>
</dubbo:service>
```

#### 客户端方法级别

```xml
<dubbo:reference interface="...">
    <dubbo:method name="..." loadbalance="roundrobin"/>
</dubbo:reference>
```



## 序列化







# dubbo相关高可用场景



## zookeeper注册中心宕机，dubbo直连。







```
健壮性
监控中心宕掉不影响使用，只是丢失部分采样数据
数据库宕掉后，注册中心仍能通过缓存提供服务列表查询，但不能注册新服务
注册中心对等集群，任意一台宕掉后，将自动切换到另一台
注册中心全部宕掉后，服务提供者和服务消费者仍能通过本地缓存通讯
服务提供者无状态，任意一台宕掉后，不影响使用
服务提供者全部宕掉后，服务消费者应用将无法使用，并无限次重连等待服务提供者恢复
```



```java
@DubboReference(loadbalance="random",timeout=1000,stub = "true",url = "") //dubbo直连
			UserService userService;
	
```



## 集群下dubbo负载均衡配置





![image-20210713012549455](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210713012549455.png)



```java
@DubboService(version = "1",loadbalance = "random") // 默认就是random  消费者也是这样配
```



## 服务熔断与降级处理



### 服务降级



可以通过服务降级功能临时屏蔽某个出错的非关键服务，并定义降级后的返回策略。



```java
		RegistryFactory registryFactory = ExtensionLoader.getExtensionLoader(RegistryFactory.class).getAdaptiveExtension();
		Registry registry = registryFactory.getRegistry(URL.valueOf("zookeeper://10.20.153.10:2181"));
		registry.register(URL.valueOf("override://0.0.0.0/com.foo.BarService?category=configurators&dynamic=false&application=foo&mock=force:return+null"));

```



:bulb: 

- `mock=force:return+null` 表示消费方对该服务的方法调用都直接返回 null 值，不发起远程调用。用来屏蔽不重要服务不可用时对调用方的影响。
- 还可以改为 `mock=fail:return+null` 表示消费方对该服务的方法调用在失败后，再返回 null 值，不抛异常。用来容忍不重要服务不稳定时对调用方的影响。



### 集群容错(推荐Hystrix)





集群调用失败时，Dubbo 提供的容错方案

在集群调用失败时，Dubbo 提供了多种容错方案，缺省为 failover 重试。

![image-20210713013454267](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210713013454267.png)



#### 容错策略



##### Failfast Cluster

快速失败，只发起一次调用，失败立即报错。通常用于非幂等性的写操作，比如新增记录。

##### Failsafe Cluster

失败安全，出现异常时，直接忽略。通常用于写入审计日志等操作。

##### Failback Cluster

失败自动恢复，后台记录失败请求，定时重发。通常用于消息通知操作。

##### Forking Cluster

并行调用多个服务器，只要一个成功即返回。通常用于实时性要求较高的读操作，但需要浪费更多服务资源。可通过 `forks="2"` 来设置最大并行数。

##### Broadcast Cluster

广播调用所有提供者，逐个调用，任意一台报错则报错。通常用于通知所有提供者更新缓存或日志等本地资源信息。

现在广播调用中，可以通过 broadcast.fail.percent 配置节点调用失败的比例，当达到这个比例后，BroadcastClusterInvoker 将不再调用其他节点，直接抛出异常。 broadcast.fail.percent 取值在 0～100 范围内。默认情况下当全部调用失败后，才会抛出异常。 broadcast.fail.percent 只是控制的当失败后是否继续调用其他节点，并不改变结果(任意一台报错则报错)。broadcast.fail.percent 参数 在 dubbo2.7.10 及以上版本生效。

Broadcast Cluster 配置 broadcast.fail.percent。

broadcast.fail.percent=20 代表了当 20% 的节点调用失败就抛出异常，不再调用其他节点。

```text
@reference(cluster = "broadcast", parameters = {"broadcast.fail.percent", "20"})
```



#### hystrix

```java
1.依赖
2.@EnableHystrix
3.方法级上 @HystrixCommand   // google
```



# dubbo的原理设计简单介绍



## 框架设计



![image-20210721230240274](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210721230240274.png)

![image-20210721230400706](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210721230400706.png)







## 启动解析，加载配置信息



容器启动，解析标签，保存设置属性值到对应的对象中

1.

![image-20210721230725692](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210721230725692.png)



> dubbo的配置文件解析也是通过实现 spring 的BeanDefinitionParser 来实现的，解析方法在parse方法中，一个个解析标签



2.注册了名称空间,构造函数中init了 很多标签解析器

![image-20210721231059799](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210721231059799.png)



![image-20210721231258836](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210721231258836.png)



:bulb:   注意  ServiceBean 和ReferenceBean 和其他不一样，比较特殊





## 服务暴露



![image-20210721233736366](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210721233736366.png)

1.



![image-20210721232028264](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210721232028264.png)



![image-20210721232802505](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210721232802505.png)



```java
public class ServiceBean<T> extends ServiceConfig<T> implements InitializingBean, DisposableBean,
        ApplicationContextAware, BeanNameAware, ApplicationEventPublisherAware {

            ...
                
                
     @Override
    public void afterPropertiesSet() throws Exception {
        if (StringUtils.isEmpty(getPath())) {
            if (StringUtils.isNotEmpty(beanName)
                    && StringUtils.isNotEmpty(getInterface())
                    && beanName.startsWith(getInterface())) {
                setPath(beanName);
            }
        }
    }
            ....
```



**在spring 容器启动时 ，dubbo有很多事件会回调，并且监听一些event事件** 

在获取并设置属性值保存在 `ServiceBean` ，容器创建完成 ，会触发回调

在  `ServiceConfig` 中有一个 export核心方法

![image-20210721233512329](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210721233512329.png)



```java
 protected synchronized void doExport() {
       ......
        doExportUrls();// 执行暴露地址
    }
```



```java
 private void doExportUrls() {
        ServiceRepository repository = ApplicationModel.getServiceRepository();
        ServiceDescriptor serviceDescriptor = repository.registerService(getInterfaceClass());
        repository.registerProvider(
                getUniqueServiceName(),
                ref,
                serviceDescriptor,
                this,
                serviceMetadata
        );
		//加载注册中心服务信息
        List<URL> registryURLs = ConfigValidationUtils.loadRegistries(this, true);
		// 暴露协议  如 <dubbo:protocol name=""  port="" ....> 中的端口协议等等
        for (ProtocolConfig protocolConfig : protocols) {
            String pathKey = URL.buildKey(getContextPath(protocolConfig)
                    .map(p -> p + "/" + path)
                    .orElse(path), group, version);
            // In case user specified path, register service one more time to map it to path.
            repository.registerService(pathKey, interfaceClass);
            // TODO, uncomment this line once service key is unified
            serviceMetadata.setServiceKey(pathKey);
            doExportUrlsFor1Protocol(protocolConfig, registryURLs);
        }
    }
```



进入 doExportUrlsFor1Protocol 方法

Invoker执行者，根据 接口 和实现 包装执行者信息 

![image-20210721234225389](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210721234225389.png)

```java
 Exporter<?> exporter = PROTOCOL.export(wrapperInvoker); //暴露invoke
```

wrapper 适配器 ， java  spi机制，



```java
@SPI("dubbo")
public interface Protocol {
```

![image-20210721234734552](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210721234734552.png)

![image-20210721234723357](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210721234723357.png)



![image-20210721235253731](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210721235253731.png)



打开服务器，获取服务地址

```java
 @Override
    public <T> Exporter<T> export(Invoker<T> invoker) throws RpcException {
        URL url = invoker.getUrl();

        // export service.
        String key = serviceKey(url);
        DubboExporter<T> exporter = new DubboExporter<T>(invoker, key, exporterMap);
   .......

        openServer(url);
        optimizeSerialization(url);

        return exporter;
    }



private void openServer(URL url) {
    // find server.
    String key = url.getAddress();
    //client can export a service which's only for server to invoke
    boolean isServer = url.getParameter(IS_SERVER_KEY, true);
    if (isServer) {
        ProtocolServer server = serverMap.get(key);
        if (server == null) {
            synchronized (this) {
                server = serverMap.get(key);
                if (server == null) {
                    serverMap.put(key, createServer(url));
                }
            }
        } else {
            // server supports reset, use together with override
            server.reset(url);
        }
    }
}

// 创建服务
private ProtocolServer createServer(URL url) {
        url = URLBuilder.from(url)
                // send readonly event when server closes, it's enabled by default
                .addParameterIfAbsent(CHANNEL_READONLYEVENT_SENT_KEY, Boolean.TRUE.toString())
                // enable heartbeat by default
                .addParameterIfAbsent(HEARTBEAT_KEY, String.valueOf(DEFAULT_HEARTBEAT))
                .addParameter(CODEC_KEY, DubboCodec.NAME)
                .build();
        String str = url.getParameter(SERVER_KEY, DEFAULT_REMOTING_SERVER);

        if (str != null && str.length() > 0 && !ExtensionLoader.getExtensionLoader(Transporter.class).hasExtension(str)) {
            throw new RpcException("Unsupported server type: " + str + ", url: " + url);
        }

        ExchangeServer server;
        try {
            // 绑定到交换机服务，通过 服务器url 和 请求处理器   底层是netty  会创建一个netty的服务器，也就是创建 启动netty服务器，监听 指定端口
            server = Exchangers.bind(url, requestHandler);
        } catch (RemotingException e) {
            throw new RpcException("Fail to start server(url: " + url + ") " + e.getMessage(), e);
        }

        str = url.getParameter(CLIENT_KEY);
        if (str != null && str.length() > 0) {
            Set<String> supportedTypes = ExtensionLoader.getExtensionLoader(Transporter.class).getSupportedExtensions();
            if (!supportedTypes.contains(str)) {
                throw new RpcException("Unsupported client type: " + str);
            }
        }
		// dubbo 协议的服务器
        return new DubboProtocolServer(server);
    }
```



每一个url的调用 会去保存 了对应的执行器 中的 注册表 中 寻找



## 服务引用



.............



![image-20210722000319105](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210722000319105.png)





![image-20210722000225954](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210722000225954.png)



用 的是 dubbo的协议 

进入 `DubboProtocol` 中

```java
@Override
public <T> Invoker<T> protocolBindingRefer(Class<T> serviceType, URL url) throws RpcException {
    optimizeSerialization(url);

    // create rpc invoker.
    DubboInvoker<T> invoker = new DubboInvoker<T>(serviceType, url, getClients(url), invokers);
    invokers.add(invoker);

    return invoker;
}
```



注册中心的 refer

![image-20210722000736959](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210722000736959.png)





## 服务调用



![/dev-guide/images/dubbo-extension.jpg](https://dubbo.apache.org/imgs/dev/dubbo-extension.jpg)



1.



![image-20210722000855263](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210722000855263.png)

```java
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        if (method.getDeclaringClass() == Object.class) {
            return method.invoke(invoker, args);
        }
        String methodName = method.getName();
        Class<?>[] parameterTypes = method.getParameterTypes();
        if (parameterTypes.length == 0) {
            if ("toString".equals(methodName)) {
                return invoker.toString();
            } else if ("$destroy".equals(methodName)) {
                invoker.destroy();
                return null;
            } else if ("hashCode".equals(methodName)) {
                return invoker.hashCode();
            }
        } else if (parameterTypes.length == 1 && "equals".equals(methodName)) {
            return invoker.equals(args[0]);
        }
        //封装成rpc 远程调用的对象
        RpcInvocation rpcInvocation = new RpcInvocation(method, invoker.getInterface().getName(), args);
        String serviceKey = invoker.getUrl().getServiceKey();
        rpcInvocation.setTargetServiceUniqueName(serviceKey);
      
        if (consumerModel != null) {
            rpcInvocation.put(Constants.CONSUMER_MODEL, consumerModel);
            rpcInvocation.put(Constants.METHOD_MODEL, consumerModel.getMethodModel(method));
        }
		// 
        return invoker.invoke(rpcInvocation).recreate();
    }
```





