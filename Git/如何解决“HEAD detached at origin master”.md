# 前言
记录最近使用git时，产生的一个新问题。

> 问题描述：本地有多个远程仓库，当某一天需要向其中一个push 时，出现了如下提示：

```
HEAD detached at origin_game/maste
```

## HEAD
结论：上面的提示的意思是，你正在做的提交不属于分支。

这是一个官方的解释，为了更好的理解这句话，我们需要知道 `HEAD` 是什么。

`HEAD` 文件通常是一个符号引用（symbolic reference），指向目前所在的分支。 所谓符号引用，表示它是一个指向其他引用的指针。

通常`HEAD` 正常的状态应该是下面这样，如果你的不一样，那就需要修复分离的`HEAD` 。
```
$ cat .git/HEAD
ref: refs/heads/master
```

### 修复分离的HEAD
如果由于某种原因导致`HEAD`指向的不属于分支的提交，则需要创建一个临时分支，让该分支指向分离的`HEAD`当前指向的提交：

```
# 因为这次提交的远程仓库是 origin_game，所以需要切换到该分支上
# git checkout origin_game/master

# 在该分支上创建一个临时分支
$ git checkout -b temp_branch
```

这么做的目的是为了将我们`HEAD`重新连接到临时分支`temp_branch`上。

接下来可以通过一些命令来检查是否符合我们的预期。
```
# 比较远程分支与临时分支的历史提交
$ git log --graph --decorate --pretty=oneline --abbrev-commit master origin/master temp_branch

# 比较master 分支与临时分支的具体区别
$ git diff master temp_branch

# 比较远程分支与临时分支的具体区别
$ git diff origin/master temp_branch
```

将临时分支的HEAD 指向 master。
```
# 临时分支将用于确定master 分支的 HEAD 应该指向的位置。
$ git branch -f master temp_branch

# 切换回master 分支
$ git checkout master

# 上面两个命令可以缩写为
$ git checkout -B master temp_branch
```
此时的master 分支已经和`origin_game/master` 保持一致了，所以可以删除临时分支。

```
$ git branch -d temp_branch
```

最后在master分支上更新远程仓库。
```
# 合并功能分支
$ git merge fire_bug

$ git push origin_game master
```
自此，就可以成功修复了分离的HEAD。

需要注意：
* 不能在`origin/master`分支上进行提交，只能在`master`分支上进行提交。

### 参考链接
* [head detached from origin-master](https://www.loekvandenouweland.com/content/head-detached-from-origin-master.html)
* [How can I reconcile detached HEAD with master/origin?](https://stackoverflow.com/questions/5772192/how-can-i-reconcile-detached-head-with-master-origin)