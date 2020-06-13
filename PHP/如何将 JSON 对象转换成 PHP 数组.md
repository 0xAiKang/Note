# 前言
最近的项目需求一直要将JSON 对象转换成PHP 的数组，这里用一篇笔记来记录，遇到的一些问题以加深印象。

在介绍如何将JSON 字符串转传为PHP 数组之前，先来复习一下什么是JSON。
## JSON

JSON格式就是一种表示一系列的“值”的方法，这些值包含在**数组或对象**之中，是它们的成员。对于这一系列的“值”，有如下几点格式规定：

1. 数组或对象的每个成员的值，可以是简单值，也可以是复合值。
2. 简单值分为四种：字符串、数值（必须以十进制表示）、布尔值和null（NaN, Infinity, -Infinity和undefined都会被转为null）。
3. 复合值分为两种：**符合JSON格式的对象**和**符合JSON格式的数组**。
4. 数组或对象最后一个成员的后面，不能加逗号。
5. 数组或对象之中的字符串必须使用双引号，不能使用单引号。
6. 对象的成员名称必须使用双引号。

通俗一点讲`JSON` 就是一种数据结构，就是一串字符串，只不过元素会通过特定的符号标注。

* `{}`：大括号表示对象
* `[]`：中括号表示数组
* `""`：双引号内是属性或值

> 需要注意的是，空数组和空对象都是合格的JSON值，null本身也是一个合格的JSON值。

以下是合格的JSON值。
```
# 这是一个 JSON 数组
["one", "two", "three"]

# 这是一个JSON 对象
{
    "name": "boo",
    "gender": "man",
    "age": 25
}

# 这是一个包含两个对象的JSON 数组
[   
    {
        "name": "boo",
        "gender": "men",
        "age": 25
    },
    {
        "name": "max",
        "gender": "men",,
        "age": 29
    }
]

# 这是一个包含数组的JSON 对象
{
    "name": ["Michael","Jerry"]
}
```

以下是不合格的JSON值：

````
{ name: "张三", 'age': 32 }  // 属性名必须使用双引号

[32, 64, 128, 0xFFF] // 不能使用十六进制值

{ "name": "张三", age: undefined } // 不能使用undefined

{ "name": "张三",
  "birthday": new Date('Fri, 26 Aug 2011 07:13:10 GMT'),
  "getName": function() {
      return this.name;
  }
} // 不能使用函数和日期对象
````

在熟悉了几种常见的JSON 字符串之后，在来看一下如何解析JSON 字符串。

### PHP

#### json_decode

`json_decode`函数的作用是将 JSON  对象转换为 PHP对象、或者 PHP 数组。

##### JSON 对象转换为PHP 对象

```
<?php
// 将 JSON 对象字符串转换成 PHP 对象
$jsonObj = '{"name": "boo"}';
$obj = json_decode($jsonObj);
var_dump($obj->{"name"}); 

object(stdClass)[1]
      public 'name' => string 'boo' (length=3)

// 将 JSON 数组对象转换成 PHP 数组对象
$jsonObj2 = '[{"name": "boo"}]';
$obj2 = json_decode($jsonObj2);
var_dump($obj[0]->{"name"});

array (size=1)
  0 => 
    object(stdClass)[1]
      public 'name' => string 'boo' (length=3)
      
```

##### JSON 对象转换为PHP 数组

```
<?php
$jsonObj = '{"name": "boo", "age":25, "gender":"man"}';

$arr = json_decode($jsonObj, true);
var_dump($arr); 

array(3) {
  'name' =>
  string(3) "boo"
  'age' =>
  int(25)
  'gender' =>
  string(3) "man"
}
```

需要注意几个容易出错的细节：
```
<?php
// 大括号外需要使用单引号
$bad_json = "{ 'bar': 'baz' }";
json_decode($bad_json);    // null

// 属性需要使用双引号引起来
$bad_json = '{ bar: "baz" }';
json_decode($bad_json);    // null

// 不允许尾随逗号
$bad_json = '{ bar: "baz", }';
json_decode($bad_json);    // null

```

#### json_encode

`json_encode`这个函数的作用是将 PHP 数组转换为 JSON 对象或者 JSON 数组。

```
<?php
$b = array();

echo "空数组作为数组输出: ", json_encode($b), "\n"; 
//空数组作为数组输出：[]
echo "空数组作为对象输出: ", json_encode($b, JSON_FORCE_OBJECT), "\n\n"; 
//空数组作为对象输出：{}

$c = array(array(1,2,3));

echo "多维数组作为数组输出: ", json_encode($c), "\n";
//多维数组作为数组输出：[[1,2,3]]
echo "多维数组作为对象输出: ", json_encode($c, JSON_FORCE_OBJECT), "\n\n";      
//多维数组作为对象输出：{"0":{"0":1,"1":2,"2":3}}

$d = array('foo' => 'bar', 'baz' => 'long');

echo "关联数组只能作为对象输出: ", json_encode($d), "\n"; 
//关联数组只能作为对象输出：{"foo":"bar","baz":"long"}
echo "关联数组只能作为对象输出: ", json_encode($d, JSON_FORCE_OBJECT), "\n\n";      
//关联数组只能作为对象输出：{"foo":"bar","baz":"long"}

$arr = array(
  "name" => "boo",
  "gender" => "men",
  "age" => 22
);
var_dump(json_encode($arr));

string '{"name":"boo","gender":"men","age":22}' (length=35)
```
> 什么时候才需要为 JSON 对象或者 JSON 数组加上单引号？

当你需要去定义一个 JSON 字面量时，才需要这么做。所以如果想要在数据库存储一个 JSON 对象，那么它的格式应该是：

````
{"name": "boo", "age": 25, "gender": "man"}
````

而不是

```
'{"name": "boo", "age": 25, "gender": "man"}'
```

### JavaScript

#### JSON.stringify

`JSON.stringify` 方法用于将一个JSON 对象或者 JSON 数组 转为 JSON 字符串。

##### JSON 对象/数组转 JSON 字符串

```
JSON.stringify("abc") // '"abc"'
JSON.stringify(1) // "1"
JSON.stringify(false) // "false"
JSON.stringify([]) // "[]"
JSON.stringify({}) // "{}"

JSON.stringify([1, "false", false])
// '[1,"false",false]'

JSON.stringify({ name: "张三" })
// '{"name":"张三"}'
```

#### JSON.parse

`JSON.parse`方法用于将JSON 字符串转化成对象。

##### JSON 字符串转 JSON 对象

```
JSON.parse('{}') // {}
JSON.parse('true') // true
JSON.parse('"foo"') // "foo"
JSON.parse('[1, 5, "false"]') // [1, 5, "false"]
JSON.parse('null') // null

var o = JSON.parse('{"name":"张三"}');
o.name // 张三
```

如果传入的字符串不是有效的JSON格式，JSON.parse方法将报错。

```
JSON.parse("'String'");
Uncaught SyntaxError: Unexpected token ' in JSON at position 0

// 这样就不会报错
JSON.parse('"String"');	
```