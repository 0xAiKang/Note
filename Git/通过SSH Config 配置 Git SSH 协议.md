# 前言
今天在Mac 上搭建开发环境，在使用`SSH`协议克隆代码时遇到了一点点问题，这也是之前一直存在疑惑的一个点，
所以总结一下。

## 认识 SSH Config
SSH 是连接远程主机最常用的方式，尽管连接到单个主机的基本操作非常直接，但当你开始使用大量的远程系统时，这就会成为笨重和复杂的任务。

幸运的是，`OpenSSH` 允许我们提供自定义的客户端连接选项。这些选项可以被存储到一个配置文件中，这个配置文件可以用来定义每个主机的配置。这有助于保持每个主机的连接选项更好的独立和组织，也让你在需要连接时避免在命令行中写繁琐的选项。

本地系统的每个用户都可以维护一个客户端的 SSH 配置文件，这个配置文件可以包含你在命令行中使用 ssh 时参数，也可以存储公共连接选项并在连接时自动处理。

### 文件位置
配置文件的文件名为 `config` ，位于用户目录下的 `.ssh` 文件夹下。

通常，该文件不是默认创建的，因此你可能要自己创建它。

### 文件结构
配置文件通过 Host 来组织，每一个 Host 定义项为匹配的主机定义连接选项。通配符可以用，为了让选项有更大的范围。

```
Host test
        User root
        HostName 39.100.139.235
```
如果现在需要连接该服务器，那么直接使用`ssh test`，等同于`root@39.100.139.235`。

## 配置Git SSH
在了解了什么是`SSH Config`之后，回归正题，如何使用`SSH Config`配置Git SSH。

首先需要在本地生成好密钥对，将**公钥**上传至 你的代码托管平台（GitHub、GitLab等）。

> 什么？你还不知道怎么生成ssh key？

赶紧[补一下课](https://github.com/0xAiKang/Note/blob/master/Git/Git%20SSH%20Key%E9%85%8D%E7%BD%AE.md)。

然后按照如下格式配置`SSH Config`
```
Host github.com
    User yourName
    HostName github.com
    IdentityFile ~/.ssh/id_rsa
    PreferredAuthentications publickey
```
参数说明
* `Host`：服务器地址的别名
* `User`：需要连接的用户名
* `HostName`：服务器地址
* `IdentityFile`：身份验证文件
* `PreferredAuthentications`：验证方式

> Host 请保持和HostName 一致。

通常按照以上格式配置好之后，就可以正常使用Git 的SSH 协议了。

如果你的运气没那么好，报错了，没关系，可以通过以下方式来解决。

### SSH 调试输出
SSH 是可以测试连接和调试的，有两种方式：

1. 指定User 及 HostName 测试连接
```
$ ssh -T git@github.com
Hi 0xAiKang! You've successfully authenticated, but GitHub does not provide shell access.
```

2. 开启调试模式连接，同时输出连接日志。
```
# 日志等级一级
$ ssh -v git@github.com

# 日志等级二级
$ ssh -vv git@github.com

# 日志等级三级
$ ssh -vvv git@github.com
```
参数说明：
* `-vvv`表示输出详细日志，启用额外的（2级和3级）详细信息，

通常通过查看输出信息，是可以发现导致异常的原因。

### 参考链接
* [`.ssh/config`文件解释算法以及配置规则](https://www.cnblogs.com/xjshi/p/9146296.html)