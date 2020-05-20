## 前言
`wget` 是一个命令行的下载工具，对于经常使用`Linux`的用户来说，真是再熟悉不过了。下面总结了一些实用的`wget`使用技巧，可能会让你更加高效地使用 `wget`。

### 重命名
最常见的使用方式：
```
$ wget http://example.com/filename.txt
```

wget默认会以最后一个符合 `/` 的后面的字符来对下载文件命名，对于动态链接的下载通常文件名会不正确。

如果希望对这个下载的文件进行重命名，我们可以使用参数 `-O` 来指定一个文件名：
```
$ wget -O file.zip http://example.com/filename.txt
```
### 后台下载
当需要下载比较大的文件时，使用参数 `-b` 可以隐藏在后台进行下载：

```
$ wget -b http://wppkg.baidupcs.com/issue/netdisk/MACguanjia/BaiduNetdisk_mac_3.2.0.9.dmg
```
然后可以使用以下命令查看当前的进度：

```
$ tail -f wget-log
```

### 下载目录
这条命令可以下载 http://example.com 网站上 packages 目录中的所有文件。

参数说明：
* `-r`：下载目录
* `-np`：不遍历父目录
* `-nd`：不在本机重新创建目录结构

```
$ wget -r -np -nd http://example.com/packages/
```

与上一条命令相似，但多加了一个 `--accept=iso` 选项，这指示 wget 仅下载 i386 目录中所有扩展名为 iso 的文件。你也可以指定多个扩展名，只需用逗号分隔即可。
```
$ wget -r -np -nd --accept=iso http://example.com/centos-5/i386/
```

### 批量下载
此命令常用于批量下载的情形，把所有需要下载文件的地址放到 filename.txt 中，然后 wget 就会自动为你下载所有文件了。

```
$ wget -i filename.txt
```

### 断点续传
通常我们在下载大文件时，为了防止中途因为网络不稳定等因素所引起的下载失败，可以使用 `-c` 参数，作为断点续传。

好处是：如果当时下载失败了，之后再次下载该文件时，会继续上一次的下载，而不用重头下载了。
```
$ wget -c http://example.com/really-big-file.iso
```

### 其他

该命令可用来镜像一个网站，wget 将对链接进行转换。如果网站中的图像是放在另外的站点，那么可以使用 -H 选项

```
$ wget -m -k (-H) http://www.example.com/
```

### 参考链接
* [wget 使用技巧](https://linuxtoy.org/archives/wget-tips.html)