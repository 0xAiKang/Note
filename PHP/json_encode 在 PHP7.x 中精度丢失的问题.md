## 前言
前段时间把项目所使用的`PHP` 升级了一下，从`5.6`升到`7.3`，起初我还没觉得有什么问题，直到升级一段时间之后，问题才暴露出来。

> json_encode 这个函数在PHP 7.x 中会存在精度丢失的现象。


```
<?php
$num = 0.1 + 0.7;
echo $num;  // 0.8
echo json_encode($num); // 0.7999999999999999
```

经过一番查找，总结了两种解决方案，不一定每一种都对你有用。
1. serialize_precision
2. 将`float`转`string`

### serialize_precision
网上有一种说法是，将`php.ini`中的`serialize_precision`值设置成`小于17`，我自己测试了几次，感觉并不能解决问题。

所以这种方式并不推荐。

### 将`float`转`string`
既然`json_encode`只会有的存在浮点数时，才可能出现精度丢失的问题，所以如果把所有浮点数都转换为其他数据类型呢？

第二个解决方案就是基于这个思路去实现的。

下面简单的封装成了一个函数：
```
/**
 * @param $data 需要处理的数据
 * @return array|string
 */
function fix_number_precision($data)
{
    if(is_array($data)){
        foreach ($data as $key => $value) {
            $data[$key] = fix_number_precision($value);
        }
        return $data;
    }

    if(is_float($data)){
        return (string)$data;
    }

    return $data;
}
```

实例：
```
$num = 0.1 + 0.7;
$arr = [
  0 => 0.8,
  1 => 1.2,
  3 => 100,
];
echo json_encode(fix_number_precision($num)); 
echo json_encode(fix_number_precision($arr)); 
```

输出结果：
```
"0.8"

 {
  "0": "0.8",
  "1": "1.2",
  "3":100
}
```

> 貌似PHP 版本 >= 7.1 均会出现此问题。

### 参考链接
* [php的json_encode()之后float类型丢失精度](https://www.cnblogs.com/mentalidade/p/9924125.html)
* [php 7.1 使用 json_encode 函数造成浮点类型数据出现精度问题](https://blog.csdn.net/qq_42451060/article/details/80993664)