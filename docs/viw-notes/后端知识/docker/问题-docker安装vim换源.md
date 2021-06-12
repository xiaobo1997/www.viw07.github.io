[TOC]





# docker内部无法使用vim，apt-get更新慢的解决



1. 容器进入 `/etc/apt 目录下`

![image-20210206112409684](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210206112409684.png)

2. 备份 `sources.list`

```shell
cp sources.list sources.list.bak
```

3. 更改apt-get

```
	  echo "deb http://mirrors.163.com/debian/ stretch main non-free contrib" >> /etc/apt/sources.list
    echo "deb http://mirrors.163.com/debian/ stretch-updates main non-free contrib" >> /etc/apt/sources.list
    echo "deb http://mirrors.163.com/debian/ stretch-backports main non-free contrib" >> /etc/apt/sources.list
    echo "deb-src http://mirrors.163.com/debian/ stretch main non-free contrib" >> /etc/apt/sources.list
    echo "deb-src http://mirrors.163.com/debian/ stretch-updates main non-free contrib" >> /etc/apt/sources.list
    echo "deb-src http://mirrors.163.com/debian/ stretch-backports main non-free contrib" >> /etc/apt/sources.list
    echo "deb http://mirrors.163.com/debian-security/ stretch/updates main non-free contrib" >> /etc/apt/sources.list
    echo "deb-src http://mirrors.163.com/debian-security/ stretch/updates main non-free contrib" >> /etc/apt/sources.list
```

4. 可能出现的问题

```shell
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Some packages could not be installed. This may mean that you have
requested an impossible situation or if you are using the unstable
distribution that some required packages have not yet been created
or been moved out of Incoming.
The following information may help to resolve the situation:

The following packages have unmet dependencies:
 vim : Depends: libtinfo5 (>= 6) but it is not going to be installed
E: Unable to correct problems, you have held broken packages.
```

解决：

```shell
apt update && apt install -y libtinfo5 --allow-remove-essential
```



5. 安装vim

```shell
apt-get -y install vim
```



6. y验证

```shell
vim
```

