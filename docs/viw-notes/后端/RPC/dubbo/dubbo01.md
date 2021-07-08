

# dubbo初识



## 相关基础概念



> 官网

https://dubbo.apache.org/zh/docs/v3.0/

> 什么是RPC

RPC全称为remote procedure call，即**远程过程调用**。比如两台服务器A和B，A服务器上部署一个应用，B服务器上部署一个应用，A服务器上的应用想调用B服务器上的应用提供的方法，由于两个应用不在一个内存空间，不能直接调用，所以需要通过网络来表达调用的语义和传达调用的数据。

需要注意的是RPC并不是一个具体的技术，而是指整个网络远程调用过程。 

而不需要了解底层网络技术的协议，在面向对象的编程语言中，远程过程调用即是 远程方法调用

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