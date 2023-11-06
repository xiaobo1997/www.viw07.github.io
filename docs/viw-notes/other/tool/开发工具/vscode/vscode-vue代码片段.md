

# vscode-vue代码片段


```js
{
	"生成 vue 模板": {
		"prefix": "vue",
		"body": [
		"<template>",
		"<div></div>",
		"</template>",
		"",
"<script>",
"//这里可以导入其他文件（比如：组件，工具 js，第三方插件 js，json文件，图片文件等等）",
"//例如：import 《组件名称》 from '《组件路径》';",
"",
"export default {",
"//import 引入的组件需要注入到对象中才能使用",
"components: {},",
"props: {},",
"data() {",
"//这里存放数据",
"return {",
"",
"};",
"},",
"//计算属性 类似于 data 概念",
"computed: {},",
"//监控 data 中的数据变化",
"watch: {},",
"//方法集合",
"methods: {",
"",
"},",
"//生命周期 - 创建完成（可以访问当前 this 实例）",
"created() {",
"",
"},",
"//生命周期 - 挂载完成（可以访问 DOM 元素）",
"mounted() {",
"",
"},",
"beforeCreate() {}, //生命周期 - 创建之前",
"beforeMount() {}, //生命周期 - 挂载之前",
"beforeUpdate() {}, //生命周期 - 更新之前",
"updated() {}, //生命周期 - 更新之后",
"beforeDestroy() {}, //生命周期 - 销毁之前",
"destroyed() {}, //生命周期 - 销毁完成",
"activated() {}, //如果页面有 keep-alive 缓存功能，这个函数会触发",
"}",
"</script>",
"<style lang='scss' scoped>",
"//@import url($3); 引入公共 css 类",
"$4",
"</style>"
],
"description": "生成 vue 模板"
}
}
```



# vscode-vue注释模板

新建全局用户片段

![image-20210225211207705](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210225211207705.png)

```vue
{
	"Print to vueNote": {
	  "prefix": "vueNote",
	  "body": [
		"<!-- ",
		" * @fileName: ${TM_FILENAME} ",
		" * @author: xiaobo ",
		" * @email: xiaobo97@163.com ",
		" * @gitee: xiaobo1997 ",
		" * @date: ${CURRENT_YEAR}-${CURRENT_MONTH}-${CURRENT_DATE} ${CURRENT_HOUR}:${CURRENT_MINUTE}:${CURRENT_SECOND}",
		" * @version: V1.0.0 ",
		" * @description: ",
		"!-->"
	  ],
	  "description": "在vue文件开头配置注释"
	}
  }
  
  
```

![image-20210225211254196](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20210225211254196.png)




# markdown 代码片段


![](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/typecho/20211229131730.png)

```console

{

"markdown-code-json": {
		"scope": "markdown",
		"prefix": "mark-code-json",
		"body": [
			"```json   ",

			"```"
		],
		"description": "Log output to console"
	},
	"markdown-code-java": {
		"scope": "markdown",
		"prefix": "mark-code-java",
		"body": [
			"```java  ",

			"```"
		],
		"description": "Log output to console"
	}

}

```

