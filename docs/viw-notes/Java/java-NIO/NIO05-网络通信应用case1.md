# nio网络通信案例-非阻塞通信



## 案例流程



<img src="https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210614130441717.png" alt="image-20210614130441717" style="zoom: 67%;" />



> 服务端流程

```java
package com.viw.nioviw.one;

import lombok.extern.slf4j.Slf4j;

import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.SelectionKey;
import java.nio.channels.Selector;
import java.nio.channels.ServerSocketChannel;
import java.nio.channels.SocketChannel;

/**
 * @Author: xhb
 * @email xiaobo97@163.com
 * gitee: https://gitee.com/xiaobo97
 * @Date: 2021/6/20 12:57
 * @description: nio通信服务端
 */
@Slf4j
public class NioServer {
    public static void main(String[] args) throws Exception {
        ServerSocketChannel serverChannel = ServerSocketChannel.open();//获取socket通道
        serverChannel.configureBlocking(false);
        serverChannel.bind(new InetSocketAddress(8080));//绑定监听端口
        Selector selector = Selector.open();//打开一个选择器
        serverChannel.register(selector, SelectionKey.OP_ACCEPT);//监听什么事件(OP_ACCEPT连接事件)
        while (selector.select() > 0) {
            //获取已经注册到选择器中的通道  就绪事件迭代器
            var iterator = selector.selectedKeys().iterator();
            log.info("已经监听到事件处理{}"+iterator.toString());
            while (iterator.hasNext()) {
                //获取准备就绪的事件
                var sk = iterator.next();

                //如果是连接
                if (sk.isAcceptable()) {
                    SocketChannel socketChannel = serverChannel.accept();
                    //切换非阻塞事件
                    socketChannel.configureBlocking(false);
                    //把当前客户端通道 注册到选择器 为读事件
                    socketChannel.register(selector, SelectionKey.OP_READ);
                } else if (sk.isReadable()) { // 事件是否可读
                    var socketChannel = (SocketChannel) sk.channel();
                    var buffer = ByteBuffer.allocate(1024);
                    int len = 0;
                    while ((len = socketChannel.read(buffer)) > 0) {
                        buffer.flip();//读模式
                        System.out.println(new String(buffer.array(), 0, len));
                        buffer.clear();// 清除,位置归0 limit归容量.切换写模式
                    }
                }
                //处理完这个事件移除
                iterator.remove();
            }
        }

    }
}
```



> 客户端流程

```java
package com.viw.nioviw.one;

import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.SocketChannel;
import java.util.Scanner;

/**
 * @Author: xhb
 * @email xiaobo97@163.com
 * gitee: https://gitee.com/xiaobo97
 * @Date: 2021/6/20 13:19
 * @description: nio通信 客户端
 */
public class NioClient {

    public static void main(String[] args) throws Exception {
        // 获取通道
        SocketChannel socketChannel = SocketChannel.open(new InetSocketAddress("127.0.0.1", 8080));

        socketChannel.configureBlocking(false);

        ByteBuffer buffer = ByteBuffer.allocate(1024);

        var sc = new Scanner(System.in);

        while (true) {
            System.out.println("输入");
            //console 中输入的休息
            String s = sc.nextLine();
            // 转成byte写入buffer中
            buffer.put(s.getBytes());
            //切换为读模式
            buffer.flip();
            //读出来放入客户端socket 通道中
            socketChannel.write(buffer);
            // 切换写模式
            buffer.clear();
        }
    }
}

```



> 测试
>
> 开三个客户端，



设置多开![设置多开](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210620194548533.png)

```java
// 客户端1 2 3 分别输入 11 22 33

// 服务端输出
19:40:49.860 [main] INFO com.viw.nioviw.one.NioServer - 已经监听到事件处理{}java.util.HashMap$KeyIterator@727803de
19:40:56.295 [main] INFO com.viw.nioviw.one.NioServer - 已经监听到事件处理{}java.util.HashMap$KeyIterator@133e16fd
19:41:01.198 [main] INFO com.viw.nioviw.one.NioServer - 已经监听到事件处理{}java.util.HashMap$KeyIterator@1ad282e0
19:41:11.627 [main] INFO com.viw.nioviw.one.NioServer - 已经监听到事件处理{}java.util.HashMap$KeyIterator@1cab0bfb
11
19:41:18.371 [main] INFO com.viw.nioviw.one.NioServer - 已经监听到事件处理{}java.util.HashMap$KeyIterator@5e955596
22
19:41:20.552 [main] INFO com.viw.nioviw.one.NioServer - 已经监听到事件处理{}java.util.HashMap$KeyIterator@50de0926
33
```



# nio网络通信-群聊天测试案例



## 流程



**目标**

- 实现多人群聊天，客户端和客户端非阻塞通信
- 实现上下线，消息转发



> 服务端流程

```java
package com.viw.nioviw.niochat;

import lombok.extern.slf4j.Slf4j;
import lombok.val;

import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.SelectionKey;
import java.nio.channels.Selector;
import java.nio.channels.ServerSocketChannel;
import java.nio.channels.SocketChannel;

/**
 * @Author: xhb
 * @email xiaobo97@163.com
 * gitee: https://gitee.com/xiaobo97
 * @Date: 2021/6/20 19:51
 * @description: 群聊天-server
 */
@Slf4j
public class ChatServer {
    //   private static final Log log = LogFactory.getLog(ChatServer.class);

    private Selector selector;
    private ServerSocketChannel socketChannel;
    private static final int PORT = 8080;

    public ChatServer() {
        try {
            selector = Selector.open();
            socketChannel = ServerSocketChannel.open();
            socketChannel.bind(new InetSocketAddress(PORT));
            socketChannel.configureBlocking(false);
            socketChannel.register(selector, SelectionKey.OP_ACCEPT);
        } catch (Exception e) {
            log.info(e.getMessage());
            e.printStackTrace();
        }
    }
    public static void main(String[] args) {
        ChatServer server = new ChatServer();
        server.listen();
    }

    private void listen() {
        try {
            //至少有一个通道已经准备好了io事件
            while (selector.select() > 0) {
                //获取所有事件
                var keys = selector.selectedKeys().iterator();

                while (keys.hasNext()) {
                    val key = keys.next();
                    //连接事件
                    if (key.isAcceptable()) {
                        //
                        var channel = socketChannel.accept();
                        channel.configureBlocking(false);
                        log.info("获取到address为" + channel.getRemoteAddress() + "的连接");
                        channel.register(selector, SelectionKey.OP_READ);
                    }
                    // 读事件
                    else if (key.isReadable()) {
                        //处理读事件
                        toReadData(key);
                    }
                    keys.remove();
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            //
        }
    }

    private void toReadData(SelectionKey key) {
        SocketChannel channel = null;
        try {
            channel = (SocketChannel) key.channel();
            var buffer = ByteBuffer.allocate(1024);
            var count = channel.read(buffer);
            if (count > 0) {
                buffer.flip();//读
                var msg = new String(buffer.array(), 0, buffer.remaining());
                log.info("收到了地址为{}的消息为{}", channel.getRemoteAddress(), msg);
                //转发给其他客户端
                sendMsgToAllClient(msg, channel);
            }

        } catch (Exception e) {
            try {
                log.info("地址为{}下线了", channel.getRemoteAddress());
                //取消当前通道的注册
                key.cancel();
                // 关闭当前通道
                channel.close();
            } catch (IOException ioException) {
                ioException.printStackTrace();
            }
        }
    }

    //把当前客户端消息 推送给其他注册的客户端
    private void sendMsgToAllClient(String msg, SocketChannel channel) throws IOException {
        log.info("当前线程id为{}", Thread.currentThread().getId());
        for (SelectionKey key : selector.keys()) {
            var sChannel = key.channel();
            if (sChannel != channel && sChannel instanceof SocketChannel) {
                // 把字节数组包装到缓冲区
                var buffer = ByteBuffer.wrap(msg.getBytes());
                //写到其他客户端通道
                ((SocketChannel) sChannel).write(buffer);
            }
        }
    }

}
//print out 
```



> client 

```java

package com.viw.nioviw.niochat;

import lombok.extern.slf4j.Slf4j;

import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.SelectionKey;
import java.nio.channels.Selector;
import java.nio.channels.SocketChannel;
import java.util.Scanner;

/**
 * @Author: xhb
 * @email xiaobo97@163.com
 * gitee: https://gitee.com/xiaobo97
 * @Date: 2021/6/21 0:04
 * @description: nio-chat-client
 */
@Slf4j
public class ChatClient {
    private Selector selector;
    private static final int PORT = 8080;
    private SocketChannel socketChannel;

    public ChatClient() {
        try {
            selector = Selector.open();
            socketChannel = SocketChannel.open(new InetSocketAddress("127.0.0.1", PORT));
            socketChannel.configureBlocking(false);
            socketChannel.register(selector, SelectionKey.OP_READ);
            log.info("当前客户端已准备{}", socketChannel.getRemoteAddress());
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private void readData() throws Exception {
        while (selector.select() > 0) {
            var keys = selector.selectedKeys().iterator();
            while (keys.hasNext()) {
                var key = keys.next();
                if (key.isReadable()) {
                    var sc = (SocketChannel) key.channel();
                    var buffer = ByteBuffer.allocate(1024);
                    sc.read(buffer);
                    System.out.println(new String(buffer.array()));
                }
                keys.remove();
            }
        }
    }

    public static void main(String[] args) throws Exception {
        ChatClient client = new ChatClient();
        new Thread(() -> {
            try {
                client.readData();
            } catch (Exception e) {
                log.info(e.getMessage());
                e.printStackTrace();
            }
        }).start();
        var sc = new Scanner(System.in);
        while (sc.hasNextLine()) {
            var s = sc.nextLine();
            client.sendMsgToServer(s);
        }
    }
    private void sendMsgToServer(String s) throws Exception {
        log.info("地址为{}发送了消息{}", socketChannel.getRemoteAddress(), s);
        socketChannel.write(ByteBuffer.wrap(s.getBytes()));
    }
}


```



> 测试 开两个客户端

```java
// sever
00:33:51.159 [main] INFO com.viw.nioviw.niochat.ChatServer - 获取到address为/127.0.0.1:5629的连接
00:34:10.446 [main] INFO com.viw.nioviw.niochat.ChatServer - 获取到address为/127.0.0.1:10771的连接
00:34:20.215 [main] INFO com.viw.nioviw.niochat.ChatServer - 收到了地址为/127.0.0.1:5629的消息为客户端111
00:34:20.217 [main] INFO com.viw.nioviw.niochat.ChatServer - 当前线程id为1
00:35:07.333 [main] INFO com.viw.nioviw.niochat.ChatServer - 收到了地址为/127.0.0.1:10771的消息为客户端222
00:35:07.333 [main] INFO com.viw.nioviw.niochat.ChatServer - 当前线程id为1
00:35:40.872 [main] INFO com.viw.nioviw.niochat.ChatServer - 地址为/127.0.0.1:10771下线了
// client1

00:33:51.159 [main] INFO com.viw.nioviw.niochat.ChatClient - 当前客户端已准备/127.0.0.1:8080
客户端111
00:34:20.214 [main] INFO com.viw.nioviw.niochat.ChatClient - 地址为/127.0.0.1:8080发送了消息客户端111
客户端222
// client2
00:34:10.449 [main] INFO com.viw.nioviw.niochat.ChatClient - 当前客户端已准备/127.0.0.1:8080
客户端111
客户端222
00:35:07.332 [main] INFO com.viw.nioviw.niochat.ChatClient - 地址为/127.0.0.1:8080发送了消息客户端222
```





