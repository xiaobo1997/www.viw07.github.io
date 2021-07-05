

# netty-组件



![image-20210706005036462](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210706005036462.png)

#  EventLoop



## EventLoop

> eventloop，事件循环对象
>
> ```java
> 
> public interface EventLoop extends OrderedEventExecutor, EventLoopGroup {
>     EventLoopGroup parent();
> }
> ```
>
> 

EventLoop 本质是一个单线程执行器（同时维护了一个 Selector），里面有 run 方法处理 Channel 上源源不断的 io 事件。

继承关系：

* 一条线是继承自 j.u.c.ScheduledExecutorService 因此包含了线程池中所有的方法
* 另一条线是继承自 netty 自己的 OrderedEventExecutor，
  * 提供了 boolean inEventLoop(Thread thread) 方法判断一个线程是否属于此 EventLoop
  * 提供了 parent 方法来看看自己属于哪个 EventLoopGroup



## EventLoopGroup

> eventloopgroup，事件循环组
>
> ```java
> public interface EventLoopGroup extends EventExecutorGroup {
>     EventLoop next();
> 
>     ChannelFuture register(Channel var1);
> 
>     ChannelFuture register(ChannelPromise var1);
> 
>     /** @deprecated */
>     @Deprecated
>     ChannelFuture register(Channel var1, ChannelPromise var2);
> }
> ```
>
> 

EventLoopGroup 是一组 EventLoop，Channel 一般会调用 EventLoopGroup 的 register 方法来绑定其中一个 EventLoop，后续这个 Channel 上的 io 事件都由此 EventLoop 来处理（保证了 io 事件处理时的线程安全）

* 继承自 netty 自己的 EventExecutorGroup
  * 实现了 Iterable 接口提供遍历 EventLoop 的能力
  * 另有 next 方法获取集合中下一个 EventLoop

## 使用



> 一般不会去直接使用eventloop，而是eventloopgroup
>
> eventLoopGroup的simple case

```java
 public static void main(String[] args) {
        //1.创建 事件循环组
        //NioEventLoopGroup PROCESS  io case  and  ordinary task and  timed task
        /**
         * {@link io.netty.channel.MultithreadEventLoopGroup#MultithreadEventLoopGroup }
         */
        EventLoopGroup eventLoopGroup = new NioEventLoopGroup(2);
        // DefaultEventLoopGroup process ordinart task and timed task
        // EventLoopGroup eventLoopGroup1 = new DefaultEventLoopGroup();

        //2.eventLoopGroup.next()  get next case eventloop

       //io.netty.channel.nio.NioEventLoop@64cd705f
        System.out.println(eventLoopGroup.next());
        //io.netty.channel.nio.NioEventLoop@9225652
        System.out.println(eventLoopGroup.next());
        //io.netty.channel.nio.NioEventLoop@64cd705f
        System.out.println(eventLoopGroup.next());

        // 3.execute ordinart task(can executor sync task , for example the task processing time is too long)
        eventLoopGroup.next().submit(()->log.debug("netty event==="));//01:14:49.456 [nioEventLoopGroup-2-1] DEBUG com.viw.viwnetty.c2.EventLoopTest - netty event===
        log.debug("{}","main");//01:14:49.460 [main] DEBUG com.viw.viwnetty.c2.EventLoopTest - main
        
        // 4. executor schedual task
        eventLoopGroup.next().scheduleAtFixedRate(()->{
            log.debug("schedu");
        },0,1, TimeUnit.SECONDS);
        log.debug("{}","schedu main");
    }
```



> 客户端和服务端代码

```java
package com.viw.viwnetty.c2;

import io.netty.bootstrap.ServerBootstrap;
import io.netty.buffer.ByteBuf;
import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.ChannelInboundHandlerAdapter;
import io.netty.channel.ChannelInitializer;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.nio.NioServerSocketChannel;
import io.netty.channel.socket.nio.NioSocketChannel;
import lombok.extern.slf4j.Slf4j;

import java.nio.charset.Charset;

/**
 * @Author: xhb
 * @email xiaobo97@163.com
 * gitee: https://gitee.com/xiaobo97
 * @Date: 2021/7/6 1:20
 * @description:
 */
@Slf4j
public class EventLoopServerTest {

    public static void main(String[] args) {
            new ServerBootstrap()
                    .group(new NioEventLoopGroup())
                    .channel(NioServerSocketChannel.class)
                    .childHandler(new ChannelInitializer<NioSocketChannel>() {
                        @Override
                        protected void initChannel(NioSocketChannel ch) throws Exception {
                            ch.pipeline().addLast(new ChannelInboundHandlerAdapter(){
                                // follow read events
                                @Override
                                public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
                                    ByteBuf buf = (ByteBuf)msg;
                                    log.debug(buf.toString(Charset.defaultCharset()));// specify character set
                                }
                            });
                        }
                    })
                    .bind(8080);
    }
}


=================================================

package com.viw.viwnetty.c2;

import io.netty.bootstrap.Bootstrap;
import io.netty.channel.Channel;
import io.netty.channel.ChannelInitializer;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.nio.NioSocketChannel;
import io.netty.handler.codec.string.StringEncoder;
import lombok.extern.slf4j.Slf4j;

import java.net.InetSocketAddress;

/**
 * @Author: xhb
 * @email xiaobo97@163.com
 * gitee: https://gitee.com/xiaobo97
 * @Date: 2021/7/6 1:27
 * @description:
 */
@Slf4j
public class EventLoopClientTest {

    public static void main(String[] args) throws Exception {
        // 创建启动器
        Channel channel = new Bootstrap()
                // 添加eventLoop  nio  bio 的客户端去连都可以
                .group(new NioEventLoopGroup())
                // 选择 客户端channel
                .channel(NioSocketChannel.class)
                // Add a processor
                .handler(new ChannelInitializer<NioSocketChannel>() {
                    @Override //在连接建立后被调用
                    protected void initChannel(NioSocketChannel nsc) {
                        // 编码
                        nsc.pipeline().addLast(new StringEncoder());
                    }
                })
                // Connect client
                .connect(new InetSocketAddress("127.0.0.1", 8080))
                .sync()
                .channel();
        log.debug("{}",channel);
        channel.writeAndFlush("events===");
        System.out.println("");
    }
}
```



**发送不同的消息始终是同一个线程在处理这个客户端的channel**

![image-20210706013536953](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210706013536953.png)





# Channel



# Future & Promise



# Handler & Pipline



# ByteBuf



