# 基础类型

## 字符串

### 单引号

```php
$name = 'viw-07';

//输出
echo 'viw-07';
```

### 双引号



### 单引号和双引号的区别

> 单引号会被当纯字符串输出，而双引号不会

## 数组

```php

//创建一个数组
$resultData = array();

//声明一个数组 其中a的值是 aaa，bbb的值是bbb
$resData = [
    "a" => "aaa",
    "bbb" => "bbb",
];

//给数组中的 resData赋值
$resultData['resData']  = $resData;


//把数组中的值取出来 重新赋给新定义的变量
$time = $arr['time']

```



## 常量

```php

//字符串
const CONSTANT = 'A';
//数字
const TIME = 10;

```

# 对象


# 流程控制

## 判断

### 函数判断

```php

//判断字符串是否空
if(empty($arr['status'])){
    // do something
}

//判断两个字符串是否相等
if($amount==$resAmount){
    // do something
}

//判断数组中的值
if($arr['status']!=$data['status']){
    // do something 
}

//判断字符串是否有值并且不为null
if(!isSet($name)){
    // do something
}


```

### == 和 ====

php中有类型转换的概念

如 ==  和 === ，其中`==` 是只比较数值 相同返回true，`===`是强比较，类型也要相等才能返回true


## 三元运算符

```php

$isAuto==1?'yes':'no';

```




## for


```php
$name = [ 'a','b','c'];
foreach ($name => $value) {
    # code...
    if($value == 'a'){
        // do something
    }
}

$age = ['a'=>1,'b'=>2];
foreach ($age as $key => $value) {
    # code...
    if($key=='a'&&$value==1){
        // do something
    }
}

```


