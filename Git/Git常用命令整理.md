# 基本概念
* 工作区：简单理解就是在你的物理磁盘上能直接看见的文件
* 暂存区：暂存区就是一个对已经修改了的文件进行存放（标记）的区域（文件）一般存放在 `.git目录下` 下的index文件（.git/index）中，所以有时也被称作为索引。
* 版本库：也叫Git仓库，是Git用来保存项目的元数据和对象数据库的地方。这是Git中最重要的部分，在工作区的隐藏目录下。

# 准备工作
这里就不介绍如何安装Git了，如果你还没有安装或者不会安装，[请看这里](https://git-scm.com/book/zh/v2/%E8%B5%B7%E6%AD%A5-%E5%AE%89%E8%A3%85-Git)。

Git 自带一个 git config 的工具来帮助设置控制 Git 外观和行为的配置变量。 这些变量存储在三个不同的位置：
* `/ect/gitconifg`文件（Git的安装目录下）：包含系统上每一个用户及他们仓库的通用配置。使用`git config -e --system`命令读写配置变量
* `~/.gitconfig 或 ~/.config/git/config`文件（当前用户目录下）：只针对当前用户。 使用 `git config -e --global` 命令读写配置变量。
* `.git/config`文件（Git仓库下）：当前Git仓库下的配置文件，只针对该仓库。使用`git config -e` 命令读写配置变量。

## 用户信息
当安装完 Git 应该做的第一件事就是设置你的用户名称与邮件地址。

这样做很重要，因为每一个 Git 的提交都会使用这些信息，并且它会写入到你的每一次提交中，不可更改：
```
$ git config --global user.name "your name"
$ git config --global user.email "your@domain.com"

```
### 编辑器
这一步其实都没那么重要，但是如果你还不知道`Vim`是什么，还是有必要知道这个命令的：
```
# 这里使用记事本作为默认编辑器
$ git config --global core.editor notepad 

```

### 检查配置信息
查看你对Git所做的所有配置：
```
$ git config --list

```
查看你对Git所做的某一项配置：
```
$ git config <key> 
$ git config user.name
max
```

### 获取帮助
若你使用 Git 时需要获取帮助，
```
# verb 指某个git命令
$ git help <verb>
$ git <verb> --help
$ man git-<verb>
```

例如要想获得 config 命令的手册，执行：
```
$ git help conifg 
```

### 获取Git仓库
获取Git仓库有两种方式： **第一种是在现有项目或目录下导入所有文件到 Git 中； 第二种是从一个服务器克隆一个现有的 Git 仓库。**

在现有目录中初始化仓库
```
$ git init
```

克隆现有的仓库
```
$ git clone [url]
$ git clone git@github.com:Hooooliday/HelloWorld.git # 克隆我的Github上的项目
```
需要注意的有两点：
1. 使用`git clone`命令时，远程Git仓库中的每一个文件每一个版本都会拉取下来。
2. 该命令会在本地直接创建一个父文件夹。

如果需要自定义仓库名，使用命令：
```
$ git clone [url] rename

```

# 常见状态
请记住，你工作目录下的每一个文件都不外乎这两种状态：**已跟踪或未跟踪。**已跟踪的文件是指那些**被纳入了版本控制的文件，** 在上一次快照中有它们的记录。工作目录下的未跟踪文件，他们既不存在于上次快照中，也没放入暂存区。初次克隆某个仓库的时候，工作目录中的所有文件都属于已跟踪文件，并处于未修改状态。

使用Git时，文件生命周期如下：
![status](https://git-scm.com/book/en/v2/images/lifecycle.png)

## 检查文件状态
要查看哪些文件处于什么状态，使用命令：
```
$ git status
On branch master
nothing to commit, working directory clean
```
上面的两条输出告诉我们：
1. 所有已跟踪文件在上次提交后都未被更改过，而且当前目录下没有任何处于未跟踪状态的新文件。
2. 还显示了当前所在分支，并告诉你这个分支同远程服务器上对应的分支没有偏离。

## 跟踪文件
上面那个标题并不能完整的概括该命令的功能，还有的功能：
1. 把已跟踪的文件放到暂存区
2. 开始跟踪新文件
3. 合并时把有冲突的文件标记为已解决状态
```
$ git add <file>

```
git add 命令可以使用文件或目录的路径作为参数；如果参数是目录的路径，该命令将递归地跟踪该目录下的所有文件。

#### 简化输出
git status 命令的输出十分详细，但其用语有些繁琐。 使用`--short`或者是`-s`参数，简化该命令的输出
```
$ git status -s
 M README # README 文件在工作区被修改了但是还没有将修改后的文件放入暂存区
MM Rakefile #  在工作区被修改并提交到暂存区后又在工作区中被修改了，所以在暂存区和工作区都有该文件被修改了的记录。
A  lib/git.rb # 新暂存的文件。
M  lib/simplegit.rb # lib/simplegit.rb 文件被修改了并将修改后的文件放入了暂存区
?? LICENSE.txt # 未跟踪的文件
```
* 新添加的未跟踪文件前面有 ?? 标记
* 新添加到暂存区中的文件前面有 A 标记
* 修改过的文件前面有 M 标记。

你可能注意到在上面的输出中，M 有两个可以出现的地方。出现在右边的 M 表示该文件被修改了但是还没放入暂存区，出现在靠左边的 M 表示该文件被修改了并放入了暂存区。 

## 查看差异
查看尚未暂存的文件的修改部分：
```
$ git diff 
```
此命令比较的是工作目录中当前文件和暂存区域快照之间的差异， 也就是修改之后还没有暂存起来的变化内容。

查看已暂存的文件：
```
$ git diff --cached # 或者使用 --staged选项，效果是相同的。
```
**需要注意的是：git diff  本身只显示尚未暂存的改动，而不是自上次提交以来所做的所有改动。 所以有时候你一下子暂存了所有更新过的文件后，运行 git diff 后却什么也没有，就是这个原因。**

## 提交更新
当确认暂存区已经准备妥当可以提交时，使用命令：
```
$ git commit -m "Remark"
```
其中`-m`选项后面的remark表示对这次提交的描述。

请记住：**每一次运行提交操作，都是对你项目作一次快照，以后可以回到这个状态，或者进行比较。**

### 跳过使用暂存区域
尽管使用暂存区域的方式可以精心准备要提交的细节，但有时候这么做略显繁琐。 Git 提供了一个跳过使用暂存区域的方式， 只要在提交的时候，给 `git commit` 加上 `-a` 选项，**Git 就会自动把所有已经跟踪过的文件暂存起来一并提交**.
```
$ git commit -a -m "Remark" 
```

# 查看提交历史
在提交了若干更新，又或者克隆了某个项目之后，你也许想回顾下提交历史，使用命令：
```
$ git log 
```
默认不用任何参数的话，git log 会按提交时间列出所有的更新，最近的更新排在最上面。

其中包括每个提交的 SHA-1 校验和、作者的名字和电子邮件地址、提交时间以及提交说明。

有时候我们需要简化输出，来寻找所需要的提交。下面介绍几个较为常用的：
#### 显示差异
```
$ git log -p -n
```
* `-p`选项，用来显示每次提交的内容差异
* `-n`选项，其中 n 表示数字，用来显示前 n 次提交
#### 查看每次提交的简略的统计信息
```
# 这个命令通常在需要浏览搭档提交的Commit所带来的变化，极为有用。
$ git log --stat -n
```
#### 指定格式输出
```
[option] => oneline | short | full | fuller 
$ git log --pretty = [option] 
```
#### 查看某个文件的修改历史
```
$ git log --pretty = oneline filename
```
#### 查看某次提交的具体内容
```
$ git show commitID
```

还有：format，可以定制要显示的记录格式。
```
# 这里的几个格式占位符，表示format常用选项。
# 其中 %an 表示作者（author）的名字，%cn提交者（committer）的名字.
# 区别在于：作者指的是实际作出修改的人，提交者指的是最后将此工作成果提交到仓库的人。
$ git log --pretty=format:"%h - %an, %ar : %s, - %cn"
```

#### 查看分支合并
有时我们需要查看本地分支的合并情况时，就需要使用该命令：
```
$ git log --graph
```
简化输出：
```
$ git log --graph --pretty=oneline --abbrev-commit
```

更多关于Git log 命令的细节，请[参考链接](https://git-scm.com/book/zh/v2/Git-%E5%9F%BA%E7%A1%80-%E6%9F%A5%E7%9C%8B%E6%8F%90%E4%BA%A4%E5%8E%86%E5%8F%B2)。


---

# 撤销操作
在任何一个阶段，你都有可能想要撤消某些操作。这里，我们将会学习几个撤消你所做修改的命令。其中有些撤销操作是不可逆的。

工作区、暂存区、版本库之间的关系：
![image](https://note.youdao.com/yws/api/personal/file/1812E5A4342042C68D935ABC8B5A2567?method=download&shareKey=f3458552d16e5707114cc0b942831c18)

### 更改最后一个 commit
有时候我们提交完了才发现漏掉了几个文件没有添加，或者提交信息写错了。使用`--amend`选项极其有用。
```
$ git commit --amend #来更新最近的 commit，而不是创建新的 commit。
```
例如，你提交后发现忘记了暂存某些需要的修改，可以像下面这样操作：
```
$ git commit -m 'solve bug'
$ git add <file>
$ git commit --amend
```
最终你只会有一个提交 - 第二次提交将代替第一次提交的结果。

### 取消暂存
如何取消掉已经暂存的文件呢？使用命令：
```
$ git reset HEAD <file> 
```
需要注意的是 HEAD 这个关键字可以不加，它的作用是指向当前分支。所以等同于该命令：
```
$ git reset <file> # 或者是 git reset -- <file>
```

### 撤销修改
如果你不想保存对某个文件所做出的修改，该如何方便的撤销修改呢？
```
$ git checkout -- <file> # 同样等同于 git checkout <file>
``` 

### 丢弃暂存
> 丢弃暂存有两种方式，clean 直接丢弃 untracked files，checkout 则丢弃已暂存的文件。

删除未暂存的文件(untracked files)
```
$ git clean -f
```
查看有哪些未暂存的文件(untracked files)
```
$ git clean -n
```
连 untracked 的目录一起删掉
```
$ git clean -fd
```
连 gitignore 的untrack 文件/目录也一起删掉
```
$ git clean -fxd
```
![image.png](https://i.loli.net/2020/06/07/hJQRHXs7wOaoFWx.png)


如果不再需要暂存区中的文件，该命令会把暂存区中的所有文件都丢弃掉，危险操作，慎用。
```
$ git checkout -f 
```
## 版本回退
在使用Git时，我们有这样一种需求，需要会退到上一个或者是某一个版本，使用命令：
```
$ git reset --hard HEAD~n
$ git reset --hard HEAD^
$ git reset --hard [commit id]
```
### 保留修改
回退到上一次或者某一个版本，但同时保留该版本所做的所有修改。
```
$ git reset --soft HEAD^
$ git reset --soft [commit id]
```

---

# 远程仓库

## 查看远程仓库
列出所有远程服务器的简写，其中origin - 这是 Git 给你克隆的仓库服务器的默认名字：
```
$ git remote 
```

#### 详细内容
如果需要显示远程库与其对应的URL，使用命令
```
$ git [remote-name] -v
[remote-name]  git@github.com:Hooooliday/HelloWorld.git (fetch)
[remote-name]  git@github.com:Hooooliday/HelloWorld.git (push)
```
#### 查看指定远程库详细配置
```
$ git [remote-name] show origin
```

## 添加远程库
```
$ git remote add [remote-name] <URL> # 添加一个叫做 [remote-name] 的远程仓库。
```

## 拉取
当需要获取某个远程仓库中所有的数据或者你还没有的数据时，使用命令：
```
$ git fetch [remote-name]
```
执行完成后，将会拥有那个远程仓库中所有分支的引用，可以随时合并或查看。

需要注意：git fetch 命令会将数据拉取到你的本地仓库，但是它并不会自动合并或修改你当前的工作。

它不会有一个新的 master 分支，只有一个不可以修改的 origin/master 指针。可以运行 `git merge origin/master `将这些工作合并到当前所在的分支。

如果觉得这种手动合并的方式，太麻烦了，Git还提供了另一个拉取的命令：
```
$ git pull
```
该命令来自动的抓取然后合并远程分支到当前分支，前提是需要为当前分支设置好远程跟踪分支。

## 推送
当你想要将本地 master 分支推送到 origin 服务器时，那么运行这个命令就可以将你所做的备份到服务器：
```
$ git push origin master
```
**只有当你有所克隆服务器的写入权限，并且之前没有人推送过时，这条命令才能生效。** 

这里有些工作被简化了，Git自动将 master 分支名字展开为 `refs/heads/serverfix:refs/heads/serverfix`，表示：“推送本地的 master 分支来更新远程仓库上的 master 分支。” 这里我们先不说 `refs/heads`是什么意思，属于Git内部原理中的内容。

你只需要知道可以运行 `git push origin master:master` 来将本地的 master 分支推送到远程仓库上的 master 分支。

所以每次在推送之前，最好先检查一下是否需要更新本地提交。

## 远程仓库的移除与重命名
重命名远程仓库
```
$ git remote rename [oldremote-name] [newremote-name]
```
删除远程仓库
```
$ git rm remote [remote-name]
```


---
# 打标签
Git 可以给历史中的某一个提交打上标签，以示重要。 比较有代表性的是人们会使用这个功能来标记发布结点（v1.0 等等）。

Git 使用两种主要类型的标签：轻量标签（lightweight）与附注标签（annotated）。

## 创建轻量标签
轻量标签本质上是将提交校验和存储到一个文件中 - 没有保存任何其他信息。所以轻量标签可以理解为一个临时标签。
```
$ git tag <tag nmae>
```
创建一个轻量标签非常简单，只需要在后面指定标签名就好了。

## 创建附注标签
附注标签其中包含打标签者的名字、电子邮件地址、日期时间；还有一个标签信息；
通常建议创建附注标签，这样你可以拥有以上所有信息；
```
$ git tag -a <tag name>
```


---

# 使用别名
Git 并不会在你输入部分命令时自动推断出你想要的命令。 如果不想每次都输入完整的 Git 命令，可以通过 git config 文件来轻松地为每一个命令设置一个别名。
```
$ git config --global alias.co checkout
$ git config --global alias.br branch
$ git config --global alias.ci commit
$ git config --global alias.st status
```

# 分支管理

## 创建分支
```
$ git branch [branch name]
```
## 查看分支
```
$ git branch
```
#### 查看已经合并的分支
```
$ git branch --merged
```
#### 查看尚未合并的分支
```
$ git branch --no-merged
```
尚未合并的分支，是不能直接删除的，如果想要删除分支并丢掉那些工作，可以使用 `-D` 选项强制删除。

## 切换分支
```
$ git checkuot [branch name]
```
## 删除本地分支
```
$ git branch -d [branch name ]
```

* 强制删除使用 `-D` 选项。

## 删除远程分支
```
$ git push origin --delete [branch name]
```
## 修改本地分支名称
```
$ git branch -m [oldbranch name] [newbranch name]
```

## 合并分支
```
$ git merge branch 
```
## 跟踪分支
**跟踪分支是与远程分支有直接关系的本地分支。** 如果在一个跟踪分支上输入 git pull，Git 能自动地识别去哪个服务器上抓取、合并到哪个分支。

从一个远程跟踪分支检出一个本地分支会自动创建一个叫做 “跟踪分支”（有时候也叫做 “上游分支”）。
```
$  git checkout -b [branch] [remotename]/[branch]
```
当克隆一个仓库时，它通常会自动地创建一个跟踪 origin/master 的 master 分支。
```
$ git clone [url]
```
这是十分常见的两种自动建立跟踪关系的快捷方式。

如果想要修改正在跟踪的上游分支，你可以在任意时间使用 -u 或 `--set-upstream-to` 选项运行 git branch 来显式地设置。
```
$ git branch -u origin/master
Branch master set up to track remote branch master from origin.
```
这样就像当前分支与 远程origin/master 分支建立了跟踪关系
#### 查看跟踪分支
将本地所有已跟踪分支列出来且包含更多信息
```
$ git branch -vv 
# 或者使用
$ git config -e #其中 [branch "branch name"]表示所跟踪分支
```