# netty入门



> 2021-07-03 开始学下netty的使用

# netty的认识



> 什么是netty



**Netty**是一个[非阻塞I/O](https://zh.wikipedia.org/wiki/异步IO)客户端-服务器[框架](https://zh.wikipedia.org/wiki/軟體框架)，主要用于开发[Java](https://zh.wikipedia.org/wiki/Java)网络应用程序，如协议服务器和客户端。异步事件驱动(这里的异步是指多线程,调用式的异步，事件驱动是指 selector，netty的io模型还是基于io多路复用)的网络应用程序框架和工具用于简化网络编程，例如[TCP](https://zh.wikipedia.org/wiki/传输控制协议)和[UDP](https://zh.wikipedia.org/wiki/用户数据报协议)套接字服务器。[[2\]](https://zh.wikipedia.org/wiki/Netty#cite_note-2)Netty包括了[反应器编程模式](https://zh.wikipedia.org/wiki/反应器模式)的实现。Netty最初由[JBoss](https://zh.wikipedia.org/wiki/JBoss)开发，现在由Netty项目社区开发和维护。

除了作为异步网络应用程序框架，Netty还包括了对[HTTP](https://zh.wikipedia.org/wiki/超文本传输协议)、[HTTP2](https://zh.wikipedia.org/wiki/HTTP/2)、[DNS](https://zh.wikipedia.org/wiki/域名系统)及其他协议的支持，涵盖了在[Servlet容器](https://zh.wikipedia.org/wiki/Servlet容器)内运行的能力、对[WebSockets](https://zh.wikipedia.org/wiki/WebSocket)的支持、与[Google](https://zh.wikipedia.org/wiki/Google) [Protocol Buffers](https://zh.wikipedia.org/wiki/Protocol_Buffers)的集成、对[SSL](https://zh.wikipedia.org/wiki/傳輸層安全性協定)/[TLS](https://zh.wikipedia.org/wiki/傳輸層安全性協定)的支持以及对用于[SPDY](https://zh.wikipedia.org/wiki/SPDY)协议和消息[压缩](https://zh.wikipedia.org/wiki/数据压缩)的支持。自2004年以来，Netty一直在被积极开发。[[3\]](https://zh.wikipedia.org/wiki/Netty#cite_note-3)

从版本4.0.0开始，Netty在支持[NIO](https://zh.wikipedia.org/wiki/Java_NIO)和阻塞Java套接字的同时，还支持使用NIO.2作为后端。

【https://zh.wikipedia.org/wiki/Netty】

【https://netty.io/】



> netty的NB应用

Netty 在 Java 网络应用框架中的地位就好比：Spring 框架在 JavaEE 开发中的地位

以下的框架都使用了 Netty，因为它们有网络通信需求！

* Cassandra - nosql 数据库
* Spark - 大数据分布式计算框架
* Hadoop - 大数据分布式存储框架
* RocketMQ - ali 开源的消息队列
* ElasticSearch - 搜索引擎
* gRPC - rpc 框架
* Dubbo - rpc 框架
* Spring 5.x - flux api 完全抛弃了 tomcat ，使用 netty 作为服务器端
* Zookeeper - 分布式协调框架



> netty的优处

* Netty vs NIO，NIO工作量大，bug 多(java nio有很多bug，而netty全部解决了java nio中留下的bug)
  * 需要自己构建协议
  * 解决 TCP 传输问题，如粘包、半包
  * epoll (linux中的多路复用)空轮询导致 CPU 100%
  * 对 API 进行增强，使之更易用，如 FastThreadLocal => ThreadLocal，ByteBuf => ByteBuffer
* Netty vs 其它网络应用框架
  * Mina 由 apache 维护，将来 3.x 版本可能会有较大重构，破坏 API 向下兼容性，Netty 的开发迭代更迅速，API 更简洁、文档更优秀
  * 久经考验，16年，Netty 版本
    * 2.x 2004
    * 3.x 2008
    * 4.x 2013
    * 5.x 已废弃（没有明显的性能提升，维护成本高）![image-20210704180558964](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210704180558964.png)



# 初识netty的开发-helloworld





> server



```java
package com.viw.viwnetty.c1;

import io.netty.bootstrap.ServerBootstrap;
import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.ChannelInboundHandlerAdapter;
import io.netty.channel.ChannelInitializer;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.nio.NioServerSocketChannel;
import io.netty.channel.socket.nio.NioSocketChannel;
import io.netty.handler.codec.string.StringDecoder;

/**
 * The type Hello netty server.
 *
 * @Author: xhb
 * @email xiaobo97 @163.com gitee: https://gitee.com/xiaobo97
 * @Date: 2021 /7/4 18:10
 * @description:
 */
public class HelloNettyServer {

    /**
     * The entry point of application.
     *
     * @param args the input arguments
     */
    public static void main(String[] args) {
        //服务器启动器，组装netty组件
        new ServerBootstrap()
                //EventLoopGroup(selector,thread) 一个选择器 配 一个线程 group组
                .group(new NioEventLoopGroup())
                // 选择 server的ServerSocketChannel实现
                .channel(NioServerSocketChannel.class)
                //childHandler可以添加后面child要处理什么操作(handler) handler不同 处理操作不同，有些是编解码 有些是读写
                .childHandler(
                        // channel是client和server数据读写的通道，initalizer初始化，负责添加别的handler
                        new ChannelInitializer<NioSocketChannel>() {
                    @Override
                    protected void initChannel(NioSocketChannel nsc) throws Exception {
                        //添加具体的handler  StringDecoder 编解码
                        nsc.pipeline().addLast(new StringDecoder());
                        nsc.pipeline().addLast(new ChannelInboundHandlerAdapter(){
                            // channelRead 处理读事件
                            @Override
                            public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
                                //super.channelRead(ctx, msg);
                                // 输出上一步 解码好的 msg
                                System.out.println(msg);
                            }
                        });
                    }
                    // bind   NioServerSocketChannel绑定的端口
                }).bind(8080);
    }
}
```



> client

```java


package com.viw.viwnetty.c1;

import io.netty.bootstrap.Bootstrap;
import io.netty.channel.ChannelInitializer;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.nio.NioSocketChannel;
import io.netty.handler.codec.string.StringEncoder;

import java.net.InetSocketAddress;

/**
 * Hello netty client
 */
public class HelloNettyClient {

    /**
     * Main
     *
     * @param args args
     */
    public static void main(String[] args)  throws Exception{
        // 创建启动器
        new Bootstrap()
                // 添加eventLoop  nio  bio 的客户端去连都可以
                .group(new NioEventLoopGroup())
                // 选择 客户端channel
                .channel(NioSocketChannel.class)
                // Add a processor
                .handler(new ChannelInitializer<NioSocketChannel>() {
                    @Override //在连接建立后被调用
                    protected void initChannel(NioSocketChannel nsc) throws Exception {
                        // 编码
                        nsc.pipeline().addLast(new StringEncoder());
                    }
                })
                // Connect client
                .connect(new InetSocketAddress("127.0.0.1",8080))
                .sync()
                .channel()
                // Send data to server
                .writeAndFlush("hello netty");
    }
}
```











