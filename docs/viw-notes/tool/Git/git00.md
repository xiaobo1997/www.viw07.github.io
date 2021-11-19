[TOC]



# Git

## 1.安装&设置



默认安装即可

设置你的个人信息

```bash
git config --global user.name "你的名字"
git config --global user.email "你的邮箱"
```

- `git concfg -l` 查看信息![image-20200902002225377](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902002225377.png)

- `git version`  版本 ![image-20200902002424624](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902002424624.png)



> mac 或 win 下 生成 ssh
>
> 1.生成ssh
>
> ```shell
> # ed25519算法
> ssh-keygen -t ed25519 -C "your_email@example.com"
> # rsa算法
> ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
> ```
>
> 2.连续enter 就可以肯定生成的ssh了
>
> ```shell
> gitlab 添加即可
> 
> # 查看ssh
>  cat ~/.ssh/id_rsa.pub
> ```
>
> 



## 2.架构&仓库

### 2.1架构

版本库：工作区中的隐藏目录 `.git` 这个目录是git 的版本库，是git管理的所有内容

暂存区： 版本库中的一个临时区域， 保存下一步要提交的文件

分支： 版本库中有多个分支(分支是git的灵魂) 提交的文件存储在分支中

![image-20200902003144785](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902003144785.png)

> 我们从工作区 不同的编辑文件 然后提交给stage 然后某一刻你觉得可以 提交给master分支  形成我们的一个又一个的版本，

---

### 2.2仓库

这个仓库的所有文件都会被git管理起来， 相当于项目的根目录，仓库中的每一个文件的变化都会由git追踪

![image-20200902003617497](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902003617497.png)



新建仓库:

选择一个我们的目录 然后 执行  `git init` 即可

![image-20200902003804505](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902003804505.png)

![image-20200902003827698](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902003827698.png)



这个git是一个隐藏文件，相当于我们的仓库目录,在这个repo1 除 .git 以外就是我们的  工作区



## 3.基本操作

### 3.1 仓库状态

我们新建一个文件 `xx.txt`

- `git status` 当前状态信息，哪些还没有提交，哪些在暂存
- `git add [xxx]` 添加指定文件
- `git add .` 添加所有

![image-20200902004707620](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902004707620.png)

添加到分支 master

- `git commit -m "你的备注消息"`  从stage commit 到 分支中成为一个版本

![image-20200902004949763](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902004949763.png)



## 4.远程仓库

> 上面我们操作的是本地仓库， 多人协作 开发时 我们需要一个远程仓库 



我们在 git 服务器上   比如 github   && gitee 上创建我们的仓库

然后我们需要创建用户

### gitee

**创建远程仓库**

![image-20200902005849249](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902005849249.png)

> 现在我们创建了一个空白的仓库, 这个仓库名尽量和我们的工程名保持一致更好

![image-20200902010036606](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902010036606.png)



查看一个我们之前创建的目录的状态

![image-20200902010104475](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902010104475.png)

然后我们绑定地址  把我们的目录和远程仓库 关联起来

`git remote add origin https://gitee.com/xiaobo97/git_0902_typecho_test.git`

- `git remote add origin [xxx]`
- `git remote -v` 

![image-20200902010324556](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902010324556.png)



然后我们上传 把我们的本地仓库内容上传到远程仓库

- `git push origin master`   将本地 master分支 上传远程仓库的master分支



![image-20200902011422421](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902011422421.png)



- 输入帐号密码

![image-20200902010615164](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902010615164.png)

- 刷新我们的仓库![image-20200902011550289](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902011550289.png)



> ```shell
> // 强推 会覆盖远程仓库
> git push -f origin master
> ```



### 克隆与共享

#### 1.克隆

一般我们新进公司或者项目组都是要克隆的代码， 所有 `git clone` 复制到本地

本地新建我们的目录

![image-20200902012028149](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902012028149.png)

然后克隆

`git clone [https]`

 ![image-20200902012126702](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902012126702.png)

![image-20200902012201670](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902012201670.png)

#### 2.多开发共享

使用两个仓库 模拟 协作开发

![image-20200902012307990](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902012307990.png)



我对repo1 仓库作出一些修改操作 模拟我们的真实开发

![image-20200902012529279](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902012529279.png)

然后 `git add .` 提交暂存区

![image-20200902012833143](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902012833143.png)

然后刷新我的远程仓库 可以看到已经提交了 

![image-20200902012922857](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902012922857.png)



回到我的repo2 仓库，拉取代码

- `git pull origin master`

![image-20200902013122193](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902013122193.png)

- 看一下内容是我在repo1更新的 ，![image-20200902013231239](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902013231239.png)

#### 3.命令

`git remote add 标识名[origin] 远程地址[https]`  本地关联远程仓库

`git push 标识名[默认origin]  master` 将本地仓库内容上传 远程仓库master分支，如果是其他分支就更改就可以了，如 `git push origin dev`，开发分支在开发分支提交，dev和到mster上去不能随随便便和，否则分支就没有意义了

` git pull 标识名[默认origin] master` 从远程仓库下载到本地仓库

`git clone 远程地址[https]` 将远程仓库 复制到本地，第一次使用clone，后面拉取使用的都是pull





## 5.分支

### 1.初始分支(git的灵魂)

![image-20200902193928397](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902193928397.png)

分支 相当于我们每一个提交以后的一个版本，我们的指针指向该地址

新建一个测试仓库  `repo03`

![image-20200902194248653](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902194248653.png)



下面的操作就是上面指针指向我们最新的的提交点

![image-20200902195232439](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902195232439.png)

然后我们继续修改 添加 该工作区内部

![image-20200902195529582](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902195529582.png)

> 现在我们的指针 已经指向我们第三次提交的地址，因为现在是我们提交的最新的版本
>
> 但是我没有对xiaobo03.txt修改， 只是增加了 xiaobo03.java ，在这一次最新的提交中，我们指针指向的节点里面`保存的是 上一次 xiaobo03.txt 的地址(上一次git commit链接)`，保存的仅仅是指向上一次提交的地址，xiaobo.txt 文件内容还是保存在上一次，因为我没有对 xiaobo03.txt做修改

查看状态 `git status`

![image-20200902200050725](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902200050725.png)

然后我们就每一次提交，形成一个又一个版本，我们的指针也一种往后走



### 2.对分支的基本操作

-  `git branch` 查看当前仓库的分支 
- 仓库中默认的只有 master(相当于我们的主分支)分支
- 执行 `git commit ` 默认在master(我们的默认分支)分支上保存版本
- 我们之前在当前仓库的操作，都是基于 `master` 分支上进行

![image-20200902200458102](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902200458102.png)



> `master` 分支是我们的主分支，我们提交到master分支的代码。都是直接进入我们项目工程中，所以我们要保证质量，新建分支来开发，
>
> 然后当某一个时间点 通过测试，我们再添加到master分支，
>
> 这样我们项目在推进的过程中，主分支代码都是有一定质量保证的

创建新的分支 

- `git branch [xxx]` 一般是 xxx dev  test等

![image-20200902201608130](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902201608130.png)

前面那个  `*` 表示我们当前所在分支

切换分支

- `git chechout [xxx]`

![image-20200902201749575](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902201749575.png)

> 我们切换以后，我们向我们的工程提交代码 就是在 dev 分支上 

然后再新建文件 试试 是否可行



![image-20200902202231242](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902202231242.png)

> 可以看到我们 在 `dev` 分支创建的 `xiaobo03ha.txt` 在我们切换分支以后 `git checkout master` 我们就看不到了
>
> 所以我们需要注意 要切换相应的分支 

### 3.分支的进阶

#### 1.新分支的内容

![image-20200902202841440](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902202841440.png)

也就是说 我们 当前master主分支包括的 我们dev都包括了，因为我们是在 master指针指向03时创建的 dev分支，包括的内容都是一样的



从上面可以看到  每一个分支都有一个指针，相当于我们创建一个新分支， 是先创建一个指针，新分支 指针和当前分支 指向同一个提交点

#### 2. 多分支走向

在新分支和 master分支 分别  git add  和 git commit ，分支走向

![image-20200902203358012](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902203358012.png)



### 4.查看日志



- `git log`
- `git log --oneline`

![image-20200902203850498](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902203850498.png)

> 可以看到我在 主分支和dev分支提交内容是不一样的，前面三个是一样的

### 5. 分支合并

对某一个 时间点 你的代码经过了测试，然后可以合并到主工程中去， master中

- `git merge [分支aaa] [合并分支bbb]`

- 分支的合并 ： 1.快速合并 2三方合并

#### 1.快速合并

如果A分支 当前的修改 是基于 B分支的 ，我们B合并A 就是移动指针

我在master 创建 两个文件 并提交

![image-20200902204855971](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902204855971.png)

新建分支 `dev` 并切换

![image-20200902205001683](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902205001683.png)

![image-20200902205115066](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902205115066.png)

在dev 上 提交 修改

![image-20200902205338795](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902205338795.png)

![image-20200902205440234](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902205440234.png)

然后合并我们的 分支 

![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902205714723.png)

> `Fast-forward` 快速合并， 相当于我们 把masrer分支的指针 指向了dev ，只是移动了一个指针， 
>
> 快速合并的前提是我们的主分支 没移动 ，是dev分支 在推进

![image-20200902210743083](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902210743083.png)

#### 2.三方合并

![image-20200902210833416](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902210833416.png)

> 上面不满足 快速合并的条件
>
> 我们需要把 1,2,3合并才可以

现在我们在主分支 做一次提交

![image-20200902211339454](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902211339454.png)

然后我们在 dev 上 再 修改内容， 再提交

![image-20200902211512997](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902211512997.png)

现在 master 和dev 都有了不同的提交内容  

![image-20200902211620357](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902211620357.png)

> 上图 我们的master的提交点 和 dev的提交点不一样，前三个是一样的，就不可以用快速合并

现在我们的 提交点指针状态 是下面，接下来我们用做一次 三方合并，master 和dev的共同起点 2 ，和 4 和 3  ，然后生成我们的一个新的主分支提交点

![image-20200902211823541](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902211823541.png)

> 注意出现了冲突 回退版本 `git reset --hard head`
>
> ![image-20200902212452038](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902212452038.png)

- 查看 `git log --oneline --graph` 提交状态图
- 继续看下面  怎么处理冲突



### 6. 解决冲突

> 当我们 同时修改了同一个文件内容  然后提交合并会出现冲突

![image-20200902213558111](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902213558111.png)

文件的内容 如下（HEAD是当前分支， 下面是dev分支上 这个文件的内容）

然后是 现在这两个文件无法直接合并

![image-20200902213603358](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902213603358.png)

> 这种情况 一般是由开发者 自己协商去决定要哪个版本，然后一起处理， 

比如说 我们经过 协商  我们留  HEAD 指向中的内容  也就是 `三方合并  master 修改了内容`



![image-20200902213946103](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902213946103.png)

> 上图  比如说 两个开发者经过交流，留下什么内容，一个的内容，还是两个人内容都保留，然后我们再提交

然后我们再提交 即可

![image-20200902214118646](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902214118646.png)

然后我们再查看我们之前的三方合并

![image-20200902214217071](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902214217071.png)



## 6.IDEA 使用Git



![image-20200902215113286](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902215113286.png)

我的是没更新，如果你要更新的话

使用淘宝境像 下载安装更新

`https://npm.taobao.org/mirrors/git-for-windows/`

如果你直接 CMD更新 比较慢![image-20200902215229698](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902215229698.png)

然后我们随便创建个项目

![image-20200902215458907](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902215458907.png)

本地打开项目 右键 项目

![image-20200902215605224](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902215605224.png)

看到目录 直接输入CMD

![image-20200902215651449](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902215651449.png)

### 1.直接创建

`git init` 

![image-20200902215713288](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902215713288.png)

后面的开发都是在这个仓库中  进入git版本控制内

![image-20200902215757745](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902215757745.png)

### 2.IDEA 创建

![image-20200902215857730](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902215857730.png)

### 3.忽略部分上传文件

比如我们的本地编译的文件不需要上传

![image-20200902220147608](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902220147608.png)

项目根目录下创建 `.gitignore` 

正确的顺序是 我们 先 创建忽略文件再 初始化仓库

![image-20200902220445889](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902220445889.png)

![image-20200902220614514](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902220614514.png)

然后再创建仓库

下面红色都是git未跟踪的文件

![image-20200902220731064](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902220731064.png)



而这些是 git忽略的

![image-20200902220810713](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902220810713.png)

然后我们提交

![image-20200902220906705](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902220906705.png)

![image-20200902221031456](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902221031456.png)

当我们对工作区修改时 会提示 是否add

![image-20200902221133418](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902221133418.png)

可以看到区别

![image-20200902221212409](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902221212409.png)

然后再提交

![image-20200902221422731](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902221422731.png)

### 4.创建远程仓库 协作开发



![image-20200902221630404](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902221630404.png)

命名 和仓库名保持一致(建议)

然后就创建了一个空白的仓库

然后我们需要关联

![image-20200902221758407](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902221758407.png)

然后输入你仓库的地址 然后登录提交

![image-20200902221929346](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902221929346.png)

然后我们PUSH就可以了 （右下角提醒）

![image-20200902222036202](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902222036202.png)

刷新 Giee  仓库

![image-20200902222109037](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902222109037.png)

### 5 创建分支

点击右下角  `Git master`

![image-20200902222149443](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902222149443.png)

![image-20200902222240852](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902222240852.png)

切换分支

![image-20200902222330082](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902222330082.png)

然后我们在dev分支上 修改 增加 内容

然后再提交

![image-20200902222556849](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902222556849.png)

查看 log

![image-20200902222855459](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902222855459.png)

### 6.push远程仓库



![image-20200902223040644](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902223040644.png)

![image-20200902223105862](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902223105862.png)

### 7.克隆仓库&更新仓库

比如有创建好的仓库我们可以直接更新到本地

![image-20200902223244780](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902223244780.png)

![image-20200902223434116](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902223434116.png)

![image-20200902223606108](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20200902223606108.png)



