[TOC]



# 1.http



## 概念



> 是什么

HTTP，英文全称为HyperText Transfer Protocol，即超文本传输协议，是互联网上应用最为广泛的一种网络协议。HTTP是一种属于**应用层**的面向对象的协议，



> 通信模式

http采用的是客户端/服务器通信模式，服务端也就是我们说的http服务器，客户端最常见的就是web浏览器



> http在的网络体系结构

http位于应用层，建立在tcp/ip基础上，http使用可靠的tcp连接，默认端口80

![image-20201129230105041](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201129230105041.png)



## 一次执行流程



![image-20201129230311368](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201129230311368.png)

![image-20201129230345544](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201129230345544.png)

- 1.客户端和服务器之间建立tcp套接字连接
- 2.建立连接后，客户端发送一个请求给服务器，请求发送的格式为：请求头，请求正文，请求方法，空行
  - ![image-20201129231533521](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201129231533521.png)
- 3.服务器接受到请求后，解析请求，定位请求资源，一个响应由状态行、响应头部、空行和响应数据4部分组成。服务器将资源写入TCP套接字，让客户端读取
- 4.客户端接收到的信息显示在浏览器中，然后关闭连接，释放tcp连接，
  - ![image-20201129232123881](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201129232123881.png)
  - 但是如果connection是keep-alive连接会保持一段时间，如果是close，服务器主动关闭连接
- 5.浏览器解析并读取状态 每一个响应头，一些html 等，并且把他们格式化，然后在浏览器显示



## 特点

> http协议有五个特点

- 1.支持客户端服务器模式
- 2.无状态
  - HTTP协议是无状态协议，无状态是指协议对于事物处理没有记忆能力，缺少状态意味着如果后续处理需要前面的信息，则它必须重传，这样可能导致每次连接传输的数据量增大。另一方面，在服务器不需要先前信息时它的应答就较快
- 3.无连接
  - **无连接的含义**是限制每次连接只处理一个请求。服务器处理完客户的请求，并收到客户的应答后，即断开连接。采用这种方式可以节省传输时间。
  - 当Web服务器和应用服务器在同一台机器上运行时，Keep-Alive 功能对资源利用的影响尤其突出。 
- 4.灵活
  - HTTP允许传输任意类型的数据对象，正在传输的类型由Content-Type加以标记
- 5.简单快速
  - 客户端向服务器发送请求服务时，只需要传送请求方法和路径。由于HTTP协议简单，使得HTTP服务器的程序规模小，因而通信速度快



## http request包含的内容



![image-20201201012111281](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201201012111281.png)

## http请求头

![image-20201129232123881](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201129232123881.png)

![image-20201201012022065](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201201012022065.png)



| **请  求  头**  | **说    明**                                                 |
| --------------- | ------------------------------------------------------------ |
| Accept          | 指定客户端能够接收的内容类型，如Accept:text/plain,text/html  |
| Accept-Charset  | 指定客户端可以接受的字符集编码，如Accept-Charset:UTF-8       |
| Accept-Encoding | 指定浏览器可以支持的Web服务器返回内容压缩编码类型，如Accept-Encoding:gzip,deflate |
| Accept-Language | 指定浏览器可以接受的语言，如Accept-Language:zh-cn,zn,en      |
| Authorization   | HTTP授权的授权证书，如Authorization:Basic...                 |
| Cache-Control   | 指定请求和响应遵循的缓存机制，如Cache-Control:no-cache       |
| Connection      | 表示是否需要持久连接（HTTP1.1默认进行持久链接），如Connection:close |
| Cookie          | HTTP请求发送时会把保存在该请求域名下的所有cookie值一起发送给Web服务器，如Cookie:$Version=1;Skin=new; |
| Content-Length  | 表示请求的请求体的内容长度，这个长度是经过了压缩后的长度，如Content-Length:348 |
| Content-Type    | 表示请求的请求体对应的MIME信息，如Content-Type:application/x-www/form-urlencoded |
| Date            | 表示请求发送的日期和时间，如Date:Oct, 24 Nov 2015 20:12:30 GMT |
| Host            | 表示请求的服务器的域名和端口号，如Host:home.cnblogs.com      |
| Pragma          | 表示用来包含实现特定的指令，如Pragma:no-cache                |
| Referer         | 表示从哪个链接链到当前地址，如Referer:http://www.cnblogs.com/ |
| User-Agent      | 表示发出请求的用户信息（如用户操作系统、浏览器和其他属性），如User-Agent: Mozilla/5.0 (Windows NT 6.3; WOW64) |



## http协议头

![image-20201129232930401](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201129232930401.png)

![image-20201201012000659](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201201012000659.png)

| **响  应  头**    | **说    明**                                                 |
| ----------------- | ------------------------------------------------------------ |
| Allow             | 表示对某网络资源的有效请求行为，不允许则返回405，如Allow:GET,HEAD |
| Cache-Control     | 表示告诉所有的缓存机制是否可以缓存及哪种缓存类型，如Cache-Control:no-cache |
| Content-Encoding  | 表示Web服务器只是的返回内容压缩编码类型，如Content-Encoding:gzip |
| Content-Language  | 表示响应体的语言，如Content-Language:en,zh                   |
| Content-Length    | 表示响应体的内容长度，这个长度是经过了压缩后的长度，如Content-Language:348 |
| Content-Type      | 表示返回内容的MIME类型，如Content-Type:text/html;charset=utf-8 |
| Date              | 表示原始服务器消息发出的时间，如Date:Oct, 24 Nov 2015 20:12:30 GMT |
| Expires           | 表示响应过期的日期和时间，如Expires:Sat, 24 Oct 2015 20:45:30 GMT |
| Location          | 用来重定向接收方到非请求URL的位置来完成请求或标识新的资源，如Location:http://www.sina.com.cn/ |
| Last-Modified     | 表示资源的最后修改时间，如Last-Modified: 表示响应过期的日期和时间，如Expires:Sat, 24 Oct 2015 20:45:30 GMT |
| Retry-After       | 表示如果响应体如果暂时不可取得，客户端在指定时间之后再尝试，如Retry-After:120 |
| Server            | 表示Web服务器软件名称，如Server:Apache/1.3.6(Unix)           |
| Set-Cookie        | 表示设置Http Cookie，如Set-Cookie:UserID=RickyXu;Max-Age=3600;Version=1 |
| Transfer-Encoding | 表示文件传输编码，如Transfer-Encoding-chunked                |
| www.Authenticate  | 表示客户端请求实体应该使用的授权方案，如WWW-Authenticate:Basic |



## http 请求方法

![HTTP请求头--那些你需要记住的基础知识](http://p3.pstatp.com/large/pgc-image/1bcb5ba7d6c346ada1485798446c41f4)



## http状态码



![img](https://upload-images.jianshu.io/upload_images/12603307-9e9f12a5f5055b34.png?imageMogr2/auto-orient/strip|imageView2/2/w/587/format/webp)

| **状  态  码** | **语    法**                                                 |
| -------------- | ------------------------------------------------------------ |
| 200            | 表示客户端请求成功，通常这表示服务器提供了请求的网页         |
| 201            | 表示客户端请求成功并且服务器创建了新的资源                   |
| 202            | 表示服务器已经接受请求但尚未处理                             |
| 204            | 表示服务器成功处理了请求但没有任何返回内容                   |
| 302            | 表示临时跳转，跳转的地址通过Location指定                     |
| 400            | 表示客户端请求有语法错误，不能被服务器识别                   |
| 403            | 表示服务器收到请求，但是拒绝提供服务                         |
| 404            | 表示服务器找不到请求的资源                                   |
| 500            | 表示服务器发生了不可预期的错误                               |
| 503            | 表示服务器目前无法使用（由于超载或停机维护），通常这只是暂时状态 |
| 505            | 表示服务器不支持请求中所使用的HTTP协议版本                   |

## http缓存

> 缓存的话 使用比较多的是 在http 报文 头部 (header)-附加信息（cookie，缓存信息等）与缓存相关的规则信息，均包含在header中

如果使用了 http-header 来控制缓存

那么第一次浏览器请求流程

![image-20201129234418400](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201129234418400.png)

浏览器再一次请求：

![image-20201129234438139](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201129234438139.png)

**图中几个概念：**

- 1.强制缓存和对比缓存的区别：
  - 根据是否需要重新向服务器发起请求来分类这两种缓存
  - 强制缓存如果生效，不需要再和服务器发生交互，而对比缓存不管是否生效，都需要与服务端发生交互。
  - 对于强制缓存来说，响应header中会有两个字段来标明失效规则（Expires/Cache-Control）
  - ![image-20201129234933355](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201129234933355.png)

- 2.**Expires**：
  - Expires的值为服务端返回的到期时间，即下一次请求时，请求时间小于服务端返回的到期时间，直接使用缓存数据。http1.1基本不用
- 3.**Cache-Control**
  - Cache-Control 是最重要的规则。常见的取值有private、public、no-cache、max-age，no-store，默认为private。
    private:        客户端可以缓存
    public:         客户端和代理服务器都可缓存（前端的同学，可以认为public和private是一样的）
    max-age=xxx:  缓存的内容将在 xxx 秒后失效
    no-cache:      需要使用对比缓存来验证缓存数据（后面介绍）
    no-store:       所有内容都不会缓存，强制缓存，对比缓存都不会触发



**使用ctrl+F5告诉服务器我们需要最新的数据**

![image-20201129235204948](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201129235204948.png)