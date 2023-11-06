
# service

## 通过Yii框架发起 http请求



> http调用函数

> 先定义一个url的参数在公共的param.php中

```php
    'check' => '/api/check',
```

> 调用函数

```php

class TestClass{

public function check($A,$B){
    $params = [
        'A' => $A,
        'B' => $B
    ];
    return $this->call($this->getUri('check',$params));
}

}
```

> 实例化并调用

```php

//实例化一个TestClass类 并且调用它的check方法，有两个参数，分别是把 arr数组中的 A的值 和 B的值传入
$res = TestClass::getInstance()->check($arr['A'],$arr['B']);

//取出返回结果中的code值 并且和 定义的常量类Constant中的 OK字段的属性值做比较
if(res['code']!=Constant::OK){
    // do something 
}

```












