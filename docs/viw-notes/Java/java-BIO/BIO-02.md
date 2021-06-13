[TOC]





# BIO



> 是一种同步阻塞io
> 两方 规定好怎么发怎么收，以及收发接受多少数据。



## 基础工作机制



> client 创建一个socket  client ， server创建一个socket server，注册端口，监听端口。建立通信。
>
> client socket  写数据，然后server socket 接收到数据  监听到数据  通过去 读取数据。



![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20210508014010.png)





## 单server 和 client 



> server socket



```java

/**
 * @Author: xhb
 * @email xiaobo97@163.com
 * gitee: https://gitee.com/xiaobo97
 * @Date: 2021/6/9 1:11
 * @description:
 */
public class MyServer01 {

    public static void main(String[] args) {
        try {
            // 实例化一个socket server
            ServerSocket serverSocket = new ServerSocket(8000);
            // 监听
            Socket accept = serverSocket.accept();
            // 获取client输入流
            InputStream inputStream = accept.getInputStream();
            // 包装成 一个使用默认大小的输入缓冲区的缓冲字符输入流。
            BufferedReader reader = new BufferedReader(new InputStreamReader(inputStream));
            String msg = null;
            if ((msg = reader.readLine()) != null) { // 按行接收，server会去阻塞client message
                System.out.println("message:" + msg);
            }

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
```



> client socket

```java

/**
 * @Author: xhb
 * @email xiaobo97@163.com
 * gitee: https://gitee.com/xiaobo97
 * @Date: 2021/6/9 1:11
 * @description:
 */
public class MyClient01 {
    public static void main(String[] args) {
            try {
                // 实例化一个socket
                Socket socket = new Socket("127.0.0.1",8000);
                // 
                OutputStream outputStream = socket.getOutputStream();
                // 打印输出流
                PrintStream printStream = new PrintStream(outputStream);

                printStream.println("hello");// 注意  server是按行读取的。否则server 认为client数据并没有发送完 一直阻塞。
                printStream.flush();


            }catch (Exception e){
                e.printStackTrace();
            }
    }
}

```



## 单client-多收发



![image-20210609014124897](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210609014124897.png)

![image-20210609014205660](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210609014205660.png)

> server

```java

/**
 * @Author: xhb
 * @email xiaobo97@163.com
 * gitee: https://gitee.com/xiaobo97
 * @Date: 2021/6/9 1:11
 * @description: server-单消息
 */
public class MyServer01 {

    public static void main(String[] args) {
        try {
            // 实例化一个socket server
            ServerSocket serverSocket = new ServerSocket(8000);
            // 监听
            Socket accept = serverSocket.accept();
            // 获取client输入流
            InputStream inputStream = accept.getInputStream();
            // 包装成 一个使用默认大小的输入缓冲区的缓冲字符输入流。
            BufferedReader reader = new BufferedReader(new InputStreamReader(inputStream));
            String msg = null;
            while ((msg = reader.readLine()) != null) { // 按行接收，server会去阻塞client message
                System.out.println("message:" + msg);
            }

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```



> client

```java

/**
 * @Author: xhb
 * @email xiaobo97@163.com
 * gitee: https://gitee.com/xiaobo97
 * @Date: 2021/6/9 1:11
 * @description: client-单消息
 */
public class MyClient01 {
    public static void main(String[] args) {
        try {
            // 实例化一个socket
            Socket socket = new Socket("127.0.0.1", 8000);
            //
            OutputStream outputStream = socket.getOutputStream();
            //
            PrintStream printStream = new PrintStream(outputStream);
            Scanner scanner = new Scanner(System.in);
            while (true) {
                System.out.print("print in:");
                String s = scanner.nextLine();
                printStream.println(s);
                printStream.flush();
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```





## 多客户端



> 传统bio 一个线程去处理一个客户端的socket连接。
>
> 会有一个server处理多个客户端的情况。就需要多个线程去处理，一个线程处理一个socket连接



![image-20210609015239900](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210609015239900.png)





### server

>

```java
/**
 * @Author: xhb
 * @email xiaobo97@163.com
 * gitee: https://gitee.com/xiaobo97
 * @Date: 2021/6/9 1:11
 * @description: server
 */
public class MyServer01 {

    public static void main(String[] args) {
        try {
            // 实例化一个socket server
            ServerSocket serverSocket = new ServerSocket(8000);
            while(true){
                // 监听
                Socket accept = serverSocket.accept();
                new ServerThread(accept).start();
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

// ======
/**
 * @Author: xhb
 * @email xiaobo97@163.com
 * gitee: https://gitee.com/xiaobo97
 * @Date: 2021/6/9 1:11
 * @description: client
 */
public class MyClient01 {
    public static void main(String[] args) {
        try {
            // 实例化一个socket
            Socket socket = new Socket("127.0.0.1", 8000);
            //
            OutputStream outputStream = socket.getOutputStream();
            //
            PrintStream printStream = new PrintStream(outputStream);
            Scanner scanner = new Scanner(System.in);
            while (true) {
                System.out.print("print in:");
                String s = scanner.nextLine();
                printStream.println(s);
                printStream.flush();
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}


```

 ![image-20210610010337906](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210610010337906.png)



### client



>

```java
/**
 * @Author: xhb
 * @email xiaobo97@163.com
 * gitee: https://gitee.com/xiaobo97
 * @Date: 2021/6/9 1:11
 * @description: client
 */
public class MyClient01 {
    public static void main(String[] args) {
        try {
            // 实例化一个socket
            Socket socket = new Socket("127.0.0.1", 8000);
            //
            OutputStream outputStream = socket.getOutputStream();
            //
            PrintStream printStream = new PrintStream(outputStream);
            Scanner scanner = new Scanner(System.in);
            while (true) {
                System.out.print("print in:");
                String s = scanner.nextLine();
                printStream.println(s);
                printStream.flush();
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

```



### 测试



>

![image-20210610011534511](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210610011534511.png)

![image-20210610011540643](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210610011540643.png)

![image-20210610011547414](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210610011547414.png)



### 总结



>

![image-20210610011730646](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210610011730646.png)





## 伪异步编程(线程池)



> 本质还是把socket请求给服务端线程池和阻塞队列来实现，



> server 端代码 

```java
/**
 * @Author: xhb
 * @email xiaobo97@163.com
 * gitee: https://gitee.com/xiaobo97
 * @Date: 2021/6/9 1:11
 * @description: server  伪异步
 */
public class MyServer01 {
    private static  ServerHandlerPool pool = new ServerHandlerPool(5, 10);

    public static void main(String[] args) {
        try {
            // 实例化一个socket server
            ServerSocket serverSocket = new ServerSocket(8000);
            while(true){
                // 监听
                Socket socket = serverSocket.accept();
                //包装成 runable 提交给线程池
                Runnable runnable = new ServerTaskRunable(socket);
                pool.submitTaskToPool(runnable);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}



/**
 * @Author: xhb
 * @email xiaobo97@163.com
 * gitee: https://gitee.com/xiaobo97
 * @Date: 2021/6/10 2:18
 * @description: 线程池
 */
public class ServerHandlerPool {

    private ExecutorService executorService;// 实例化一个线程池


    public ServerHandlerPool(int max, int queuesize) {
        executorService = new ThreadPoolExecutor(3, max, 120,
                TimeUnit.SECONDS, new ArrayBlockingQueue<>(queuesize));
    }

    /**
     * 把任务包装runable 提交到线程池 来处理
     *
     *
     * @param runnable
     */
    public void submitTaskToPool(Runnable runnable) {
        // executor没有返回值，和submit不一样，面试会问
        executorService.execute(runnable);
    }
}



/**
 * @Author: xhb
 * @email xiaobo97@163.com
 * gitee: https://gitee.com/xiaobo97
 * @Date: 2021/6/13 16:03
 * @description: socket包装成runable 让线程池的线程去处理
 */
public class ServerTaskRunable implements Runnable{
    Socket socket;
    public ServerTaskRunable(Socket socket){
        this.socket= socket;
    }
    @Override
    public void run() {
        // 这里是线程池分配 线程 去 处理socket msg
        try {
            // 实例化一个socket server
            ServerSocket serverSocket = new ServerSocket(8000);
            // 监听
            Socket accept = serverSocket.accept();
            // 获取client输入流
            InputStream inputStream = accept.getInputStream();
            // 包装成 一个使用默认大小的输入缓冲区的缓冲字符输入流。
            BufferedReader reader = new BufferedReader(new InputStreamReader(inputStream));
            String msg = null;
            while ((msg = reader.readLine()) != null) { // 按行接收，server会去阻塞client message
                System.out.println("message:" + msg);
            }

        } catch (Exception e) {
            e.printStackTrace();
        }

    }
}


```



> client 代码



```java
public class MyClient01 {
    public static void main(String[] args) {
        try {
            // 实例化一个socket
            Socket socket = new Socket("127.0.0.1", 8000);
            //
            OutputStream outputStream = socket.getOutputStream();
            //
            PrintStream printStream = new PrintStream(outputStream);
            Scanner scanner = new Scanner(System.in);
            while (true) {
                System.out.print("print in:");
                String s = scanner.nextLine();
                printStream.println(s);
                printStream.flush();
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}


```



> 小结
>
> 需要去处理客户端主动断开时抛异常的情况

![image-20210613161925242](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210613161925242.png)



## 文件上传



> server

```java

/**
 * @Author: xhb
 * @email xiaobo97@163.com
 * gitee: https://gitee.com/xiaobo97
 * @Date: 2021/6/13 16:33
 * @description: 接收任意文件类型 并存储
 */
public class Server {

    public static void main(String[] args) {
        try {
            ServerSocket socket = new ServerSocket(8080);

            while (true) {
                Socket accept = socket.accept();
                new ServerThread(accept).start();
            }

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}


class ServerThread extends Thread {
    private Socket socket;

    public ServerThread(Socket socket) {
        this.socket = socket;
    }

    @Override
    public void run() {
        try {
            //获取client 输入流
            DataInputStream inputStream = new DataInputStream(socket.getInputStream());
            // 获取文件类型
            String suffix = inputStream.readUTF();

            System.out.println("===========接收到文件=======");

            // 把获取的client的流 输出到本地上，实例化一个文件输出流
            OutputStream fileOutputStream = new FileOutputStream("D:\\xiaobo\\test\\" + UUID.randomUUID().toString() + suffix);
            // 读数据 写到文件中
            byte[] buffer = new byte[1024];//读的时侯是1kb
            int len;
            while ((len = inputStream.read(buffer)) > 0) {
                fileOutputStream.write(buffer, 0, len);
            }
            fileOutputStream.close();
            System.out.println("===========已成功写入文件============");
        } catch (Exception e) {
            e.printStackTrace();
        }


    }
}

```



> client



```java
public class Client {

    public static void main(String[] args) {
        try (
                //加载本地文件
               // InputStream inputStream = new FileInputStream("D:\\xiaobo\\1.html"); // html OK
                InputStream inputStream = new FileInputStream("E:\\xiaobo_o2o_img\\11.png");
        ) {
            // 请求和server 连接
            Socket socket = new Socket("127.0.0.1", 8080);
            // 字符流=》数据输出流
            DataOutputStream dos = new DataOutputStream(socket.getOutputStream());
            // 先发送 文件类型  DataOutputStream支持先发送文件类型
//            dos.writeUTF(".html");
            dos.writeUTF(".png");

            // 缓冲区大小
            byte[] buffer = new byte[1024];
            int len;
            while ((len = inputStream.read(buffer)) > 0) {
                dos.write(buffer, 0, len);
            }
            dos.flush();
            //告诉服务端文件发送完成，否则 server会一直等待，然后client主动关闭  srever就抛 connection reset
            socket.shutdownOutput();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```



> 测试
>
> 先启动server，再启动client

![image-20210613221836357](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210613221836357.png)



> 总结
>
> 文件格式需要定义(发txt 收就txt，xxOutputStrean 对应 xxInputStream)，server应该被主动通知 文件发送完毕，否则会一直等待阻塞





## 端口转发



> 群聊天思想，一个客户端的消息可以发送给多个客户端去接收
>
> 大致思路是需要有一个保存在线 socket的集合



```java

/**
 * @Author: xhb
 * @email xiaobo97@163.com
 * gitee: https://gitee.com/xiaobo97
 * @Date: 2021/6/13 22:24
 * @description: 1.注册端口，把client socket放入一个集合，有消息就推送到集合中
 */
public class Server {
    public static List<Socket> allSocketOnLineList = new ArrayList<>();
    public static void main(String[] args) {
        try {
            ServerSocket socket = new ServerSocket(8080);
            while (true) {
                Socket accept = socket.accept();
                allSocketOnLineList.add(accept);
                new ServerThread(accept).start();
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}


class ServerThread extends Thread {
    private Socket socket;
    public ServerThread(Socket socket) {
        this.socket = socket;
    }

    @Override
    public void run() {
        try {
            // 从当前socket获取输入流 包装成 字符输入流 ，包装成缓冲输入流
            BufferedReader reader = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            String msg;
            while ((msg = reader.readLine()) != null) {
                sendAllMsg(msg);
            }
        } catch (Exception e) {
            Server.allSocketOnLineList.remove(socket);
            System.out.println(socket.getLocalPort() + "下线了");
            e.printStackTrace();
        }
    }

    /**
     * 发送给所有socket集合中(包括了自己)
     *
     * @param msg
     * @throws Exception
     */
    private void sendAllMsg(String msg) throws Exception {
        for (Socket s : Server.allSocketOnLineList) {
            PrintStream printStream = new PrintStream(s.getOutputStream());
            printStream.println(msg);
            printStream.flush();
        }
    }

}

```





> 客户端和客户端的通信









