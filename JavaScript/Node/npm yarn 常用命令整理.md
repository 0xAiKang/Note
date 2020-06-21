## 前言
这篇笔记用来记录`npm/yarn`相关的命令。

查看npm当前镜像源
```
npm config get registry

// 设置npm镜像源为淘宝镜像
npm config set registry https://registry.npm.taobao.org 
```
查看yarn当前镜像源

```
yarn config get registry

// 设置yarn镜像源为淘宝镜像
yarn config set registry https://registry.npm.taobao.org
```

查看本文件夹所有的安装模块
```
npm list --depth=0
```

查看全局所有的安装模块
```
npm list --depth=0 -global
```