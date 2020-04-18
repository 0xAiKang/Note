# 前言
最近在学习`Swoole`，顺手整理一下`PHP` 中的四种设置回调函数的方式。

### 匿名函数
```
<?php
$server->on("request", function($request, $respone){
  echo "Http Server";
});
```

### 类静态函数
```
class A{
  static function onConnect($server, $fd){
    echo "UDP Server";
  }
}
$server->on("connect", "A::onConnect");
$server->on("conncet", ["A", "onConnect"]);
```

### 函数
```
$server->on("connect", "callBack");

function callBack($server, $fd){
  echo "Tcp Server";
}
```

### 对象方法
```
# 情景一
Class A{
  public function __construct(){
    $this->server->on("open", [$this, "onOpen"]);
  }
  
  public function onOpen($server, $request){
    echo "WebSocket Server";
  }

}
# 情景二
Class A{
  function onOpen($request, $respone){
    echo "WebSocket Server";
  }
}
$obj = new A();
$server->on("open", [$obj, "onOpen"]);
```