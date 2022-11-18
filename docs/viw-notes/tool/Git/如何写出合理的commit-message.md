

# 1.写在前面

因为发现自己的git commit 有时候经常性的 就写个 commit 作为 message，这是错误的做法

# 2.格式


Commit message 都包括三个部分：Header，Body 和 Footer。

```
header部分 <type>(<scope>): <subject>
// 空一行
<body>
// 空一行
<footer>
```

**type:**
feat：新功能（feature）
fix：修补bug
docs：文档（documentation）
style： 格式（不影响代码运行的变动）
refactor：重构（即不是新增功能，也不是修改bug的代码变动）
test：增加测试
chore：构建过程或辅助工具的变动

**scope**
scope用于说明 commit 影响的范围，比如数据层、控制层、视图层等等，视项目不同而不同。

**subject**
subject是 commit 目的的简短描述，不超过50个字符。


# 参考

https://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html
https://docs.google.com/document/d/1QrDFcIiPjSLDn3EL15IJygNPiHORgU1_OOAqWjiDU5Y/edit#heading=h.8gbcep5xnw19

