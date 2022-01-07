# idea-git小问题汇总

> 以下不说明都是在mac下

## 修改创建文件的作者信息

打开设置Preferences 然后选择 editor,点击 file and templates ，点击 右边的 includes  选择 file header

如我的设置

```
/**
 * @author xhb
 * @email xiaohaibo77@gmail.com
 * @Date ${DATE} ${TIME}
 * @Description :1.
 */

```


## 合并分支到当前分支

如  你想把你同事正在开发的分正代码合并到你的当前开发分正，然后你同事开发的功能你需要用 并且他是先于你之前上线合并master，

我当前的分支![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20220107130707.png)


我需要合并的分支![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20220107130749.png)

> 如果没有就fetch一下

![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/20220107130846.png)



