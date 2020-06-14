# 前言
虽然每天都在使用`Git`，但是有些命令太久不使用，还是会忘记，所以这篇笔记的目的就是整理那些`Git` 常用命令。

## 基础配置
Git的设置文件为`.gitconfig`，它可以在用户主目录下（全局配置），也可以在项目目录下（项目配置）。
```
# 查看全局配置列表
$ git config --global --list
# 查看局部配置列表
$ git config --local --list

# 设置全局用户名/邮箱
$ git config --global user.name "yourName"
$ git config --global user.email "example@example.com"

# 设置本地当前工作区仓库用户名/邮箱
$ git config --local user.name "yourName"
$ git config --local user.email "example@example.com"

# 将默认文本编辑器设置为 emacs/vim
$ git config --global core.editor emacs/vim

# 编辑当前仓库的配置文件
$ git config -e  # 等价与 vim .git/config
# 编辑全局配置文件
$ git config --global -e
```

### 命令别名配置

```
# 添加别名 git st = git status
$ git config --global alias.st status

# 删除 st 别名
$ git config --global --unset alias.st

# 执行外部命令, 只要在前面加 ! 即可
$ git config --global alias.st '!echo hello';
```

### 代理配置
如果想知道关于`Git`配置代理的更多信息，可以查阅[这篇笔记](https://github.com/0xAiKang/Note/blob/master/Skill/Git%20Clone%20%E5%A4%AA%E6%85%A2%E6%80%8E%E4%B9%88%E5%8A%9E%EF%BC%9F.md)。

```
# 配置HTTP/HTTPS 代理
$ git config --global https.proxy  http://127.0.0.1:1087
$ git config --global http.proxy  http://127.0.0.1:1087

# 查看
$ git config --global --get http.proxy
$ git config --global --get https.proxy

# 取消代理
$ git config --global --unset http.proxy
$ git config --global --unset https.proxy
```

### 生成SSHKey
关于如何配置`ssh config` 可以查阅[这篇笔记](https://github.com/0xAiKang/Note/blob/master/Git/%E9%80%9A%E8%BF%87SSH%20Config%20%E9%85%8D%E7%BD%AE%20Git%20SSH%20%E5%8D%8F%E8%AE%AE.md)。

```
# 将ssh key生成在默认下，也就是`~/.ssh/id_rsa`。
$ ssh-keygen -t rsa -C "youremail"

# 将ssh key生成在指定路径下的指定文件名中
$ ssh-keygen -t rsa  -f ~/.ssh/id_rsa_bitbucket -C "youremail"
```

## 准备工作

```
# 在当前目录新建一个Git代码库
$ git init

# 新建一个目录，将其初始化为Git代码库
$ git init [project-name]

# 下载一个项目和它的整个代码历史
$ git clone [url] [project-name]

# 浅克隆, 历史记录只克隆最后一条, 减少克隆时间
$ git clone --depth=1 https://github.com/0xAiKang/Note.git
```

## 基础操作
基础操作中的命令都是日常使用频率非常高的。

### 文件状态

```
# 查看工作区状态
$ git status

# 列出没有被 .gitignore 忽略的文件列表
$ git status --ignored

# 列出没有被 .gitignore 忽略的文件列表
$ git ls-files
```
### 文件操作

```
# 暂存所有
$ git add -A

# 暂存某个文件
$ git add ./README.md

# 添加当前目录的所有文件到暂存区    
$ git add .

# 暂存一系列文件
$ git add 1.txt 2.txt ...

# 从暂存区中删除文件（git add 的反向操作）
$ git rm [file] 

# 暂存区、工作区一起删除
$ git rm -f [file]

# 停止追踪指定文件，但该文件会保留在工作区
$ git rm --cached [file]
```

### 查看文件改动

```
# 查看所有文件改动
$ git diff

# 查看具体文件的改动
$ git diff README.md

# 查看指定 commit-id 改动内容
$ git diff [commit-id]

# 对比工作区和版本库里的最新版本有什么区别
$ git diff HEAD --[file-name]

# 查看某个文件的历史修改记录
$ git log README.md
$ git show [commit-id] README.md
```

### 撤销与回滚

```
# 恢复暂存区的指定文件到工作区
$ git checkout [file]

# 恢复暂存区的所有文件到工作区
$ git checkout .

# 重置暂存区与工作区，与上一次commit保持一致
$ git reset --hard

# 回滚上一个版本
$ git reset --hard HEAD^

# 回退到指定版本（会重置暂存区与工作区）
$ git reset --hard [commit-id]

# 回退到指定版本（不会重置暂存区与工作区，会回到该版本的暂存状态）
$ git reset --soft [commit-id]
```

### 提交

```
# 提交暂存区到本地仓库
$ git commit -m [message]

# 提交暂存区的指定文件到本地仓库
git commit README.md -m [message]

# 提交并显示diff变化
git commit -v

# 重写上一次的提交
# 如果代码没有任何新变化，则用来改写上一次commit的提交信息
$ git commit --amend -m [message]
```

### 日志
```
# 查看完整历史提交记录
$ git log

# 查看前n 条记录
$ git log -n

# 以图形方式查看完整历史提交记录
$ git log --graph --pretty=oneline --abbrev-commit

# 通过commit log 进行搜索
$ git log -i --grep="fire bug"

# 列出提交者贡献数量, 只会打印作者和贡献数量
$ git shortlog -sn

# 以提交贡献数量排序并打印出信息
$ git shortlog -n

# 采用邮箱格式化的方式进行查看贡献度
$ git shortlog -e
```
    
### 分支
```
# 查看本地分支
git branch

# 查看所有分支
git branch -a

# 查看本地分支所关联的远程分支
git branch -vv

# 查看本地 master 分支创建时间
git reflog show --date=iso master

# 新建一个分支，但依然停留在当前分支
$ git branch [branch-name]

# 新建一个分支，并切换到该分支
$ git checkout -b [branch]

# 新建一个分支，指向指定commit
$ git branch [branch] [commit-id]

# 新建一个分支，与指定的远程分支建立追踪关系
$ git branch --track [branch] [remote-branch]

# 切换到指定分支，并更新工作区
$ git checkout [branch-name]

# 建立追踪关系，在现有分支与指定的远程分支之间
$ git branch --set-upstream [branch] [remote-branch]

# 合并指定分支到当前分支
$ git merge [branch]

# 选择一个commit，合并进当前分支
$ git cherry-pick [commit-id]

# 删除指定分支
$ git branch -d [branch-name]

# 强制删除指定分支
$ git branch -D [branch-name]

# 删除远程分支
$ git push origin --delete [branch-name]
$ git branch -dr [remote/branch]
```

### 远程仓库管理

```
# 查看远程仓库（默认是origin，这是git 会使用的默认名称）
$ git remote 

# 指定-v, 查看所有远程仓库地址
$ git remote -v

# 添加一个新的远程仓库
$ git remote add [origin-name] https://github.com/0xAiKang/Note.git

# 查看指定远程仓库的详情信息
$ git remote show [origin-name]

# 重命名远程仓库
$ git remote rename [old-name] [new-name]

# 移除远程仓库
$ git remote remove [origin-name]
```

### Push
```
# 默认推送当前分支
$ git push

# 推送内容到主分支，并建立追踪关系
$ git push -u origin master

# 将本地分支推送到指定远程分支， （本地分支:远程分支）
$ git push origin [branch]:[branch]

# 强行推送当前分支到远程仓库，即使有冲突
$ git push -f
```

### Pull
```
# 取回默认远程仓库的变化，并自动与本地分支合并
$ git pull

# 取回指定远程仓库的变化，并自动与本地指定分支合并（远程分支名:本地分支名）
$ git pull [remote] [branch]:[branch]

# 取回指定远程仓库的变化，并自动与本地当前分支合并
$ git pull origin master

# 取回远程仓库的所有变动，但是不会自动与本地当前分支合并
$ git fetch
```

## 进阶操作
进阶操作中的命令是一些很实用，但可能不常使用，所以把它们单独拎出来。

### cherry-pick

```
# 选择一个commit，合并进当前分支
$ git cherry-pick [commit-id]

# 保留原有作者信息进行提交
$ git cherry-pick -x [commit-id]
```

### Stash

```
# 将当前的工作区隐藏
$ git stash

# 恢复隐藏的工作区，并将此次隐藏记录从隐藏列表中移出
$ git stash pop

# 恢复隐藏的工作区，保留此次隐藏记录
$ git stash apply

# 查看当前隐藏列表
$ git stash list
```

### Blame
`git blame` 用于查看某个文件的修改历史记录是哪个作者进行了改动。

```
# 查看 README.md 文件的修改历史记录，包括时间、作者以及内容
$ git blame README.md

# 查看谁改动了 README.md 文件的 11行-12行
$ git blame -L 11,12 README.md

# 查看谁改动了 README.md 文件11行以后
$ git blame -L 11 README.md
```

### 标签
```
# 列出本地所有标签
git tag

# 新建一个tag在当前commit
$ git tag [tag]

# 新建一个tag在指定commit
$ git tag [tag] [commit]

# 删除本地tag
$ git tag -d [tag]

# 删除远程tag
$ git push origin :refs/tags/[tagName]

# 列出远程所有标签
$ git ls-remote --tags origin

# 创建带有附注标签
$ git tag -a v1.1.0 -m "标签描述"

# 查看本地tag信息
$ git show [tag]

# 提交指定tag
$ git push [remote] [tag]

# 提交所有tag
$ git push [remote] --tags

# 新建一个分支，指向某个tag
$ git checkout -b [branch] [tag]
```

### Git ProTips
`Git ProTips` 则是整理的一些Git 的奇技淫巧。
```
# 通过使用别名，优化 git log 输出，这里另外提供几种模式, 可以选择喜欢的一种进行别名配置
$ git config --global alias.lg "log --graph --pretty=format:'%Cred%h - %Cgreen[%an]%Creset -%C(yellow)%d%Creset %s %C(yellow)<%cr>%Creset' --abbrev-commit --date=relative"

$ git config --global alias.his "log --graph --decorate --oneline --pretty=format:'%Creset %s %C(magenta)in %Cred%h %C(magenta)commited by %Cgreen%cn %C(magenta)on %C(yellow) %cd %C(magenta)from %Creset %C(yellow)%d' --abbrev-commit --date=format:'%Y-%m-%d %H:%M:%S'"

$ git config --global alias.hist "log --graph --decorate --oneline --pretty=format:'%Cred%h - %C(bold white) %s %Creset %C(yellow)%d  %C(cyan) <%cd> %Creset %Cgreen(%cn)' --abbrev-commit --date=format:'%Y-%m-%d %H:%M:%S'"

$ git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"

$ git config --global alias.lg "log --pretty=format:'%h - %an, %ar : %s' "
```

### 参考链接
* [Git 常用命令整理](https://github.com/xjh22222228/git-manual)
* [常用Git 命令清单](https://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html)