

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



## simple-code











