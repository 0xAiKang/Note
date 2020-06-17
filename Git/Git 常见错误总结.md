# 前言
这篇笔记的目的是用来记录自己使用`Git`的过程中遇到的各种问题，整理在一起，方便日后查阅。

## git push 被拒绝
问题描述：`git push` 时，得到`Permission denied (publickey)`这样的提示。

完整错误内容如下：
```
git@gitlab.com: Permission denied (publickey).
fatal: Could not read from remote repository.
```

### 错误原因
通常遇到这个错误有两个原因：
1. `ssh config` 配置异常
2. 远程仓库不存在

而大多数时候都是第一种情况。

### 解决方案
在已经有`id_rsa`和`id_rsa.pub`文件的前提下，请检查`~/.ssh/config` 文件下的配置。

并保证`id_rsa` 文件中的密钥已经上传到`gitlab`或者`github`这类源代码托管服务平台。
```
Host gitlab.com
    User username                      // 用户名
    HostName gitlab.com                // 远程仓库的地址
    IdentityFile ~/.ssh/id_rsa_gitlab  // id_rsa.pub 文件所在的目录
    PreferredAuthentications publickey
```

## 域名没有被解析
问题描述：
昨天都还能正常提交，晚上提交时突然报错了。

完整错误内容如下：
```
ssh: Could not resolve hostname gitlab.com: nodename nor servname provided, or not known  
fatal: Could not read from remote repository.
```

### 错误原因
`gitlab.com`没有被主机解析。

### 解决方案
```
$ ping gitlab.com
192.30.253.112

// 将得到的IP，添加至 hosts 文件
$ sudo vim /etc/hosts
192.30.253.112 gitlab.com
```
不一定需要重启电脑，但我是通过重启电脑，这个问题才得到解决。

## fatal: refusing to merge unrelated histories

完整错误信息如下：
```
$ git pull origin master
From gitlab.com:options/bustabit
 * branch            master     -> FETCH_HEAD
fatal: refusing to merge unrelated histories
```

这个异常信息其实已经非常明确了，解决办法也很简单。

```
$ git pull origin master --allow-unrelated-histories
```
在基础命令之上，添加一个 `--allow-unrelated-histories` 参数，但是需要注意的是，git 出于安全考虑，才没有拒绝了合并，如果执意要合并的话，需要做好解决合并冲突的准备。

### 参考链接
* [Git使用过程遇到问题及解决方案](https://xiaogliu.github.io/2017/02/12/gather-git-question/)