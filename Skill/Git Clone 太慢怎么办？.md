## 前言
今天在使用git 时，需要克隆`Bitbucket`的一个仓库，于是像往常一样打开了`iTerm`，便放在一边了。
直到一个小时后，我才想起来，想着应该克隆完了，打开才发现百分之一都没下载完。

![git clone 失败](https://i.loli.net/2020/03/15/hV53lgLruRd8XCW.jpg)

强大的长城技术对GitHub、Bitbucket 这类源代码托管服务平台网开一面，并没有像Google、FaceBook那样直接一刀切，但是它做了严格的限速，这种折磨简直比无法访问更难受。

上图中`git clone`的速度从来没有超过 `10k/s`，这也就意味着一个 `100M` 的项目，需要近三个小时才能下载完，而且由于网络的不稳定性，下载过程中偶尔会出现断开连接的情况，由于`git clone` 不支持端点续传，这就会导致前几个小时的下载量完全浪费掉了，只能重新开始下载。

这篇文章主要用来介绍几种方式可以快速的克隆远程仓库。

### 浅复制
`git clone`默认会下载项目的完整历史版本，如果你只关心代码，而不关心历史信息，那么可以使用 git 的浅复制功能：

```
$ git clone --depth=1 https://github.com/bcit-ci/CodeIgniter.git
```
`--depth=1` 表示只下载最近一次的版本，使用浅复制可以大大减少下载的数据量，例如，CodeIgniter 项目完整下载有近 100MiB ，而使用浅复制只有 5MiB 多，这样即使在恶劣的网络环境下，也可以快速的获得代码。

如果之后又想获取完整历史信息，可以使用下面的命令：
```
$ git fetch --unshallow
```
或者，如果你只想下载最新的代码，你也可以直接从远程仓库下载打包好的`zip`文件，这会比浅复制更快，因为它只包含了最新的代码文件，而且`zip`是压缩文件。但是很显然，使用浅复制会灵活一些。

### GUI 工具
如果你有幸正在使用代理，懂得如何科学上网的话，那么访问`GitHub`、`Bitbucket`对你来说应该不在话下。

从源代码托管服务平台下载项目最简单的方法就是使用一款图形化界面（`GUI`）的Git工具。

使用`GUI`工具方便之处就在于，可以在设置中直接配置是否使用代理。或者直接将代理配置尾系统代理。

### http/https proxy
如果你跟我一样，更喜欢使用原生的`git`命令，喜欢使用在命令行下操作的那种感觉，那么你也可以在命令行下直接配置代理。

这里也有两种方式，根据实际情况自行选择。
#### http
```
$ git config --global http.proxy http://127.0.0.1:1087
$ git config --global https.proxy https://127.0.0.1:1087
```

或者直接编辑`~/.gitconifg`文件
```
# vim ~/.gitconfig

[http]
	proxy = http://127.0.0.1:1087
[https]
	proxy = https://127.0.0.1:1087
```

#### socks5
```
$ git config --global http.proxy socks5://127.0.0.1:1086
$ git config --global https.proxy socks5://127.0.0.1:1086
```

其中，`1087`、`1086`分别是你本地机器的 `http`、`socks5`代理的端口号。 

另外，如果想取消设置，可以输入以下命令：
```
$ git config --global --unset http.proxy
$ git conifg --global --unset https.proxy
```

配置完成后，重新 `clone`一遍，可以看到速度得到了极大的提升。

![git clone 成功](https://i.loli.net/2020/03/15/Xfw1r86iFgSGsxe.jpg)

> 注意⚠️

上面这种配置方式仅适用于 `https`协议，如果你在`clone`时选择`ssh`协议，那么速度仍然会很慢。

### 参考链接
[Git Clone 太慢怎么办？](https://www.aneasystone.com/archives/2015/08/git-clone-faster.html)

