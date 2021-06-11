[TOC]





# BIO



> 是一种同步阻塞io



## 基础工作机制



> client 创建一个socket  client ， server创建一个socket server，注册端口，监听端口。建立通信。
>
> client socket  写数据，然后server socket 接收到数据  监听到数据  通过去 读取数据。



![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20210508014010.png)





## 单收发消息-server socket和 client socket



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



## 单客户端-多收发消息



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
> 会有一个server处理多个客户端的情况。就需要多个线程去处理，



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





## 伪异步编程



> 线程池和任务队列实现





