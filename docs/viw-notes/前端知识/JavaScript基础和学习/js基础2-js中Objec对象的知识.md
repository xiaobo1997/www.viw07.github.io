[TOC]

# 1.Object(对象)基础知识



## 1.Object对象

> 对象则用来存储键值对和更复杂的实体。在 JavaScript 中，对象几乎渗透到了这门编程语言的方方面面。

```js
// 比如人，他的有名字属性 年龄属性 等... 这就是一个对象  名字是key  名字属性值 是value 
// 创建对象
let user = new Object();
let user = {};
```



> 操作属性

```js
var usr = {
 name = "A",
 age = 18
};
// 访问属性
alert(usr.name);
// 删除属性
delete usr.name;

var u = {
    "say some" : false;// 多词属性名必须加引号
}
```



> 多词属性 使用方括号
>
> 对于 多词属性 不能用 `.` 了

```js
var user = {};
user["say some"] = true;// 设置
alert(user["say some"]); // 使用
delete user["say some"]; // 删除

var a
```



> 计算属性：**当创建一个对象时，我们可以在对象字面量中使用方括号。**

```js
let fruit = prompt("Which fruit to buy?", "apple");

let bag = {
  [fruit]: 5, // 属性名是从 fruit 变量中得到的
};

alert( bag.apple ); // 5 如果 fruit="apple"
```



> ## 属性存在性测试，“in” 操作符

```js
let user = { name: "John", age: 30 };

//in 的左边必须是 属性名。  省略引号，就意味着左边是一个变量，它应该包含要判断的实际属性名。
alert( "age" in user ); // true，user.age 存在
alert( "blabla" in user ); // false，user.blabla 不存在。
```



> for.. in循环
>
> 为了遍历一个对象的所有键（key），可以使用一个特殊形式的循环：`for..in`。

```js
let user = {
  name: "John",
  age: 30,
  isAdmin: true
};

for (let key in user) {
  // keys
  alert( key );  // name, age, isAdmin
  // 属性键的值
  alert( user[key] ); // John, 30, true
}
```



### 1.小结



对象是具有一些特殊特性的关联数组。

它们存储属性（键值对），其中：

- 属性的键必须是字符串或者 symbol（通常是字符串）。
- 值可以是任何类型。

我们可以用下面的方法访问属性：

- 点符号: `obj.property`。
- 方括号 `obj["property"]`，方括号允许从变量中获取键，例如 `obj[varWithKey]`。

其他操作：

- 删除属性：`delete obj.prop`。
- 检查是否存在给定键的属性：`"key" in obj`。
- 遍历对象：`for(let key in obj)` 循环。

我们在这一章学习的叫做“普通对象（plain object）”，或者就叫对象。

JavaScript 中还有很多其他类型的对象：

- `Array` 用于存储有序数据集合，
- `Date` 用于存储时间日期，
- `Error` 用于存储错误信息。
- ……等等。

### 2.判断 对象 是否为空

```js
// 
let data = {};
if(Object.keys(data).length == 0){
  alert("yes");
} 
```



---



## 2.对象拷贝  引用



> 原始数据类型 ：字符串，数字，布尔值 —— 被“作为整体”赋值/拷贝。
>
> ```js
> //创建了两个独立的变量，每个都存储着字符串 "Hello!"。
> let message = "Hello!";
> let phrase = message;
> ```
>
> 对象  ：**变量存储的不是对象自身，而是该对象的“内存地址”，换句话说就是一个对该对象的“引用”。**  相当于 **当一个对象变量被拷贝 —— 引用则被拷贝，而该对象并没有被复制。**
>
> ```js
> let user = {
>   name: "John"
> };
> ```
>
> ```js
> let user = { name: "John" };
> 
> let admin = user; // 拷贝引用
> //现在我们有了两个变量，它们保存的都是对同一个对象的引用： 相当于 user 和admin两个 对象变量 都指向同一个内存地址 name
> //用任何变量来访问该对象并修改它的内容：
> // 比如 admin.name = 'aaa';  那么 user.name = 'aaa';
> ```
>
> 



> 克隆和合并
>
> 拷贝一个对象变量会又创建一个对相同对象的引用。
>
> 可以使用 Object.assign(dest, [src1, src2, src3...])

```js
let user = { name: "John" };

let permissions1 = { canView: true };
let permissions2 = { canEdit: true };

// 将 permissions1 和 permissions2 中的所有属性都拷贝到 user 中
Object.assign(user, permissions1, permissions2);

// 现在 user = { name: "John", canView: true, canEdit: true }
```



> 深层克隆
>
> 假设 `user` 的所有属性均为原始类型。但属性可以是对其他对象的引用。 使用深层克隆【“深拷贝”。】



> 总结：
>
> 对象通过引用被赋值和拷贝。换句话说，一个变量存储的不是“对象的值”，而是一个对值的“引用”（内存地址）。因此，拷贝此类变量或将其作为函数参数传递时，所拷贝的是引用，而不是对象本身。
>
> 所有通过被拷贝的引用的操作（如添加、删除属性）都作用在同一个对象上。
>
> 为了创建“真正的拷贝”（一个克隆），我们可以使用 `Object.assign` 来做所谓的“浅拷贝”（嵌套对象被通过引用进行拷贝）或者使用“深拷贝”函数，例如 [_.cloneDeep(obj)](https://lodash.com/docs#cloneDeep)。



## 3.垃圾回收

> 可达性： js中对内存的管理主要概念是可达性
>
> 什么叫可达：如果一个值可以通过引用或引用链从根访问任何其他值，则认为该值是可达的。

```js
//  在前面我们知道 对象保存的是 这个对象的引用   相当于 全局变量 user 引用了 对象{ naem: "aaa",age:18 } 
let user = {
 name : "aaa",
 age : 18
};

//
user = null; // 现在这个user的值被重写了， 这个引用就没了  那么aaa 属性也就是不可达 的了

```

==所以现在 aaa变成不可达的了。因为没有引用了，就不能访问到它了。垃圾回收器会认为它是垃圾数据并进行回收，然后释放内存。==

> 两个引用
>
> 如果有两个引用指向 同一个对象

```js
let a = {
	name : "aaa"
}


let b = a;
//此时 a  和 b 都指向了 {name:"aaa"}
```

```js
// 我们让 user 重写 不可达，
user = null

// 对象仍然可以被通过 admin 这个全局变量访问到，所以对象还在内存中。如果我们又重写了 admin，对象就会被删除。
alert(b.name) = aaa; // b.name 还是可以访问
```



> 相互关联的对象

```js
function marry(man, woman) {
  woman.husband = man;
  man.wife = woman;

  return {
    father: man,
    mother: woman
  }
}

let family = marry({
  name: "John"
}, {
  name: "Ann"
});
```

产生的内存结构：

![image-20201024002902168](https://xiaoboblog-bucket.oss-cn-hangzhou.aliyuncs.com/blog/image-20201024002902168.png)



> 几个对象相互引用，但外部没有对其任意对象的引用，这些对象也可能是不可达的，并被从内存中删除。

```js
//如对上 family
family = null;
```



> 内部的垃圾回收算法
>
> 垃圾回收的基本算法被称为 “mark-and-sweep”
>
> 定期执行以下“垃圾回收”步骤：
>
> - 垃圾收集器找到所有的根，并“标记”（记住）它们。
> - 然后它遍历并“标记”来自它们的所有引用。
> - 然后它遍历标记的对象并标记 **它们的** 引用。所有被遍历到的对象都会被记住，以免将来再次遍历到同一个对象。
> - ……如此操作，直到所有可达的（从根部）引用都被访问到。
> - 没有被标记的对象都会被删除。

```js
//一些优化建议：
分代收集（Generational collection）—— 对象被分成两组：“新的”和“旧的”。许多对象出现，完成它们的工作并很快死去，它们可以很快被清理。那些长期存活的对象会变得“老旧”，而且被检查的频次也会减少。

增量收集（Incremental collection）—— 如果有许多对象，并且我们试图一次遍历并标记整个对象集，则可能需要一些时间，并在执行过程中带来明显的延迟。所以引擎试图将垃圾收集工作分成几部分来做。然后将这几部分会逐一进行处理。这需要它们之间有额外的标记来追踪变化，但是这样会有许多微小的延迟而不是一个大的延迟。

闲时收集（Idle-time collection）—— 垃圾收集器只会在 CPU 空闲时尝试运行，以减少可能对代码执行的影响。
```



