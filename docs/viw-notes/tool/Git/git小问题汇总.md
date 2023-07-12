<!-- TOC -->

- [写在前面](#写在前面)
  - [不显示提交人信息](#不显示提交人信息)
  - [git push错误failed to push some refs to](#git-push错误failed-to-push-some-refs-to)
  - [每一次git push 都需要输入用户名密码](#每一次git-push-都需要输入用户名密码)
  - [HTTP/2 stream 1 was not closed cleanly before end of the underlying stream。](#http2-stream-1-was-not-closed-cleanly-before-end-of-the-underlying-stream)

<!-- /TOC -->

# 写在前面

> 这里汇总一下日常开发中使用git的一点小问题




## 不显示提交人信息

背景：

当前电脑git全局设置是公司的邮箱和用户名，因为平常需要提交到gitlab上，但是如果你有个人仓库，比如你自己的私人github仓库，你提交的时候就会显示是 公司的用户名提交，无法在个人首页中显示提交数据


修改：

全局使用的是公司的gitlab的邮箱信息等

进入当前仓库目录下 比如 进入 viw-notes目录下

使用
`git config user.name`  和 `git config user.email` 查看当前仓库的提交用户信息
然后修改成  github 上的信息, 比如我github的用户名 是xiaobo1997  邮箱是 "xxx@qq.com",前提是要添加了公钥



## git push错误failed to push some refs to

远程和本地不一致，两种解决办法

1.强制push，不建议，公司应该不会给你这个权限

2.合并代码 如果使用了类似于idea 这种编辑器，直接拉代码 合并就可以了，如果是命令行 可以使用 `git pull --rebase origin master`   当前是master分支，也可以是别的分支



## 每一次git push 都需要输入用户名密码

其中一种情况可能是 你clone仓库的时候是使用https 而不是ssh,

> 因为绑定是因为https的关系 修改成 ssh就可以了

![image-20201115160107064](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201115160107064.png)


## HTTP/2 stream 1 was not closed cleanly before end of the underlying stream。

> mac 执行  brew update 一直卡着，用的国内的源，下载包是没问题

`git config --global http.version HTTP/1.1`


