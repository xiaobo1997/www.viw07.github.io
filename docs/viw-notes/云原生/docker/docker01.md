
<!-- TOC -->

- [docker](#docker)
    - [1. docker是什么](#1-docker是什么)
    - [2. docker为什么出现](#2-docker为什么出现)
    - [3. docker的架构图](#3-docker的架构图)
    - [4. 和传统虚拟机的区别](#4-和传统虚拟机的区别)
        - [虚拟机：](#虚拟机)
        - [docker](#docker)
        - [区别](#区别)
        - [为什么docker比虚拟机快](#为什么docker比虚拟机快)
    - [5. 为什么要用docker](#5-为什么要用docker)
    - [6. devops开发工程师](#6-devops开发工程师)
- [docker基本概念](#docker基本概念)
    - [三个基本概念](#三个基本概念)
        - [镜像](#镜像)
        - [容器](#容器)
        - [仓库](#仓库)
    - [架构](#架构)
- [docker安装](#docker安装)
    - [官网下载](#官网下载)
    - [运行hello-world](#运行hello-world)
- [docker常用命令](#docker常用命令)
    - [启动类命令](#启动类命令)
    - [镜像地址](#镜像地址)
    - [容器命令](#容器命令)
    - [总结](#总结)
- [镜像](#镜像)
- [本地镜像推送到私有库](#本地镜像推送到私有库)
- [容器卷](#容器卷)
- [案例](#案例)
    - [搭个mysql玩玩](#搭个mysql玩玩)
    - [设置serverid，同一局域网中需要唯一](#设置serverid同一局域网中需要唯一)
    - [指定不需要同步的数据库名称](#指定不需要同步的数据库名称)
    - [开启二进制日志功能](#开启二进制日志功能)
    - [设置二进制日志使用内存大小（事务）](#设置二进制日志使用内存大小事务)
    - [设置使用的二进制日志格式（mixed,statement,row）](#设置使用的二进制日志格式mixedstatementrow)
    - [二进制日志过期清理时间。默认值为0，表示不自动清理。](#二进制日志过期清理时间默认值为0表示不自动清理)
    - [跳过主从复制中遇到的所有错误或指定类型的错误，避免slave端复制中断。](#跳过主从复制中遇到的所有错误或指定类型的错误避免slave端复制中断)
    - [如：1062错误是指一些主键重复，1032错误是因为主从数据库数据不一致](#如1062错误是指一些主键重复1032错误是因为主从数据库数据不一致)
    - [设置serverid，同一局域网中需要唯一](#设置serverid同一局域网中需要唯一)
    - [指定不需要同步的数据库名称](#指定不需要同步的数据库名称)
    - [开启二进制日志功能，以备Slave作为其它数据库实例的Master时使用](#开启二进制日志功能以备slave作为其它数据库实例的master时使用)
    - [设置二进制日志使用内存大小（事务）](#设置二进制日志使用内存大小事务)
    - [设置使用的二进制日志格式（mixed,statement,row）](#设置使用的二进制日志格式mixedstatementrow)
    - [二进制日志过期清理时间。默认值为0，表示不自动清理。](#二进制日志过期清理时间默认值为0表示不自动清理)
    - [跳过主从复制中遇到的所有错误或指定类型的错误，避免slave端复制中断。](#跳过主从复制中遇到的所有错误或指定类型的错误避免slave端复制中断)
    - [如：1062错误是指一些主键重复，1032错误是因为主从数据库数据不一致](#如1062错误是指一些主键重复1032错误是因为主从数据库数据不一致)
    - [relaylog配置中继日志](#relaylog配置中继日志)
    - [logslaveupdates表示slave将复制事件写进自己的二进制日志](#logslaveupdates表示slave将复制事件写进自己的二进制日志)
    - [slave设置为只读（具有super权限的用户除外）](#slave设置为只读具有super权限的用户除外)

<!-- /TOC -->


# docker

## 1. docker是什么



Docker 是一个用于开发、发布和运行应用程序的开放平台。 Docker 使您能够将应用程序与基础架构分离，以便您可以快速交付软件。使用 Docker，您可以像管理应用程序一样管理基础设施。通过利用 Docker 快速交付、测试和部署代码的方法，您可以显着减少编写代码和在生产中运行之间的延迟。

最开始 docker是google基于Linux用go写的，可以对进程进行封装隔离。隔离的进程独立于宿主和其它的隔离的进程，也称其为容器。





## 2. docker为什么出现


RD可以利用 Docker 可以消除协作编码时“在我的机器上可正常工作”的问题，实现 一次镜像，处处运行

透过镜像(images)将作业系统核心除外，运作应用程式所需要的系统环境，由下而上打包，达到应用程式跨平台间的无缝接轨运作。

RD只需要关系写代码，告诉OP系统运行环境，让OP去维护和部署即可

现在我们公司都是k8s+docker，开发只需要在把对应的分支部署到对应的泳道既可，这个泳道也要有着系统的上下游系统


## 3.docker的架构图



![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20220826005118.png)



## 4.和传统虚拟机的区别

### 虚拟机：
它可以在一种操作系统里面运行另一种操作系统，比如在Windows10系统里面运行Linux系统CentOS7。应用程序对此毫无感知，因为虚拟机看上去跟真实系统一模一样，而对于底层系统来说，虚拟机就是一个普通文件，不需要了就删掉，对其他部分毫无影响。这类虚拟机完美的运行了另一套系统，能够使应用程序，操作系统和硬件三者之间的逻辑不变。 

**虚拟机的缺点**
- 1    资源占用多 
- 2    冗余步骤多
- 3    启动慢

![](docker01/imgs/2022-08-26-00-54-56.png)


> 说简单点就是，有了虚拟机，你就可以在mac上跑Windows系统，这个系统对于mac来说 就是个应用

### docker

![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20220826010014.png)


### 区别

传统虚拟机技术是虚拟出一套硬件后，在其上运行一个完整操作系统，在该系统上再运行所需应用进程；而容器内的应用进程直接运行于宿主的内核，容器内没有自己的内核，而且也没有进行硬件虚拟。因此容器要比传统虚拟机更为轻便。

![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20220826005950.png)

### 为什么docker比虚拟机快

docker利用的是宿主机的内核,而不需要加载操作系统OS内核

当新建一个容器时,docker不需要和虚拟机一样重新加载一个操作系统内核。进而避免引寻、加载操作系统内核返回等比较费时费资源的过程,当新建一个虚拟机时,虚拟机软件需要加载OS,返回新建过程是分钟级别的。而docker由于直接利用宿主机的操作系统,则省略了返回过程,因此新建一个docker容器只需要几秒钟。

![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20220826012808.png)

## 5.为什么要用docker


- 1.更高效的利用系统资源
- 2.更快速的启动时间
- 3.一致的运行环境,由于开发环境、测试环境、生产环境不一致，导致有些 bug 并未在开发过程中被发现。而 Docker 的镜像提供了除内核外完整的运行时环境，确保了应用运行环境一致性，从而不会再出现 「这段代码在我机器上没问题啊」 这类问题。
- 4.持续交付和自动部署
- 5.更轻松的迁移
- 6.更轻松的维护和扩展

## 6. devops开发工程师


...




# docker基本概念


## 三个基本概念

![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20220826010905.png)

> 说简单点 就是 仓库就是这一只鲸鱼，镜像就是模板，模板创建出来的上面一个个的集装箱就是容器实例，有了模板，可以在A的电脑上创建redis实例，在B的电脑上创建一模一样的实例，容器就是来干活的

### 镜像

Docker 镜像 是一个特殊的文件系统，除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数（如匿名卷、环境变量、用户等）。镜像 不包含 任何动态数据，其内容在构建之后也不会被改变。

> 说简单点就是 Docker 镜像（Image）就是一个只读的模板。镜像可以用来创建 Docker 容器，一个镜像可以创建很多容器。它也相当于是一个root文件系统。比如官方镜像 ubuntu:18.04 就包含了完整的一套 Ubuntu 18.04 最小系统的 root 文件系统。


### 容器

镜像（Image）和容器（Container）的关系，就像是面向对象程序设计中的 类 和 实例 一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等。

> 可以把容器看做是一个简易版的 Linux 环境（包括root用户权限、进程空间、用户空间和网络空间等）和运行在其中的应用程序。容器是用镜像创建出来的运行实例

### 仓库

镜像构建完成后，可以很容易的在当前宿主机上运行，但是，如果需要在其它服务器上使用这个镜像，我们就需要一个集中的存储、分发镜像的服务，Docker Registry 就是这样的服务。

一个仓库会包含同一个软件不同版本的镜像，而标签就常用于对应该软件的各个版本。我们可以通过 <仓库名>:<标签> 的格式来指定具体是这个软件哪个版本的镜像。如果不给出标签，将以 latest 作为默认标签。

仓库名经常以 两段式路径 形式出现，比如 aaa/redis，前者往往意味着 Docker Registry 多用户环境下的用户名，后者则往往是对应的软件名。但这并非绝对，取决于所使用的具体 Docker Registry 的软件或服务。


仓库分为公开仓库（Public）和私有仓库（Private）两种形式。

最大的公开仓库是 Docker Hub(https://hub.docker.com/)，

存放了数量庞大的镜像供用户下载。国内的公开仓库包括阿里云 、网易云等

> 简单点说仓库就是存放镜像的地方

## 架构

![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20220826012116.png)

Docker 采用了 C/S 架构，包括客户端和服务端。Docker 守护进程 （Daemon）作为服务端接受来自客户端的请求，并处理这些请求（创建、运行、分发容器）。


Docker 守护进程一般在宿主主机后台运行，等待接收来自客户端的消息。
Docker 客户端则为用户提供一系列可执行命令，用户用这些命令实现跟 Docker 守护进程交互。

![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20220826012249.png)


# docker安装


## 官网下载

> mac下直接安装 docker mac desktop


然后开启国内加速


![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20220826012524.png)




## 运行hello-world


```sh
docker run hello-world
```

![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20220829224716.png)



# docker常用命令

## 启动类命令


- 启动docker `systemctl start docker`
- 停止docker `systemctl stop docker`
- 重启 `systemctl restart docekr`
- 查看docker状态 `systemctl status docker`
- 开机启动 `systemctl enable docker`
- 查看docker概要信息 `docker info`
- 查看docker帮助文档 `docker --help`


## 镜像地址

- `docker images` 查看镜像

![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20220829225658.png)




REPOSITORY：表示镜像的仓库源
TAG：镜像的标签版本号
IMAGE ID：镜像ID
CREATED：镜像创建时间
SIZE：镜像大小

>  同一仓库源可以有多个 TAG版本，代表这个仓库源的不同个版本，我们使用 REPOSITORY:TAG 来定义不同的镜像。如果你不指定一个镜像的版本标签 将默认使用 ubuntu:latest 镜像

- `docekr search [image_name]`搜索镜像
- `docker pull [name]` 拉取镜像

- `docker system df`查看image信息等
```
 docker system df
TYPE            TOTAL     ACTIVE    SIZE      RECLAIMABLE
Images          4         2         1.426GB   1.318GB (92%)
Containers      2         0         5B        5B (100%)
Local Volumes   1         1         92B       0B (0%)
Build Cache     0         0         0B        0B
```


- `docker rmi [name or 镜像id]` 删除镜像


## 容器命令


- `docker run [options] image [command][参数]` 
    - --name="xxx"为容器指定一个名称；-d: 后台运行容器并返回容器ID，也即启动守护式容器(后台运行)；
    - -i：以交互模式运行容器，通常与 -t 同时使用；
    - -t：为容器重新分配一个伪输入终端，通常与 -i 同时使用；也即启动交互式容器(前台有伪终端，等待交互)；
    - -P: 随机端口映射，大写P
    - -p: 指定端口映射，小写p
    ![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20220829230714.png)

- `docekr ps -a ` 查看所有容器

- run进入容器 `exit`  退出，容器停止；也可以不停止退出

- `docker start [容器id 容器名]` 启动停止的容器

- `docekr restart [容器id 容器名]` 重新启动容器


- `docker stop [容器id 容器名]` 停止容器


- `docker kill [容器id 容器名]`

- `docker rm [xx]`删除已经停止的容器

- `docker logs [容器id]` 查看容器log

- `docker top [容器id]` 查看容器进程


- `docker inspect [容器id]` 查看容器内部细节

- `docker exec -it [容器id] /bin/bash` 进入容器  退出 容器不停止

- `docker cp 容器id:容器路径 目的主机路径`

- 导入 导出

## 总结

![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20220829231739.png)



# 镜像




镜像一种轻量级、可执行的独立软件包，它包含运行某个软件所需的所有内容，我们把应用程序和配置依赖打包好形成一个可交付的运行环境(包括代码、运行时需要的库、环境变量和配置文件等)，这个打包好的运行环境就是image镜像文件。
只有通过这个镜像模板文件才能生成Docker容器实例(类似Java中new出来一个对象)。


![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20220830000123.png)

Docker中的镜像分层，支持通过扩展现有镜像，创建新的镜像。类似Java继承于一个Base基础类，自己再按需扩展。

新镜像是从 base 镜像一层一层叠加生成的。每安装一个软件，就在现有镜像的基础上增加一层

> 可以通过下载一个镜像看到 docker的镜像是在一层一层的在下载



**联合文件系统**

Union文件系统（UnionFS）是一种分层、轻量级并且高性能的文件系统，它支持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下(unite several directories into a single virtual filesystem)。Union 文
件系统是 Docker 镜像的基础。镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像。
docker的镜像实际上由一层一层的文件系统组成，这种层级的文件系统UnionFS。
bootfs(boot file system)主要包含bootloader和kernel, bootloader主要是引导加载kernel, Linux刚启动时会加载bootfs文件系统，在Docker镜像的最底层是引导文件系统bootfs。这一层与我们典型的Linux/Unix系统是一样的，包含boot加载器和内核。当boot加载完成之后整个内核就都在内存中了，此时内存的使用权已由bootfs转交给内核，此时系统也会卸载bootfs。

Docker 中使用的 AUFS（Advanced Multi-Layered Unification Filesystem）就是一种联合文件系统。 AUFS 支持为每一个成员目录（类似 Git 的分支）设定只读（readonly）、读写（readwrite）和写出（whiteout-able）权限, 同时 AUFS 里有一个类似分层的概念, 对只读权限的分支可以逻辑上进行增量地修改(不影响只读部分的)。


>不同 Docker 容器就可以共享一些基础的文件系统层，同时再加上自己独有的改动层，大大提高了存储的效率。


> 镜像层是只读的，容器是可写的 当容器启动时，一个新的可写层被加载到镜像的顶部。这一层通常被称作“容器层”，“容器层”之下的都叫“镜像层”。所有对容器的改动 - 无论添加、删除、还是修改文件都只会发生在容器层中。只有容器层是可写的，容器层下面的所有镜像层都是只读的。
![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20220829235308.png)


# 本地镜像推送到私有库

- google


# 容器卷


如果是CentOS7安全模块会比之前系统版本加强，不安全的会先禁止，所以目录挂载的情况被默认为不安全的行为，
在SELinux里面挂载目录被禁止掉了额，如果要开启，我们一般使用--privileged=true命令，扩大容器的权限解决挂载目录没有权限的问题，也即
使用该参数，container内的root拥有真正的root权限，否则，container内的root只是外部的一个普通用户权限。


- 运行一个带有容器卷存储功能的容器实例`docker run -it --privileged=true -v /宿主机绝对路径目录:/容器内目录 镜像名`  -v 后面就是路径
    - 1  docker修改，主机同步获得 2 主机修改，docker同步获得3 docker容器stop，主机修改，docker容器重启看数据是否同步。
- 查看是否成功 `docker inspect 容器ID`

> cannot open directory .: Permission denied 在挂载目录后多加一个--privileged=true参数即可


**特点**

1：数据卷可在容器之间共享或重用数据
2：卷中的更改可以直接实时生效，爽
3：数据卷中的更改不会包含在镜像的更新中
4：数据卷的生命周期一直持续到没有容器使用它为止



# 案例


## 搭个mysql玩玩


```
docker run -p 3307:3306 --name mysql-master \

-v /mydata/mysql-master/log:/var/log/mysql \

-v /mydata/mysql-master/data:/var/lib/mysql \

-v /mydata/mysql-master/conf:/etc/mysql \

-e MYSQL_ROOT_PASSWORD=root  \

-d mysql:5.7

```

相关conf下面的 my.conf

```
[mysqld]

## 设置server_id，同一局域网中需要唯一

server_id=101 

## 指定不需要同步的数据库名称

binlog-ignore-db=mysql  

## 开启二进制日志功能

log-bin=mall-mysql-bin  

## 设置二进制日志使用内存大小（事务）

binlog_cache_size=1M  

## 设置使用的二进制日志格式（mixed,statement,row）

binlog_format=mixed  

## 二进制日志过期清理时间。默认值为0，表示不自动清理。

expire_logs_days=7  

## 跳过主从复制中遇到的所有错误或指定类型的错误，避免slave端复制中断。

## 如：1062错误是指一些主键重复，1032错误是因为主从数据库数据不一致

slave_skip_errors=1062
```


然后重启


进入master中，命令行连接mysql


![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20220830003323.png)





新建从

```
docker run -p 3308:3306 --name mysql-slave \

-v /mydata/mysql-slave/log:/var/log/mysql \

-v /mydata/mysql-slave/data:/var/lib/mysql \

-v /mydata/mysql-slave/conf:/etc/mysql \

-e MYSQL_ROOT_PASSWORD=root  \

-d mysql:5.7

```

相关conf下的my.conf

```
[mysqld]

## 设置server_id，同一局域网中需要唯一

server_id=102

## 指定不需要同步的数据库名称

binlog-ignore-db=mysql  

## 开启二进制日志功能，以备Slave作为其它数据库实例的Master时使用

log-bin=mall-mysql-slave1-bin  

## 设置二进制日志使用内存大小（事务）

binlog_cache_size=1M  

## 设置使用的二进制日志格式（mixed,statement,row）

binlog_format=mixed  

## 二进制日志过期清理时间。默认值为0，表示不自动清理。

expire_logs_days=7  

## 跳过主从复制中遇到的所有错误或指定类型的错误，避免slave端复制中断。

## 如：1062错误是指一些主键重复，1032错误是因为主从数据库数据不一致

slave_skip_errors=1062  

## relay_log配置中继日志

relay_log=mall-mysql-relay-bin  

## log_slave_updates表示slave将复制事件写进自己的二进制日志

log_slave_updates=1  

## slave设置为只读（具有super权限的用户除外）

read_only=1
```


重启从mysql


在主中查看从状态 `show master status`


进入从的容器中


```
change master to master_host='宿主机ip', master_user='slave', master_password='123456', master_port=3307, master_log_file='mall-mysql-bin.000001', master_log_pos=617, master_connect_retry=30;

```

master_host：主数据库的IP地址；

master_port：主数据库的运行端口；

master_user：在主数据库创建的用于同步数据的用户账号；

master_password：在主数据库创建的用于同步数据的用户密码；

master_log_file：指定从数据库要复制数据的日志文件，通过查看主数据的状态，获取File参数；

master_log_pos：指定从数据库从哪个位置开始复制数据，通过查看主数据的状态，获取Position参数；

master_connect_retry：连接失败重试的时间间隔，单位为秒。

在从mysql中查看 从状态 `show slave status`

在从mysql开启 主从同步  `start slave`



