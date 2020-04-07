# 前言
今天看到一个问题，感觉挺有趣的，于是便有了这篇笔记。

> 问题描述：JavaScript 中的 (function(){})() 是什么？它等同于 document.onload 吗？

语法：
```
(function(){

})();
```

## IIFE
Immediately Invoked Function Expression，简称 [IIFE](http://benalman.com/news/2010/11/immediately-invoked-function-expression/)。它是JavaScript 中的立即调用函数表达式。

注意，大多数人会将这个功能称为“自我调用”，这是不准确的，而应该称之为“立即调用函数表达式”。

它与任何事件（例如`document.onload`）的事件处理程序无关。

### 立即调用
为了方便理解什么是立即执行的匿名函数，可以看下面这个实例。

就像将其分配给变量，然后在没有变量的情况下立即使用它：
```
var foo = function(){

};
foo();
```
因为函数表达式后面紧跟 `()`会自动调用，但是如果把函数表达式赋给一个变量则不需要添加括号也可以直接调用，下方的代码中，函数表达式的主体部分会执行，并且会返回一个字符串给`foo`。

```
var foo = function(){
  document.write("我是函数输出的内容");
  return "我是函数返回的内容";
}();
document.write(foo);
```

该函数在声明的同时会立即调用一次，输出语句一：`我是函数输出的内容`，然后每次执行`document.write(foo);`，都会返回语句二的内容：`我是函数返回的内容`。

**需要注意：**
该函数在创建后立即执行，而不是在解析后执行。在执行脚本块中的任何代码之前，都将对其进行分析。
同样，解析代码并不会意味着它已被执行，例如，如果在`IIFE`函数内部，那么直到调用该函数后，它才会被执行。

在不同的语法环境中，该表达式可以重写成不同的形式。
### ES6 
```
(()=>{})()
```

### jQuery
```
$(function(){

});
```

### DOM
```
$(document).ready(function(){

});
```

### 参考链接
* [What is the (function() { } )() construct in JavaScript?](https://stackoverflow.com/questions/8228281/what-is-the-function-construct-in-javascript)