



# 安装清单



## brew&

https://cloud.tencent.com/developer/article/1614039

https://blog.csdn.net/yuanshangshenghuo/article/details/106599836

https://www.jianshu.com/p/22122a1d4474

## on-my-zsh&itern2

https://www.jianshu.com/p/ba08713c2b19





## idea



## maven

## typora

## sublime

## postman



## vscode



## xmind

## git





## mac-v2rayU



1.下载

```
https://github.com/yanue/V2rayU
```

2.`unzip xxxx.zip -d [name]` 解压

3.开启运行

4.pac配置，从粘贴板导入


## mac  httpie

```
1.brew update
2.brew install httpie
3.`http -v get www.baidu.com`

发起http post  url参数
http -v post [url]  paramer==xxxx
发起post  json参数
http -v post [url] paramer=xxx

```

## 切换jdk



# mac 切换多版本jdk环境

## 效果


![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3262e7bd29d045dfaaf85c2e3da67dd4~tplv-k3u1fbpfcp-watermark.image)

## do

> jdk8,jdk11


1.修改.bash_profile


```shell

export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles



export M2_HOME=/Users/xxx/workspace1/devtools/apache-maven-3.8.1
export PATH=$PATH:$M2_HOME/bin

export JAVA_HOME=$(/usr/libexec/java_home)

export JAVA_7_HOME="/Library/Java/JavaVirtualMachines/zulu-7.jdk/Contents/Home"
alias jdk7='export JAVA_HOME=$JAVA_7_HOME'

export JAVA_11_HOME="/usr/local/Cellar/openjdk@11/11.0.10/libexec/openjdk.jdk/Contents/Home"
alias jdk11='export JAVA_HOME=$JAVA_11_HOME'

export JAVA_HOME=$JAVA_11_HOME


#
export TOMCAT_HOME=/Users/xxx/workspace1/devtools/apache-tomcat-8.5.69
PATH=$PATH:$TOMCAT_HOME/bin
```

