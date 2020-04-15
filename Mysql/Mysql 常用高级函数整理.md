# 前言
最近不少接触SQL语句，其实部分逻辑可以不用在业务代码中去实现，而是直接通过`Mysql`的高级函数来实现。

这篇笔记用来整理下`Msyql` 的常用高级函数。

## CASE
CASE -  通过条件并在满足第一个条件时返回值，一旦条件为真，它将停止读取并返回结果。

如果没有条件成立，它将返回ELSE子句中的值。

如果没有ELSE部分且没有条件为真，则返回NULL。

```
//句法
CASE
    WHEN condition1 THEN result1
    WHEN condition2 THEN result2
    WHEN conditionN THEN resultN
    ELSE result
END CASE;

// 实例
select name, gender, age
case 
    when age < 18 and gender = boy then
        boy
    when age > 18 then gender = boy then 
        men
    when age < 18 and gender = girl then 
        girl
    else 
        women
end gender 
from peoper;    
```

## Concat
Concat 函数可以将两个或多个表达式连在一起。可以连接一个或者多个字符串。
```
// 句法
CONCAT(expression1, expression2, expression3,...)

// 实例1
SELECT CONCAT("SQL ", "Tutorial ", "is ", "fun!") AS ConcatenatedString;

// 输出
ConcatenatedString
SQL Tutorial is fun!

//实例2
select concat('my', 's', 'ql');
mysql
```

## Cast
Cast函数 - 这个函数我最近经常用到，它的作用是将任何类型的值转换为指定的数据类型。

其中类型包括：
* char：固定长度的字符串
* signed：有符号的64 位整数
* unsigned：无符号的64 位整数
* date：日期型
* time：时间型
* datetime：日期和时间型
* decimal：浮点型
```
// 句法
CAST(value AS datatype)

//实例
select cast(150 as char);       
-> 150

//实例2
select 0+cast(150.220 as char);
-> 150.22
//上面这种用法暂时还不明白是什么原理，却能很好的满足我需要的效果。等之后明白了，再补充。
```

## Round
Round 函数 - 对值进行四舍五入。
```
//实例
select round(99.99);  // 100

select round(99.56);  // 100

select round(99.46);  // 99

//该函数可以添加参数，会按照参数的值保留到其小数点后位数。
select round(99.435, 2);   //99.44

select round(99.002, 2);   //99.00
```

## FROM_UNIXTIME
from_unixtime 函数 - 将unix 时间戳转换成常见的时间格式。
```
mysql> SELECT FROM_UNIXTIME(1447430881);
-> '2015-11-13 00:08:01'

mysql> SELECT FROM_UNIXTIME(1447430881, "%Y-%m-%d %H:%m:%s");
-> '2015-11-14 00:11:01'
```

### 参考链接
* [Mysql 的Case When 语句使用说明](https://blog.csdn.net/helloxiaozhe/article/details/78124138)
* [Mysql 教程](https://www.mysqltutorial.org/mysql-cast/)