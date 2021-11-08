## 



# 1.对文件，目录的操作



- 更改文件名 
- 查看npm路径. 可通过命令 npm config get prefix 查看 npm 安装路径（npm config set prefix 可设置 npm 安装路径）； 
- 创建文件夹 mkdir
- 进入文件 cd 
- 查看目录下的文件 ls  or ls -1
- 创建目录 mkdir
- 删除目录（及其内容） rm -rf [文件名]
- 删除文件rm 文件名
- 修改文件名 mv
- 返回上一级 cd ..
- rpm -q centos-release  查看centos版本信息
- `cat 编辑对应的文件名`  
- 压缩zip  unzip [文件名]
- `pwd`  显示当前文件(工作)路径
- 移动目录文件  mv [文件名1] [文件名2]
- 复制文件 cp file1 file2
- 复制一个目录到当前工作(所在)目录 cp -a /xxx/file1
- 复制一个目录   cp -a [目录1] [目录2]
- 赋权限 chmod [R] 模式 文件



## 1.1 vi编辑
- vim [文件名]
- 进入后按i 进行编辑-->按ESC-->输入`wq!`保存退出
- vi [文件名]



## 1.2 cat

- cat [参数] 文件名
- 1.一次显示整个文件。$ cat filename
- 2.从键盘创建一个文件。$ cat > filename   只能创建新文件,不能编辑已有文件.  在用cat > linuxyw.com时，用到>符号，这个符号是重定向的意思，会覆盖原来文件的内容，没有文件时会自动创建。如果用>>符号，即表进追加内容，不会覆盖原文件内容，只会在原文件内容下面追加你所输入的内容。
- 3.将几个文件合并为一个文件： $cat file1 file2 > file



## 1.3 日志

- `tail -200f xxxx`



## 1.4本机配置

- `uname -r` 
- `yum update`升级软件包..
- `yum -y install wget` 安装wget
- `lsb_release -a` 查看系统信息 如果命令提示未找到，  `yum install -y redhat-lsb`
- `cat /etc/redhat-release` 查看系统版本 或者 `cat /etc/issue`



## 1.5防火墙

`service firewalld status`查看防火墙状态
`systemctl stop firewalld` 关闭防火墙

`service stop iptables stop`  关闭防火墙

`systemctl start firewalld`开启
`systemctl disable firewalld`禁止开机启动
`lsof -i :port`，使用lsof -i :port就能看见所指定端口运行的程序，同时还有当前连接。



## 1.6 端口信息

查看目前所有端口 `netstat -ntlp`

查看80端口使用情况 `netstat -ntulp | grep 80`

查看有哪些服务和端口 `netstat -lanp`

根据pid 查询端口号  `netstat -antup|grep [pid]`

 具体端口信息  `lson -i :[端口]`  





## 1.7 查看进程内存命令
- `ps`查看当前终端下的命令

- `htop` 查看内存信息，

- `free -h`是一个快照 也可查看内存信息

- `top` 查看 各个进程的资源占用状况，可以选择按进程查看或者按用户查看

  - ```
     PID：进程的ID
      　　USER：进程所有者
      　　PR：进程的优先级别，越小越优先被执行
      　　NInice：值
      　　VIRT：进程占用的虚拟内存
      　　RES：进程占用的物理内存
      　　SHR：进程使用的共享内存
      　　S：进程的状态。S表示休眠，R表示正在运行，Z表示僵死状态，N表示该进程优先值为负数
      　　%CPU：进程占用CPU的使用率
      　　%MEM：进程使用的物理内存和总内存的百分比
      　　TIME+：该进程启动后占用的总的CPU时间，即占用CPU使用时间的累加值。
      　　COMMAND：进程启动命令名称
     ```
  ​


## 1.8 切换文件目录的所有者用户组和权限

```shell
chown 用户名:用户组 文件名

chown -R 用户名:用户组 文件名
```



## 1.9  查找文件

```
find /etc -name xxx  按键文件名在etc下面查找
find / -amin -10  查找在系统中最后10分钟访问的文件
```



## 2.0 ip网络相关

- mac查看ip， `ifconfig en0`
- ​mac查看端口, `lsof -i [port]` 
- 查看端口  `ps -ef | grep [target_port]`

## 2.1 对文件内容
- 对某一个文件查询指定内容 `grep [指定的内容] [文件名]` 如 `grep hello a.log`
- 


# 2.Linux中docker的使用命令
- 查看正运行的容器 `docker ps`
- 查看所有容器  `docker ps -a`
- 启动容器 `docke start [CONTAINER]`
- 停止容器 `docker stop [CONTAINER]`
- 进入容器 `docker exec -it 容器ID bash` `docker exec -it [CONTAINER] /bin/sh`
- 复制 如：`docker cp [文件名或文件所在路径] typecho-blog:/data/themes`
- 查看本机上的镜像 `docker images`
- 查看docker容器网关 `ip addr`
- docker run -d -p [端口][id] --name [自定义名字]



# 3.linux 文件权限

> 通过 `chmox` 可以设置文件权限 ； 两种方式
>
> - 1.通过数字
> - 2.通过字母



> 在linux中  `rwx` 分别表示的数字是 4 2 1 
>
> 相当于
>
> ```shell
> r --> 4
> w --> 2
> x --> 1
> ```
>
> ```shell
> ## 比如 777 就是  rwxrwxrwx  也就是说 所属者rwx的权限  所属组rwx的权限  其他用户rwx的权限
> chmod 777 [文件名]
> ```



> 通过字母来 修改
>
> 使用 u --所属者  g --组  o--其他人  a--所以
>
> 使用 `+` 增加  `-` 删除  `=` 设置
>
> 如
>
> ```shell
> ##  rwx-w-r--
> chmod g=rwx,g=w,o=r [文件名]
> ```
>
> ```java
> ## 所有人 增加 读权限
> chmod a+r [文件名] 
> ```
>
> 

# 4. linux 历史命令

> 查看历史输入的命令

```shell
history
```

> 清除历史命令

```shell
history -c
```

