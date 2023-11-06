
<!-- TOC -->

- [dubbo初识](#dubbo初识)
	- [相关基础概念](#相关基础概念)
	- [使用zk做注册中心](#使用zk做注册中心)
	- [dubbo-admin](#dubbo-admin)
	- [simple-case](#simple-case)
		- [api(公共接口提取)](#api公共接口提取)
		- [生产者(provider)](#生产者provider)
		- [消费者(consumer)](#消费者consumer)
	- [springboot-dubbo](#springboot-dubbo)
		- [api](#api)
		- [服务提供者](#服务提供者)
		- [服务消费者](#服务消费者)
	- [dubbo的常用配置](#dubbo的常用配置)
		- [**所有模块配置**](#所有模块配置)
		- [参数覆盖优先级策略](#参数覆盖优先级策略)
		- [服务配置](#服务配置)
		- [服务引用配置](#服务引用配置)
		- [协议配置](#协议配置)
		- [应用配置](#应用配置)
		- [注册中心配置](#注册中心配置)
		- [监控中心配置](#监控中心配置)
		- [提供方配置](#提供方配置)
		- [消费方配置](#消费方配置)
		- [方法配置](#方法配置)
		- [参数配置](#参数配置)

<!-- /TOC -->
# dubbo初识



## 相关基础概念



> 官网

https://dubbo.apache.org/zh/docs/v3.0/

> 什么是RPC

RPC全称为remote procedure call，即**远程过程调用**。比如两台服务器A和B，A服务器上部署一个应用，B服务器上部署一个应用，A服务器上的应用想调用B服务器上的应用提供的方法，由于两个应用不在一个内存空间，不能直接调用，所以需要通过网络来表达调用的语义和传达调用的数据。

需要注意的是RPC并不是一个具体的技术，而是指整个网络远程调用过程。 

而不需要了解底层网络技术的协议，在面向对象的编程语言中，远程过程调用即是 远程方法调用

![image-20210713014915196](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210713014915196.png)

> 什么是dubbo

Apache Dubbo 是一款微服务RPC开发框架，它提供了 RPC通信 与 微服务治理 两大关键能力。这意味着，使用 Dubbo 开发的微服务，将具备相互之间的远程发现与通信能力， 同时利用 Dubbo 提供的丰富服务治理能力，可以实现诸如服务发现、负载均衡、流量调度等服务治理诉求。同时 Dubbo 是高度可扩展的，用户几乎可以在任意功能点去定制自己的实现，以改变框架的默认行为来满足自己的业务需求。

- 开箱即用
  - 易用性高，如 Java 版本的面向接口代理特性能实现本地透明调用
  - 功能丰富，基于原生库或轻量扩展即可实现绝大多数的微服务治理能力
- 超大规模微服务集群实践
  - 高性能的跨进程通信协议
  - 地址发现、流量治理层面，轻松支持百万规模集群实例
- 企业级微服务治理能力
  - 服务测试
  - 服务Mock

> 架构图
>
> - 注册中心。协调 Consumer 与 Provider 之间的地址注册与发现
> - 配置中心。
>   - 存储 Dubbo 启动阶段的全局配置，保证配置的跨环境共享与全局一致性
>   - 负责服务治理规则（路由规则、动态配置等）的存储与推送。
> - 元数据中心。
>   - 接收 Provider 上报的服务接口元数据，为 Admin 等控制台提供运维能力（如服务测试、接口文档等）
>   - 作为服务发现机制的补充，提供额外的接口/方法级别配置信息的同步能力，相当于注册中心的额外扩展

![image-20210706231754389](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210706231754389.png)



dubbo官网-dubbo整体设计

![image-20210710002827261](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210710002827261.png)

**interface是消费者使用的接口左边，右边是提供者的接口实现，interface在中间是因为我们常常会和兄弟部门沟通以maven私服jar的形式来提供给我们接口来调用兄弟部门服务，是公共的**



- **config 配置层**：对外配置接口，以 `ServiceConfig`, `ReferenceConfig` 为中心，可以直接初始化配置类，也可以通过 spring 解析配置生成配置类
- **proxy 服务代理层**：服务接口透明代理，生成服务的客户端 Stub 和服务器端 Skeleton, 以 `ServiceProxy` 为中心，扩展接口为 `ProxyFactory`
- **registry 注册中心层**：封装服务地址的注册与发现，以服务 URL 为中心，扩展接口为 `RegistryFactory`, `Registry`, `RegistryService`
- **cluster 路由层**：封装多个提供者的路由及负载均衡，并桥接注册中心，以 `Invoker` 为中心，扩展接口为 `Cluster`, `Directory`, `Router`, `LoadBalance`
- **monitor 监控层**：RPC 调用次数和调用时间监控，以 `Statistics` 为中心，扩展接口为 `MonitorFactory`, `Monitor`, `MonitorService`
- **protocol 远程调用层**：封装 RPC 调用，以 `Invocation`, `Result` 为中心，扩展接口为 `Protocol`, `Invoker`, `Exporter`
- **exchange 信息交换层**：封装请求响应模式，同步转异步，以 `Request`, `Response` 为中心，扩展接口为 `Exchanger`, `ExchangeChannel`, `ExchangeClient`, `ExchangeServer`
- **transport 网络传输层**：抽象 mina 和 netty 为统一接口，以 `Message` 为中心，扩展接口为 `Channel`, `Transporter`, `Client`, `Server`, `Codec`
- **serialize 数据序列化层**：可复用的一些工具，扩展接口为 `Serialization`, `ObjectInput`, `ObjectOutput`, `ThreadPool`







> 服务发现原理
>
> 0.服务提供者运行在容器中
>
> 1.服务提供者注册到注册中心中
>
> 2.消费者向注册中心寻找服务提供者的调用信息等
>
> 3.请求一次获取一次元信息
>
> 4.消费者获取到提供者的信息RPC调用
>
> 5.服务监控
>
> **init  0,1,2 是服务启动的时侯要做的事情**
>
> **async 3 5 是异步调用**
>
> **4 是同步调用**

<img src="https://dubbo.apache.org/imgs/architecture.png" alt="//imgs/architecture.png" style="zoom:67%;" />



## 使用zk做注册中心



> 两种方式，
>
> - 一种是docker安装， `docket pull zookeeper`
> - 还有一种是去官网选择zk tar包上传到服务器上，然后 `tar -zxvf  xxx.tar.gz` 然后修改 zoo_sample.cfg 为 zoo.cfg 然后启动就可以了 进入bin目录下  `./zkServer.sh start` 启动，`然后./zkServer.sh stop` 停止  查看 zk状态  `./zkServer.sh status`









## dubbo-admin



> 1.安装 dubbo admin

https://github.com/apache/dubbo-admin/tree/develop

<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210708001556478.png" alt="image-20210708001556478" style="zoom:67%;" />



> 2.修改 server工程的配置文件  application.properties 修改zk的地址为 阿里云 自己安装的zk地址

```properties
admin.registry.address=zookeeper://xxx:2181
admin.config-center=zookeeper://xxx:2181
admin.metadata-report.address=zookeeper://xxx:2181
```



> 3.打包 本身是一个springboot工程

直接 `mvn clean package `  前端是vue  node 启动  `npm run dev`

![image-20210708002328649](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210708002328649.png)

![image-20210708002341065](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210708002341065.png)

运行 `java -jar xxxx.jar`  后端就启动了

前端也是这样 ` npm run dev 就可以了`



## simple-case



> 配置文件形式



### api(公共接口提取)

> BO

```java
package com.viw.api.bean;

import lombok.Data;

import java.io.Serializable;


/**
 * User address
 */
@Data
@AllArgsConstructor
@NoArgsConstructor
public class UserAddress implements Serializable {
	
	private Integer id;
    private String userAddress; //用户地址
    private String userId; //用户id
    private String consignee; //收货人
    private String phoneNum; //电话号码
    private String isDefault; //是否为默认地址    Y-是     N-否

}

```

> 接口

```java
package com.viw.api.service;

import java.util.List;

import com.viw.api.bean.UserAddress;


public interface OrderService {
	
	/**
	 * 初始化订单
	 * @param userId
	 */
	public List<UserAddress> initOrder(String userId);
}

```



```java
package com.viw.api.service;

import java.util.List;

import com.viw.api.bean.UserAddress;

/**
 * 用户服务
 *
 */
public interface UserService {
	
	/**
	 * 按照用户id返回所有的收货地址
	 * @param userId
	 * @return
	 */
	public List<UserAddress> getUserAddressList(String userId);

}
```





### 生产者(provider)



> 接口实现类

```java
import com.viw.api.bean.UserAddress;
import com.viw.api.service.UserService;

public class UserServiceImpl implements UserService {

	@Override
	public List<UserAddress> getUserAddressList(String userId) {
		System.out.println("UserServiceImpl.....old...");
		// TODO Auto-generated method stub
		UserAddress address1 = new UserAddress(1, "北京", "1", "李老师", "010", "Y");
		UserAddress address2 = new UserAddress(2, "深圳", "1", "王老师", "010", "N");
		
		return Arrays.asList(address1,address2);
	}

}
```



```java
public class UserServiceImpl2 implements UserService {

	@Override
	public List<UserAddress> getUserAddressList(String userId) {
		System.out.println("UserServiceImpl.....new...");
		// TODO Auto-generated method stub
		UserAddress address1 = new UserAddress(1, "北京", "1", "李老师", "010", "Y");
		UserAddress address2 = new UserAddress(2, "深圳", "1", "王老师", "010", "N");
		
		return Arrays.asList(address1,address2);
	}

```



> provider.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://dubbo.apache.org/schema/dubbo http://dubbo.apache.org/schema/dubbo/dubbo.xsd
		http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd">

	<!-- 1、指定当前服务/应用的名字（同样的服务名字相同，不要和别的服务同名） -->
	<dubbo:application name="user-service-provider"></dubbo:application>

	<!-- 2、指定注册中心的位置 -->
	<!-- <dubbo:registry address="zookeeper://127.0.0.1:2181"></dubbo:registry> -->
	<dubbo:registry protocol="zookeeper" address="xxx:2181"></dubbo:registry>
	
	<!-- 3、指定通信规则（通信协议？通信端口） -->
	<dubbo:protocol name="dubbo" port="20882"></dubbo:protocol>
	
	<!-- 4、暴露服务 其他服务就可以调用了   ref：指向服务的真正的实现对象   interface:接口的全限定类名-->
	<dubbo:service interface="com.viw.api.service.UserService"
		ref="userServiceImpl01" timeout="1000" version="1.0.0">
		<dubbo:method name="getUserAddressList" timeout="1000"></dubbo:method>
	</dubbo:service>
	
	<!--统一设置服务提供方的规则  -->
	<dubbo:provider timeout="1000"></dubbo:provider>
	
	
	<!-- 服务的实现 -->
	<bean id="userServiceImpl01" class="com.viw.service.impl.UserServiceImpl"></bean>
	
	
	<dubbo:service interface="com.viw.api.service.UserService"
		ref="userServiceImpl02" timeout="1000" version="2.0.0">
		<dubbo:method name="getUserAddressList" timeout="1000"></dubbo:method>
	</dubbo:service>
	<bean id="userServiceImpl02" class="com.viw.service.impl.UserServiceImpl2"></bean>
	
	<!--监控中心，直连 或者 加入 -->
	<dubbo:monitor protocol="registry"></dubbo:monitor>
	
</beans>

```





### 消费者(consumer)



> 接口
>
> rpc调用UserService



```java
@Service
public class OrderServiceImpl implements OrderService {

	@Autowired
	UserService userService;
	@Override
	public List<UserAddress> initOrder(String userId) {
		// TODO Auto-generated method stub
		System.out.println("用户id："+userId);
		//1、查询用户的收货地址
		List<UserAddress> addressList = userService.getUserAddressList(userId);
		for (UserAddress userAddress : addressList) {
			System.out.println(userAddress.getUserAddress());
		}
		return addressList;
	}
	

```



> consumer.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
		http://dubbo.apache.org/schema/dubbo http://dubbo.apache.org/schema/dubbo/dubbo.xsd
		http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd">
	<context:component-scan base-package="com.viw.web.service.impl"></context:component-scan>


	<dubbo:application name="order-service-consumer"></dubbo:application>
	
	<dubbo:registry address="zookeeper://xxx:2181"></dubbo:registry>
	
	<!--  配置本地存根-->
	
	<!--声明需要调用的远程服务的接口；生成远程服务代理  -->
	<!-- 
		1）、精确优先 (方法级优先，接口级次之，全局配置再次之)
		2）、消费者设置优先(如果级别一样，则消费方优先，提供方次之)
	-->
	<!-- timeout="0" 默认是1000ms-->
	<!-- retries="":重试次数，不包含第一次调用，0代表不重试-->
	<!-- 幂等（设置重试次数）【查询、删除、修改】、非幂等（不能设置重试次数）【新增】 -->
	<dubbo:reference interface="com.viw.api.service.UserService"
		id="userService" timeout="5000" retries="3" version="*">
		<!-- <dubbo:method name="getUserAddressList" timeout="1000"></dubbo:method> -->
	</dubbo:reference>
		
	<!-- 配置当前消费者的统一规则：所有的服务都不检查 -->
	<dubbo:consumer check="false" timeout="5000"></dubbo:consumer>

	<!--监控中心，直连 或者 加入 -->
	<dubbo:monitor protocol="registry"></dubbo:monitor>
	<!-- <dubbo:monitor address="127.0.0.1:7070"></dubbo:monitor> -->
	
</beans>


```



## springboot-dubbo



> 三种方式
>
> ```java
>  * SpringBoot与dubbo整合的三种方式：
>  * 1）、导入dubbo-starter，在application.properties配置属性，使用@Service【暴露服务】使用	@Reference【引用服务】
>        在消费者上 要引入的接口类上标记 @DubboReference  提供者类上使用 @DubboService
>  * 2）、保留dubbo xml配置文件;
>  * 		导入dubbo-starter，使用@ImportResource导入dubbo的配置文件即可 
>      	//@ImportResource(locations="classpath:provider.xml")
>  * 3）、使用注解API的方式：
>      	//  https://dubbo.apache.org/zh/docs/v2.7/user/configuration/api/
>  * 		将每一个组件手动创建到容器中,让dubbo来扫描其他的组件
>         
> ```
>
> 第三种方式
>
> ```java
> 
> /**
>  * api的方式配置
>  */
> @Configuration
> public class MyDubboConfig {
> 	
> 	@Bean
> 	public ApplicationConfig applicationConfig() {
> 		ApplicationConfig applicationConfig = new ApplicationConfig();
> 		applicationConfig.setName("boot-user-service-provider");
> 		return applicationConfig;
> 	}
> 	
> 	//<dubbo:registry protocol="zookeeper" address="127.0.0.1:2181"></dubbo:registry>
> 	@Bean
> 	public RegistryConfig registryConfig() {
> 		RegistryConfig registryConfig = new RegistryConfig();
> 		registryConfig.setProtocol("zookeeper");
> 		registryConfig.setAddress("127.0.0.1:2181");
> 		return registryConfig;
> 	}
> 	
> 	//<dubbo:protocol name="dubbo" port="20882"></dubbo:protocol>
> 	@Bean
> 	public ProtocolConfig protocolConfig() {
> 		ProtocolConfig protocolConfig = new ProtocolConfig();
> 		protocolConfig.setName("dubbo");
> 		protocolConfig.setPort(20882);
> 		return protocolConfig;
> 	}
> 	
> 	/**
> 	 *<dubbo:service interface="com.atguigu.gmall.service.UserService" 
> 		ref="userServiceImpl01" timeout="1000" version="1.0.0">
> 		<dubbo:method name="getUserAddressList" timeout="1000"></dubbo:method>
> 	</dubbo:service>
> 	 */
> 	@Bean
> 	public ServiceConfig<UserService> userServiceConfig(UserService userService){
> 		ServiceConfig<UserService> serviceConfig = new ServiceConfig<>();
> 		serviceConfig.setInterface(UserService.class);
> 		serviceConfig.setRef(userService);
> 		serviceConfig.setVersion("1.0.0");
> 		
> 		//配置每一个method的信息
> 		MethodConfig methodConfig = new MethodConfig();
> 		methodConfig.setName("getUserAddressList");
> 		methodConfig.setTimeout(1000);
> 		
> 		//将method的设置关联到service配置中
> 		List<MethodConfig> methods = new ArrayList<>();
> 		methods.add(methodConfig);
> 		serviceConfig.setMethods(methods);
> 		
> 		//ProviderConfig
> 		//MonitorConfig
> 		
> 		return serviceConfig;
> 	}
> 
> ```
>
> 



### api

> 公共接口引入，一般都是和兄弟部门沟通，让他们通过接口，然后放到公司私服，然后我们去引入就可以了

![image-20210709014335246](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210709014335246.png)



### 服务提供者



api方式配置

```java
package com.viw.provider.config;

import java.util.ArrayList;
import java.util.List;

import com.viw.api.service.UserService;
import org.apache.dubbo.config.*;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;


/**
 * api的方式配置
 */
@Configuration
public class MyDubboConfig {
	
	@Bean
	public ApplicationConfig applicationConfig() {
		ApplicationConfig applicationConfig = new ApplicationConfig();
		applicationConfig.setName("boot-user-service-provider");
		return applicationConfig;
	}
	
	//<dubbo:registry protocol="zookeeper" address="127.0.0.1:2181"></dubbo:registry>
	@Bean
	public RegistryConfig registryConfig() {
		RegistryConfig registryConfig = new RegistryConfig();
		registryConfig.setProtocol("zookeeper");
		registryConfig.setAddress("127.0.0.1:2181");
		return registryConfig;
	}
	
	//<dubbo:protocol name="dubbo" port="20882"></dubbo:protocol>
	@Bean
	public ProtocolConfig protocolConfig() {
		ProtocolConfig protocolConfig = new ProtocolConfig();
		protocolConfig.setName("dubbo");
		protocolConfig.setPort(20882);
		return protocolConfig;
	}
	
	/**
	 *<dubbo:service interface="com.atguigu.gmall.service.UserService" 
		ref="userServiceImpl01" timeout="1000" version="1.0.0">
		<dubbo:method name="getUserAddressList" timeout="1000"></dubbo:method>
	</dubbo:service>
	 */
	@Bean
	public ServiceConfig<UserService> userServiceConfig(UserService userService){
		ServiceConfig<UserService> serviceConfig = new ServiceConfig<>();
		serviceConfig.setInterface(UserService.class);
		serviceConfig.setRef(userService);
		serviceConfig.setVersion("1.0.0");
		
		//配置每一个method的信息
		MethodConfig methodConfig = new MethodConfig();
		methodConfig.setName("getUserAddressList");
		methodConfig.setTimeout(1000);
		
		//将method的设置关联到service配置中
		List<MethodConfig> methods = new ArrayList<>();
		methods.add(methodConfig);
		serviceConfig.setMethods(methods);
		
		//ProviderConfig
		//MonitorConfig
		
		return serviceConfig;
	}

}

```



接口的实现类



```java
package com.viw.provider.service.impl;

import java.util.Arrays;
import java.util.List;

import com.viw.api.bean.UserAddress;
import com.viw.api.service.UserService;
import org.apache.dubbo.config.annotation.Service;
import org.springframework.stereotype.Component;


@Service
@Component
public class UserServiceImpl implements UserService {

//	@HystrixCommand
	@Override
	public List<UserAddress> getUserAddressList(String userId) {
		// TODO Auto-generated method stub
		System.out.println("UserServiceImpl..3.....");
		UserAddress address1 = new UserAddress(1, "北京", "1", "李", "010-", "Y");
		UserAddress address2 = new UserAddress(2, "深圳", "1", "王", "010", "N");
//		try {
//			Thread.sleep(2000);
//		} catch (InterruptedException e) {
//			e.printStackTrace();
//		}
		if(Math.random()>0.5) {
			throw new RuntimeException();
		}
		return Arrays.asList(address1,address2);
	}

}

```





启动类

```java
/**
 * 1、导入依赖；
 * 		1）、导入dubbo-starter
 * 		2）、导入dubbo的其他依赖
 * @author lfy
 *
 * SpringBoot与dubbo整合的三种方式：
 * 1）、导入dubbo-starter，在application.properties配置属性，使用@Service【暴露服务】使用@Reference【引用服务】
 * 2）、保留dubbo xml配置文件;
 * 		导入dubbo-starter，使用@ImportResource导入dubbo的配置文件即可
 * 3）、使用注解API的方式：
 * 		将每一个组件手动创建到容器中,让dubbo来扫描其他的组件
 */
@EnableDubbo(scanBasePackages="com.viw.api")
@SpringBootApplication
public class ProviderApplication {

    public static void main(String[] args) {
        SpringApplication.run(ProviderApplication.class, args);
    }

}

```



application.properties

```properties
# 应用名称
spring.application.name=provider


# dubbo 的配置
#dubbo.application.name=user-service-provider
#dubbo.registry.address=127.0.0.1:2181
#dubbo.registry.protocol=zookeeper
#
#dubbo.protocol.name=dubbo
##dubbo.protocol.port=20881
#
#dubbo.monitor.protocol=registry
##dubbo.scan.base-packages=com.atguigu.gmall
```

dubbo.properties



```properties
dubbo.protocol.port=20882
```



provider.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	   xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
	   xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://dubbo.apache.org/schema/dubbo http://dubbo.apache.org/schema/dubbo/dubbo.xsd
		http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd">

	<!-- 1、指定当前服务/应用的名字（同样的服务名字相同，不要和别的服务同名） -->
	<dubbo:application name="boot-user-service-provider"></dubbo:application>

	<!-- 2、指定注册中心的位置 -->
	<!-- <dubbo:registry address="zookeeper://127.0.0.1:2181"></dubbo:registry> -->
	<dubbo:registry protocol="zookeeper" address="127.0.0.1:2181"></dubbo:registry>

	<!-- 3、指定通信规则（通信协议？通信端口） -->
	<dubbo:protocol name="dubbo" port="20882"></dubbo:protocol>

	<!-- 4、暴露服务   ref：指向服务的真正的实现对象 -->
	<dubbo:service interface="com.viw.api.service.UserService"
				   ref="userServiceImpl01" timeout="1000" version="1.0.0">
		<dubbo:method name="getUserAddressList" timeout="1000"></dubbo:method>
	</dubbo:service>

	<!--统一设置服务提供方的规则  -->
	<dubbo:provider timeout="1000"></dubbo:provider>


	<!-- 服务的实现 -->
	<bean id="userServiceImpl01" class="com.viw.provider.service.impl.UserServiceImpl"></bean>


	<!-- 连接监控中心 -->
	<dubbo:monitor protocol="registry"></dubbo:monitor>

</beans>

```





###  服务消费者

![image-20210709014649848](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210709014649848.png)



## dubbo的常用配置

https://dubbo.apache.org/zh/docs/v2.7/user/references/xml/

目前springboot比较多，xml的比较少了，但是可以知道dubbo有哪些参数是可以配置的，

### **所有模块配置**

| `<dubbo:service/>`     | 服务配置     | 用于暴露一个服务，定义服务的元信息，一个服务可以用多个协议暴露，一个服务也可以注册到多个注册中心 |
| ---------------------- | ------------ | ------------------------------------------------------------ |
| `<dubbo:reference/>`   | 引用配置     | 用于创建一个远程服务代理，一个引用可以指向多个注册中心       |
| `<dubbo:protocol/>`    | 协议配置     | 用于配置提供服务的协议信息，协议由提供方指定，消费方被动接受 |
| `<dubbo:application/>` | 应用配置     | 用于配置当前应用信息，不管该应用是提供者还是消费者           |
| `<dubbo:module/>`      | 模块配置     | 用于配置当前模块信息，可选                                   |
| `<dubbo:registry/>`    | 注册中心配置 | 用于配置连接注册中心相关信息                                 |
| `<dubbo:monitor/>`     | 监控中心配置 | 用于配置连接监控中心相关信息，可选                           |
| `<dubbo:provider/>`    | 提供方配置   | 当 ProtocolConfig 和 ServiceConfig 某属性没有配置时，采用此缺省值，可选 |
| `<dubbo:consumer/>`    | 消费方配置   | 当 ReferenceConfig 某属性没有配置时，采用此缺省值，可选      |
| `<dubbo:method/>`      | 方法配置     | 用于 ServiceConfig 和 ReferenceConfig 指定方法级的配置信息   |
| `<dubbo:argument/>`    | 参数配置     | 用于指定方法参数配置                                         |



### 参数覆盖优先级策略

- 方法级优先，接口级次之，全局配置再次之。
- 如果级别一样，则消费方优先，提供方次之。

![image-20210710005645871](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210710005645871.png)







### 服务配置

服务提供方<dubbo:service />
服务发现类
1、interface
dubbo服务暴露的接口全路径，例如：`<dubbo:service interface="com.dubbo.service.UserService" />`
2、ref
dubbo服务暴露的接口的实现类例如：

```
<bean id="userService" class="com.dubbo.service.impl.UserServiceImpl" />
<dubbo:service interface="com.dubbo.service.UserService" ref="userServie" />
```


3、version
服务版本号，服务提供方和服务消费方的version务必保持一致
4、group
服务分组，当一个接口有多个实现，可以用分组区分配置类：com.alibaba.dubbo.config.ServiceConfig
服务治理类
1、stub
设为true，表示使用缺省代理类名，即：接口名 + Local后缀，服务接口客户端本地代理类名，用于在客户端执行本地逻辑，如本地缓存等，该本地代理类的构造函数必须允许传入远程代理对象，构造函数如：public XxxServiceLocal(XxxService xxxService)
2、mock
设为true，表示使用缺省Mock类名，即：接口名 + Mock后缀，服务接口调用失败Mock实现类，该Mock类必须有一个无参构造函数，与Local的区别在于，Local总是被执行，而Mock只在出现非业务异常(比如超时，网络异常等)时执行，Local在远程调用之前执行，Mock在远程调用后执行。
3、token
令牌验证，为空表示不开启，如果为true，表示随机生成动态令牌，否则使用静态令牌，令牌的作用是防止消费者绕过注册中心直接访问，保证注册中心的授权功能有效，如果使用点对点调用，需关闭令牌功能
4、registry
向指定注册中心注册，在多个注册中心时使用，值为<dubbo:registry>的id属性，多个注册中心ID用逗号分隔，如果不想将该服务注册到任何registry，可将值设为N/A
5、provider
指定provider，值为<dubbo:provider>的id属性
6、deprecate
服务是否过时，如果设为true，消费方引用时将打印服务过时警告error日志
7、dynamic
服务是否动态注册，如果设为false，注册后将显示后disable状态，需人工启用，并且服务提供者停止时，也不会自动取消册，需人工禁用。
8、accesslog
设为true，将向logger中输出访问日志，也可填写访问日志文件路径，直接把访问日志输出到指定文件
9、owner
服务负责人，用于服务治理，请填写公司邮箱前缀
10、protocol
使用指定的协议暴露服务，在多协议时使用，值为<dubbo:protocol>的id属性，多个协议ID用逗号分隔，可以有dubbo/hessian/rmi/thrift/redis等，后面会详细介绍每种协议
11、register
该协议的服务是否注册到注册中心
服务调优类
1、delay
延迟注册服务时间(毫秒) ，设为-1时，表示延迟到Spring容器初始化完成时暴露服务
2、timeout
远程调用服务超时时间，如果客户端服务端都配置了以客户端为准，客户端没配以服务端为准，单位毫秒默认1000
3、retries
远程服务调用重试次数，不包括第一次调用，不需要重试请设为0，默认2
4、connections
对每个提供者的最大连接数，rmi、http、hessian等短连接协议表示限制连接数，dubbo等长连接协表示建立的长连接个数，默认100
5、loadbalance
负载均衡策略，random,roundrobin,leastactive，分别表示：随机，轮循，最少活跃调用
6、async
是否缺省异步执行，不可靠异步，只是忽略返回值，不阻塞执行线程
7、weigth
服务权重，结合负载均衡策略使用
8、executes
服务提供者每服务每方法最大可并行执行请求数
9、actives
每服务消费者每服务每方法最大并发调用数
10、proxy
生成动态代理方式，可选：jdk/javassist
11、cluster
集群方式，可选：
【failover】
失败自动切换，当出现失败，重试其他服务器（缺省），通常用于读操作，但重试会带来更长的延时，可通过retries=“2”来设置重试次数（不含第一次）

```
<dubbo:service retries="2"> 
```


或者

或者

```
<dubbo:reference retries="2"> 
或者
```

```
<dubbo:reference> 
   <dubbo:method name="findFoo" retries=2> 
<dubbo:reference/> 
```


【failfast】
快速失效，只发起一次调用，失败立即报错。通常用于非幂等性写操作，比如说新增记录

```
<dubbo:service cluster="failfast"> 
```


或者

```
<dubbo:reference cluster="failfast"
```


【failsafe】
失败安全，出现异常时，直接忽略，通常用于写入审计日志等操作

【failsafe】
失败安全，出现异常时，直接忽略，通常用于写入审计日志等操作

```
<dubbo:service cluster="failsafe"> 

```


或者

```
<dubbo:reference cluster="failsafe">

```


【failback】
失败自动恢复，后台记录失败请求，定时重发，通常用于消息通知操作

```
<dubbo:service cluster="failback"> 

```


或者

```
<dubbo:reference cluster="failback">

```


【forking】
并行调用多个服务器，只要一个成功即返回。通常用于实时性要求较高的读操作，但需要浪费更多的服务器资源。可通过forks=“2”来设置最大并行数。

```
<dubbo:service cluster="forking"> 

```


或者

```
<dubbo:reference cluster="forking">

```


12、filter
服务提供方远程调用过程拦截器名称，多个名称用逗号分隔，Spring拦截器可以拦截Web接口，无法拦截dubbo接口，但是dubbo提供了Filter扩展
13、listener
服务提供方导出服务监听器名称，多个名称用逗号分隔




### 服务引用配置



服务发现类
1、id
服务引用Bean的id，例如

```
<dubbo:reference id=“xxxService” interface=“com.xxx.XxxService” /> <!-- 增加引用远程服务配置 -->
<bean id="xxxAction" class="com.xxx.XxxAction"> <!-- 和本地服务一样使用远程服务 -->
    <property name="xxxService" ref="xxxService" />
</bean>
```


2、interface
服务接口全路径，例子同上
3、version
版本号，与服务提供者保持一致
4、group
服务分组，当一个接口有多个实现，可以用分组区分，必需和服务提供方一致
5、registry
从指定注册中心注册获取服务列表，在多个注册中心时使用，值为<dubbo:registry>的id属性，多个注册中心ID用逗号分隔

服务治理类
1、generic
是否缺省泛化接口，如果为泛化接口，将返回GenericService
2、check
启动时检查提供者是否存在，true报错，false忽略
3、url
点对点直连服务提供者地址，将绕过注册中心
4、stub
服务接口客户端本地代理类名，用于在客户端执行本地逻辑，如本地缓存等，该本地代理类的构造函数必须允许传入远程代理对象，构造函数如：public XxxServiceLocal(XxxService xxxService)
5、mock
服务接口调用失败Mock实现类名，该Mock类必须有一个无参构造函数，与Local的区别在于，Local总是被执行，而Mock只在出现非业务异常(比如超时，网络异常等)时执行，Local在远程调用之前执行，Mock在远程调用后执行
6、cache
以调用参数为key，缓存返回结果，可选：lru, threadlocal, jcache等
7、validation
是否启用JSR303标准注解验证，如果启用，将对方法参数上的注解进行校验
8、layer
layer服务调用者所在的分层。如：biz、dao、intl:web、china:acton
9、protocol
只调用指定协议的服务提供方，其它协议忽略

服务调优类
1、timeout
服务调用超时时间，如果客户端配置以客户端为准
2、retries
远程服务调用重试次数，不包括第一次调用，不需要重试请设为0
3、connections
对每个提供者的最大连接数，rmi、http、hessian等短连接协议表示限制连接数，dubbo等长连接协表示建立的长连接个数
4、loadbalance
负载均衡策略，可选值：random,roundrobin,leastactive，分别表示：随机，轮循，最少活跃调用
5、async
是否异步执行，不可靠异步，只是忽略返回值，不阻塞执行线程
6、proxy
选择动态代理实现策略，可选：javassist, jdk
7、client
客户端传输类型设置，如Dubbo协议的netty或mina
8、actives
每服务消费者每服务每方法最大并发调用数
9、cluster
集群方式，可选：
【failover】
失败自动切换，当出现失败，重试其他服务器（缺省），通常用于读操作，但重试会带来更长的延时，可通过retries=“2”来设置重试次数（不含第一次）

<dubbo:service retries="2"> 

或者

<dubbo:reference retries="2"> 

或者

```
<dubbo:reference> 
   <dubbo:method name="findFoo" retries=2> 
<dubbo:reference/> 
```

【failfast】
快速失效，只发起一次调用，失败立即报错。通常用于非幂等性写操作，比如说新增记录

<dubbo:service cluster="failfast"> 

或者

<dubbo:reference cluster="failfast"

【failsafe】
失败安全，出现异常时，直接忽略，通常用于写入审计日志等操作

<dubbo:service cluster="failsafe"> 

或者

```
<dubbo:reference cluster="failsafe">
```


【failback】

失败自动恢复，后台记录失败请求，定时重发，通常用于消息通知操作

```
<dubbo:service cluster="failback"> 
```

或者

```
<dubbo:reference cluster="failback">
```

【forking】
并行调用多个服务器，只要一个成功即返回。通常用于实时性要求较高的读操作，但需要浪费更多的服务器资源。可通过forks=“2”来设置最大并行数。

```
<dubbo:service cluster="forking">
```

 

或者

```
<dubbo:reference cluster="forking">

```

10、filter
服务消费方远程调用过程拦截器名称，多个名称用逗号分隔
11、listener
服务消费方引用服务监听器名称，多个名称用逗号分隔
12、init
是否在afterPropertiesSet()时饥饿初始化引用，否则等到有人注入或引用该实例时再初始化。





### 协议配置

### 应用配置

### 注册中心配置

### 监控中心配置

### 提供方配置

### 消费方配置

### 方法配置

###  参数配置



