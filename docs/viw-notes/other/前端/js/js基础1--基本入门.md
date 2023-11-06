[TOC]



# 1.数据类型和转换

##  1. `8种类型介绍`

> js有8种基本数据类型(7个原始数据类型，1个引用类型)
>
> js是一种 `dynamically typed` ，相当于 你在定义变量以后，不会被限制为某一种具体的数据类型
>
> ```js
> let message = "hello";
> message = 123;
> ```



| Number                 | 可以表示整数和浮点数，可以加减乘除等，还包括特殊`Infinity`(无穷大)、`-Infinity` 和 `NaN`(计算错误，不正确的使用)。 | let n = 123; n = 12.111;                                     |
| ---------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| BigInt                 | js中，number无法表示大于2^53-1,                              | const bigInt = 1234.....123n; // const 不能被改变的变量      |
| String                 | js中，有三种包含字符串的方式，1. "AA"，2.'AA',3.  `AA`（反引号），如例中，str3 ，${...} 里面可以放，表达式，变量等等 | let str = "hello"; let str2 = 'aaa'; let str3 = `aaa ${str}`; |
| Boolean                | 真值  `true` and  `fasle`                                    | let a= true; let b = false; let c  = 4>1;                    |
| null值                 | 特殊的null值 不需要任何一个类型，是独立的，只包含 null 值 ，js中的null不是对一个不存在的object 引用和null指针，仅仅是表示一个代表 无，空，值未知的特殊值 | let name = null;                                             |
| "undefind"值           | undefind 是 未被赋值，也就是说，一个变量声明，但是没赋值，就是 undefind | let age; alert(age);                                         |
| object类型和symbol类型 | `object` 类型是一个特殊的类型,其他所有的数据类型都被称为“原始类型”，object储存数据集合和更复杂的实体  `symbol` 类型用于创建对象的唯一标识符。 |                                                              |
| typeof运算符           | `typeof` 运算符返回参数的类型。运算符： typeof x。 作为函数形式：typeox(x)。对 `typeof x` 的调用会以字符串的形式返回数据类型 | typeof undefined // "undefined"    typeof 0 // "number"   typeof 10n // "bigint"   typeof "foo" // "string"   typeof Symbol("id") // "symbol"   *typeof null // "object"  * |

### 总结

JavaScript 中有八种基本的数据类型（译注：前七种为基本数据类型，也称为原始类型，而 `object` 为复杂数据类型）。

- `number` 用于任何类型的数字：整数或浮点数，在 `±(253-1)` 范围内的整数。
- `bigint` 用于任意长度的整数。
- `string` 用于字符串：一个字符串可以包含 0 个或多个字符，所以没有单独的单字符类型。
- `boolean` 用于 `true` 和 `false`。
- `null` 用于未知的值 —— 只有一个 `null` 值的独立类型。
- `undefined` 用于未定义的值 —— 只有一个 `undefined` 值的独立类型。
- `symbol` 用于唯一的标识符。
- `object` 用于更复杂的数据结构。

我们可以通过 `typeof` 运算符查看存储在变量中的数据类型。

- 两种形式：`typeof x` 或者 `typeof(x)`。
- 以字符串的形式返回类型名称，例如 `"string"`。
- `typeof null` 会返回 `"object"` —— 这是 JavaScript 编程语言的一个错误，实际上它并不是一个 `object`。



## 2.转换

### 1.字符串转换

> 字符串转换 false 转 "false"  null 转 "null"

```js
let value = true;
alert(typeof value); // boolean
value = String(value);
alert(typeof value); // string
```



### 2.数字类型

> 在算术函数和表达式中，会自动进行 number 类型转换

```js
alert("6"/"3"); // 2 string 自动转换成 number 类型后进行计算
```

> 显式转换 

```js
let str = "123";
alert(typeof str);
let num = Number(str);
alert(typeof num);
```

> 该字符串不是一个有效的数字，转换的结果会是 `NaN`

```js
let age = Number("an arbitrary string instead of a number");

alert(age); // NaN，转换失败
```

> 转换规则

| 值              | 变成……                                                       |                                                              |
| :-------------- | :----------------------------------------------------------- | ------------------------------------------------------------ |
| `undefined`     | `NaN`                                                        | 对 `undefined` 进行数字型转换时，输出结果为 `NaN`，而非 `0`。 |
| `null`          | `0`                                                          |                                                              |
| `true 和 false` | `1` and `0`                                                  |                                                              |
| `string`        | 去掉首尾空格后的纯数字字符串中含有的数字。如果剩余字符串为空，则转换结果为 `0`。否则，将会从剩余字符串中“读取”数字。当类型转换出现 error 时返回 `NaN`。 | alert( Number("123z") );      // NaN（从字符串“读取”数字，读到 "z" 时出现错误） |



### 3.boolean 转换

> 转换规则如下：
>
> - 直观上为“空”的值（如 `0`、空字符串、`null`、`undefined` 和 `NaN`）将变为 `false`。
> - 其他值变成 `true`。

```js
alert( Boolean(1) ); // true
alert( Boolean(0) ); // false

alert( Boolean("hello") ); // true
alert( Boolean("") ); // false

alert( Boolean("0") ); // true
alert( Boolean(" ") ); // 空白，也是 true（任何非空字符串都是 true）
```



## 3.交互

> 与用户交互的 3 个浏览器的特定函数：

```js
// alert
alert("Hello");
// prompt  title:显示给用户的文本   default:指定 input 框的初始值。
result = prompt(title, [default]);
// confirm  显示一个带有 question 以及确定和取消两个按钮的模态窗口。
result = confirm(question);
```



> 用浏览器内置对象输出log



```js
let a = {};
// 在浏览器控制台输出
console.log(a);
```



---

# 2.基础运算符和值

> 基本数学运算符

```js
//加减乘除 + -  *  /
//取余  a % b 的结果是 a 整除 b 的 余数  
alert(8%3); // 2
aleet(8%2); // 0
//求幂 求幂运算 a ** b 是 a 乘以自身 b 次。
alert(2**2); // 2*2 = 4
alert(2**3); // 2*2*2 = 8
```

## 1.运算符

>  二元 `+` 可以求和 拼接字符串

```js
// 1.
let s = "a"+"b";
alert(s); // ab;
// 2.
alert(1+'2'); // 12
// 3.
alert(1+2+'1');//31  第一个 + 将两个数字相加，所以返回 3，然后下一个 + 将字符串 1 加入其中，所以就是 3 + '1' = 31。

```

> 二元 `+` 是唯一一个以这种方式支持字符串的运算符。其他算术运算符只对数字起作用，并且总是将其运算元转换为数字。

```js
alert( 6 - '2' ); // 4，将 '2' 转换为数字
alert( '6' / '2' ); // 3，将两个运算元都转换为数字
```



> 一元运算符加号，或者说，加号 `+` 应用于单个值，对数字没有任何作用。但是如果运算元不是数字，加号 `+` 则会将其转化为数字。

```js
var x = 1;
alert(+x); // 1
// 转为数字
alert(+true);// 1
alert(+"");// 0

var v1 = "1";
var v2 = "2";
alert(v1+v2); // 12
alert(+v1 +  +v2); // 3
```

## 2.优先级

> 圆括号拥有最高优先级，所以如果我们对现有的运算顺序不满意，我们可以使用圆括号来修改运算顺序，就像这样：`(1 + 2) * 2`。
>
> 在 JavaScript 中有众多运算符。每个运算符都有对应的优先级数字。数字越大，越先执行。如果优先级相同，则按照由左至右的顺序执行。

| 优先级 | 名称     | 符号 |
| :----- | :------- | :--- |
| …      | …        | …    |
| 17     | 一元加号 | `+`  |
| 17     | 一元负号 | `-`  |
| 16     | 求幂     | `**` |
| 15     | 乘号     | `*`  |
| 15     | 除号     | `/`  |
| 13     | 加号     | `+`  |
| 13     | 减号     | `-`  |
| …      | …        | …    |
| 3      | 赋值符   | `=`  |



## 3.赋值和值的修改

> `=` 是一个运算符，
>
> 语句 `x = value` 将值 `value` 写入 `x` **然后返回 x**。

```js
var   a = 1,b=2;
var c = 4-(a= b+b);// 0
```

> 链式赋值
>
> 链式赋值从右到左进行计算。

```js
var a,b,c;
a =b =c = 1+1;
alert(a);//2
alert(b);//2
```

> `+=`, `*=`, `/=` 等
>
> 这类运算符的优先级与普通赋值运算符的优先级相同，所以它们在大多数其他运算之后执行

```js
var n = 2;
n *= 2+2;
alert(8)
```

> `++`   ， `--`
>
> ==自增/自减只能应用于变量。试一下，将其应用于数值（比如 `5++`）则会报错。==
>
> `++/--` 运算符同样可以在表达式内部使用。它们的优先级比绝大部分的算数运算符要高。

```js
var a = 1;
alert(a--); // 1
alert(a++); // 0
alert(--a); // 0
alert(++a);// 1
```

```js
let counter = 1;
alert( 2 * ++counter ); // 4
```

> 位运算符 （这个在之前文章有写过）
>
> **位运算符把运算元当做 32 位整数，并在它们的二进制表现形式上操作。**
>
> - 按位与 ( `&` )
> - 按位或 ( `|` )
> - 按位异或 ( `^` )
> - 按位非 ( `~` )
> - 左移 ( `<<` )
> - 右移 ( `>>` )
> - 无符号右移 ( `>>>` )

---

> 逗号运算符 `,` 是最少见最不常使用的运算符之一。
>
> ==逗号运算符能让我们处理多个语句，使用 `,` 将它们分开。每个语句都运行了，但是只有最后的语句的结果会被返回。==
>
> 请注意逗号运算符的优先级非常低，比 `=` 还要低，因此上面你的例子中圆括号非常重要。
>
> 如果没有圆括号：`a = 1 + 2, 3 + 4` 会先执行 `+`，将数值相加得到 `a = 3, 7`，然后赋值运算符 `=` 执行, ‘a = 3’，然后逗号之后的数值 `7` 不会再执行，它被忽略掉了。相当于 `(a = 1 + 2), 3 + 4`。

```js
var a = (1+2,2+3);
alert(a); // 5
```



---

## 4. 值的比较

> a>b,a<b
>
> a>=b;a<=b;
>
> `a==b;`  // 请注意双等号 `==` 表示相等性检查，而单等号 `a = b` 表示赋值。 
>
> a!=b;

```js
// boolean类型的比较
alert(2>1); // true;
alert(2==1); //false
alert(2 != 1); // true
```

```js
// 比较的值还可以赋给任意变量
let res = 5>2; // true
alert(res); // true;
```



> 字符串的比较
>
> 字符串的比较算法非常简单：
>
> 1. 首先比较两个字符串的首位字符大小。
> 2. 如果一方字符较大（或较小），则该字符串大于（或小于）另一个字符串。算法结束。
> 3. 否则，如果两个字符串的首位字符相等，则继续取出两个字符串各自的后一位字符进行比较。
> 4. 重复上述步骤进行比较，直到比较完成某字符串的所有字符为止。
> 5. 如果两个字符串的字符同时用完，那么则判定它们相等，否则未结束（还有未比较的字符）的字符串更大。
>
> 字符串比较对字母大小写是敏感的。大写的 `"A"` 并不等于小写的 `"a"`。哪一个更大呢？实际上小写的 `"a"` 更大。这是因为在 JavaScript 使用的内部编码表中（Unicode），小写字母的字符索引值更大。



```js
// 字符串的比较
alert( 'B'>'A'); // true
alert('BB'>'BA'); // true
```



> 不同类型的比较：
>
> 当对不同类型的值进行比较时，JavaScript 会首先将其转化为数字（number）再判定大小。
>
> 

```js
alert(true == 1); // true
alert(false == 0); // false
```

- 若直接比较两个值，其结果是相等的。

- 若把两个值转为布尔值，它们可能得出完全相反的结果，即一个是 `true`，一个是 `false`。

  - ```js
    let a = 0;
    alert( Boolean(a) ); // false
    
    let b = "0";
    alert( Boolean(b) ); // true
    
    alert(a == b); // true!
    ```



> 普通的相等性检查 `==` 存在一个问题，它不能区分出 `0` 和 `false`：
>
> ```
> alert( 0 == false ); // true
> ```
>
> **严格相等运算符 `===` 在进行比较时不会做任何的类型转换。**
>
> 如果 `a` 和 `b` 属于不同的数据类型，那么 `a === b` 不会做任何的类型转换而立刻返回 `false`。
>
> ```js
> alert(0 ==== false); // false
> ```
>
> **同样的，与“不相等”符号 `!=` 类似，“严格不相等”表示为 `!==`。**



> 对null和undefind 的比较

```js
//当使用严格相等 === 比较二者时
//它们不相等，因为它们属于不同的类型。
alert( null === undefined ); // false
```

```js
//当使用非严格相等 == 比较二者时
alert( null == undefined ); // true
```





```js
// 当使用数学式或其他比较方法 < > <= >= 时：
// null/undefined 会被转化为数字：null 被转化为 0，undefined 被转化为 NaN。
alert( null > 0 );  // (1) false
alert( null == 0 ); // (2) false
alert( null >= 0 ); // (3) true
```

这是因为相等性检查 `==` 和普通比较符 `> < >= <=` 的代码逻辑是相互独立的。进行值的比较时，`null` 会被转化为数字，因此它被转化为了 `0`。这就是为什么（3）中 `null >= 0` 返回值是 true，（1）中 `null > 0` 返回值是 false。



# 3.条件分支



## 1.基础常见条件分支

> ```js
> // if语句
> if(...);
> // boolean语句
>    if(0){} // 0是fasle
> 	if(1){} // 1是true
> 
> ```

> 三元运算符

```js
let result = condition ? value1 : value2;
```

> 多个 `？`的运算

```js
let age = prompt('age?', 18);

let message = (age < 3) ? 'Hi, baby!' :
  (age < 18) ? 'Hello!' :
  (age < 100) ? 'Greetings!' :
  'What an unusual age!';

alert( message );

// 上面写法的 if else 写法
if (age < 3) {
  message = 'Hi, baby!';
} else if (age < 18) {
  message = 'Hello!';
} else if (age < 100) {
  message = 'Greetings!';
} else {
  message = 'What an unusual age!';
}
```



## 2.逻辑运算符

>  或： `||`   与： `&&`  非： `！`

```js
alert(true || false);
alert(true && false);
alert(!true);
```

> 或的 运算逻辑
>
> ==一个或运算 `"||"` 的链，将返回第一个真值，如果不存在真值，就返回该链的最后一个值。==

```js
result = value1 || value2 || value3;
```

> 与 的运算逻辑
>
> ==与运算符返回第一个假值，如果没有假值就返回最后一个值。==

```js
result = value1 && value2 && value3;
```

> 非 的运算逻辑
>
> ==非运算符 `!` 的优先级在所有逻辑运算符里面最高，所以它总是在 `&&` 和 `||` 前执行。==

```js
alert(!true);
```

> 空值合并运算符
>
> 用来获取列表中第一个“已定义”的变量
>
> ==空值合并运算符 `??` 提供了一种简洁的方式获取列表中“已定义”的值。==

```js
//  ？？ 写法
x = a ?? b
// 和上面的作用是一样的
x = (a !== null && a !== undefined) ? a : b;
// 优先级  ?? 在大多数其他运算之后，但在 = 和 ? 之前进行运算。
let height = null;
let width = null;

// 重要：使用括号
let area = (height ?? 100) * (width ?? 50);
```



## 3.循环

> while循环
>
> 在 `while` 中的循环条件会被计算，计算结果会被转化为布尔值。

```js
let i = 0;
//循环体的单次执行叫作 一次迭代。
while (i < 3) { // 依次显示 0、1 和 2
  alert( i );
  i++;
}
```



> do..while 循环
>
> 这种形式的语法很少使用，除非你希望不管条件是否为真，循环体 **至少执行一次**。

```js
let i = 0;
do {
  alert( i );
  i++;
} while (i < 3);
```



> for 循环
>
> | begin          | `i = 0`    | 进入循环时执行一次。                             |
> | -------------- | ---------- | ------------------------------------------------ |
> | condition      | `i < 3`    | 在每次循环迭代之前检查，如果为 false，停止循环。 |
> | body（循环体） | `alert(i)` | 条件为真时，重复运行。                           |
> | step           | `i++`      | 在每次循环体迭代后执行。                         |
>
> ```js
> for (begin; condition; step) {
>   // ……循环体……
> }
> ```
>
> 在for循环中  `for` 循环的任何语句段都可以被省略。
>
> ```js
> let i = 0; // 我们已经声明了 i 并对它进行了赋值
> 
> for (; i < 3; i++) { // 不再需要 "begin" 语句段
>   alert( i ); // 0, 1, 2
> }
> ```
>
> ```js
> let i = 0;
> // 该循环与 while (i < 3) 等价。
> for (; i < 3;) {
>   alert( i++ );
> }
> ```
>
> ```js
> //删除所有内容，从而创建一个无限循环：
> for (;;) {
>   // 无限循环
> }
> ```
>
> 

```js
/*
开始运行
→ (如果 condition 成立 → 运行 body 然后运行 step)
→ (如果 condition 成立 → 运行 body 然后运行 step)
→ (如果 condition 成立 → 运行 body 然后运行 step)
→ ...
*/
for (let i = 0; i < 3; i++) { // 结果为 0、1、2
  alert(i);
}
```



> 跳出 循环 
>
> 使用 `break` 指令强制退出。

```js
 ......
 /*
 用户输入空行或取消输入，在 (*) 行的 break 指令会被激活。它立刻终止循环，将控制权传递给循环后的第一行，即，alert。
 */
while (true) {

  let value = +prompt("Enter a number", '');

  if (!value) break; // (*)

  sum += value;

}
```



> `continue` 指令是 `break` 的“轻量版”。它不会停掉整个循环。而是停止当前这一次迭代，并强制启动新一轮循环（如果条件允许的话）。

```js
for (let i = 0; i < 10; i++) {

  //如果为真，跳过循环体的剩余部分。
  if (i % 2 == 0) continue;

  alert(i); // 1，然后 3，5，7，9
}
```



> 使用 break/ continue 标签
>
> `break/continue` 支持循环前的标签。标签是 `break/continue` 跳出嵌套循环以转到外部的唯一方法。
>
> 只有在循环内部才能调用 `break/continue`，并且标签必须位于指令上方的某个位置。

```js
outer: for (let i = 0; i < 3; i++) {

  for (let j = 0; j < 3; j++) {

    let input = prompt(`Value at coords (${i},${j})`, '');

    // 如果是空字符串或被取消，则中断并跳出这两个循环。
    // break outer 向上寻找名为 outer 的标签并跳出当前循环。
    if (!input) break outer; // (*)

    // 用得到的值做些事……
  }
}
alert('Done!');
```

## 4. switch 语句

> `switch` 语句可以替代多个 `if` 判断。
>
> `switch` 语句有至少一个 `case` 代码块和一个可选的 `default` 代码块。
>
> 共享同一段代码的几个 `case` 分支可以被分为一组：
>
> ==强调一下，这里的相等是严格相等。被比较的值必须是相同的类型才能进行匹配。==

```js
switch(x) {
  case 'value1':  // if (x === 'value1')
    ...
    [break]

  case 'value2':  // if (x === 'value2')
    ...
    [break]

  default:
    ...
    [break]
}
```

- 比较 `x` 值与第一个 `case`（也就是 `value1`）是否严格相等，然后比较第二个 `case`（`value2`）以此类推。
- 如果相等，`switch` 语句就执行相应 `case` 下的代码块，直到遇到最靠近的 `break` 语句（或者直到 `switch` 语句末尾）。
- 如果没有符合的 case，则执行 `default` 代码块（如果 `default` 存在）。



---



# 4.函数



## 1.函数的基本声明

> 函数的说明： 函数是脚本的主要构建块。
>
> 局部变量：
>
> 外部变量： 函数对外部变量拥有全部的访问权限。函数也可以修改外部变量。
>
> 参数：
>
> 默认值：
>
> 命名：
>
> 为了使代码简洁易懂，建议在函数中主要使用局部变量和参数，而不是外部变量。

```js
function fun1(){
 ....// 函数体
}

let a = 123; //外部变量

function fun2(){
    let b = 1; //局部变量
}

// 如果未提供参数，那么其默认值则是 undefined。
function fun3(value1,value2){
    alert(value+value2);// 
}
```



## 2.函数表达式

> 除了上面的函数声明以外，下面这种
>
> ```js
> // 创建一个函数，并把它存进变量 sayHi
> function sayHi() {
>   alert( "Hello" );
> }
> ```
>
> 类似于 下面这种， 创建函数的语法称为 **函数表达式**。
>
> ```js
> // 函数被创建并像其他赋值一样，被明确地分配给了一个变量。不管函数是被怎样定义的，都只是一个存储在变量 sayHi 中的值。
> // 创建一个函数，并把它存进变量 sayHi
> let sayHi = function() {
>   alert( "Hello" );
> };
> ```

```js
function sayHi() {   // (1) 创建  ： (1) 行声明创建了函数，并把它放入到变量 sayHi。
  alert( "Hello" );
}

let func = sayHi;    // (2) 复制 ： (2) 行将 sayHi 复制到了变量 func。请注意：sayHi 后面没有括  号。如果有括号，func = sayHi() 会把 sayHi() 的调用结果写进func，而不是 sayHi 函数 本身。

func(); // Hello     // (3) 运行复制的值（正常运行）！ ：现在函数可以通过 sayHi() 和 func() 两种方式进行调用。
sayHi(); // Hello    //     这里也能运行
```



## 3.回调函数

> 思想是我们传递一个函数，并期望在稍后必要时将其“回调”。

```js
function a(q,fun1,fun2){
 ...
}

function fun1(){
...
}

function fun2(){
....
}
// 函数fun1 fun2 作为参数 传入 a中
a("hello",fun1,fun2);
```

使用匿名函数 对上面修改

> 这里直接在 `a(...)` 调用内进行函数声明。这两个函数没有名字，所以叫 **匿名函数**。这样的函数在 `a` 外无法访问（因为没有对它们分配变量），

```js
function a(q,f1,f2){
..
}

a("hello",
function(){ alert(1);},
function(){ alert(2); }
);
```

## 4.函数声明和函数表达式

- **函数表达式是在代码执行到达时被创建，并且仅从那一刻起可用。**
- 当 JavaScript **准备** 运行脚本时，首先会在脚本中寻找全局函数声明，并创建这些函数。我们可以将其视为“初始化阶段”。
- **当一个函数声明在一个代码块内时，它在该代码块内的任何位置都是可见的。但在代码块外不可见。**

```js
let age = prompt("What is your age?", 18);
// 使用 函数表达式 来 让 if外 也可以访问
let welcome;
if (age < 18) {

  welcome = function() {
    alert("Hello!");
  };
} else {
  welcome = function() {
    alert("Greetings!");
  };
}
welcome(); // 现在可以了
```

上面的简化

```js
let age = prompt("...");
let welcome=(age<18)? function(){ alert(...);}:function(){ alert(..);};

welcome();
```



## 5.箭头函数



> 这里创建了一个函数 `func`，它接受参数 `arg1..argN`，然后使用参数对右侧的 `expression` 求值并返回其结果
>
> 只有一个参数，还可以省略掉参数外的圆括号，使代码更短  `let double = n => n \* 2;`
>
> 如果没有参数，括号将是空的（但括号应该保留）： `let say = ()=> alert(1);`

```js
let fun1 = (arg1,arg2,...argN)=> expression;
```

上面这个相当于

```js
let fun1 = function(arg1,arg2,...argN){
 return expression;
}
```

例：

```js
let sum = (a, b) => a + b;

/* 这个箭头函数是下面这个函数的更短的版本：

let sum = function(a, b) {
  return a + b;
};
*/

alert( sum(1, 2) ); // 3
```



> 多行的箭头函数

```js
var sumVal = (a,b,c) =>{
	var  res = a+b+c;
	return res; // 如果我们使用了花括号，那么我们需要一个显式的 “return”
}
```

---

# 5.小总结

变量：

> 可以使用以下方式声明变量：
>
> - `let`
> - `const`（不变的，不能被改变）
> - `var`（旧式的，稍后会看到）
>
> 一个变量名可以由以下组成：
>
> - 字母和数字，但是第一个字符不能是数字。
> - 字符 `$` 和 `_` 是允许的，用法同字母。
> - 非拉丁字母和象形文字也是允许的，但通常不会使用。
>
> 变量是动态类型的，它们可以存储任何值：



