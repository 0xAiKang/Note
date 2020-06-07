### 准备工作
*      创建文件夹： mkdir filename
      进入文件夹： cd filename
      创建文件夹并进入： mdkir filename && cd filename
      初始化，将这个目录变成Git仓库： git init  
      `~` 这个符号表示快速进入当前用户目录。~/.xxx 通常这种形式
      
### 用户名和邮箱配置
*      配置用户名：git config user.name "用户名"
      配置邮箱：git config user.email "邮箱"   #上面这两个命令是任何时候都可以配置的。并不限定在做准备工作时才能用
      配置全局的用户名：git config --global user.name "用户名"
      配置全局的邮箱：git config --global user.email "邮箱" #加上参数 global
    
### 提交操作
*      添加文件到暂存区： git add filename
      一次性提交所有文件： git add .
      提交文件到版本库：git commit -m "Reamrk"
      从工作区一次性更新到版本库： git commit -a -m "Ramark"

### 查看操作日志      
*     查看工作区状态：git status  
      查看详细提交历史：git log  # 按q退出
      查看简约提交日志：git log --pretty=oneline
      查看命令历史：git reflog
      查看分支历史图（简约版）：git log --graph --pretty=oneline --abbrev-commit
      查看分支历史图（详细版）：git log --graph
      查看前n条记录：git log -n   #这里的n指的是数字
      查看提交版本之间的差异：git log -p  #相当于图形化的比较差异
      查看每个用户提交了多少个commit：git shortlog
      查看每个用户提交的commit 数量：git shortlog -s -n
      
### 对比文件差异
*     对比当前分支下工作区与暂存区的差异： git diff 
      对比当前分支下工作区与暂存区该文件的差异： git diff filename.txt     
      对比当前分之下暂存区与版本库的差异： git diff --cached
      对比当前分支与目标分支的差异： git diff objbranch
      对比工作区和版本库里的最新版本有什么区别：git diff HEAD -- <file name>

### 撤销操作
*     撤销对工作区的修改： git checkout -- filename  
      撤销对工作区的所有修改： git checkout .
      撤销对暂存区的修改： git reset HEAD -- filename
      丢弃暂存区的修改：git checkout -f   # 将所有内容都丢弃
      
### 版本回退
*     回退上一个head^： git reset --hard HEAD^   
      回退到指定数字的版本：git reset --hard HEAD~num
      回退到指定的版本：git reset --hard commit id
      ^ : 脱字符，会退一个版本。
      ~n : 波浪字符，相当于回退n个版本。
      回退到某个版本的暂存状态：git reset --soft HEAD^

### 删除操作
*     删除文件： rm filename  # 从文件资源管理器中删除文件
      从暂存区删除文件： git rm filename # 因为工作区和暂存区不一致了，所以确认是否真的删除。 然后Commit 提交删除。 
      从缓存区删除该文件（工作区保留）：git rm --cached <filename>
      从缓存区删除所有文件：git rm -r --cached .
      删除文件： git rm -f filename  # 暂存区、工作区一起删除
      
      

### 分支操作
*     创建分支： git branch branchname
      切换分支： git checkout branchname
      切换到远程分支：git checkout origin/branch -b mybranch
      创建+切换操作： git checkout -b branchname
      查看本地仓库所有分支： git branch
      删除分支： git branch -d branchname
      强制删除分支(甚至不合并)：git branch -D branchname
      合并分支： git merge branchname
      查看本地+远程所有分支： git branch -a 
      从本地删除远程分支：git push origin --delete BranchName  #这里的origin指的是你想删除的分支所在的URL
      
      建立追踪关系：git branch --set -upstream master origin/master #如果当前分支与远程某个分支存在追踪关系，那么本地分支和远程分支都可以省略。 $ git fetch origin
      在本地删除远程分支： git push origin --delete branchname


### 远程仓库操作
*     关联一个远程库： git remote add origin url # SSH or HTTPS
      克隆远程所有分支到本地： git clone url or origin # 如果提前有建立联系，那么就使用上面的命令。如果没有就还是用 SSH or HTTPS
      拉取分支： git fetch origin branchname #
      推送分支： git push origin branchname 
      推送分支： git push origin mybranch:master #将mybranch分支上的提交推送到远程master分支上
      强制推送分支： git push -f origin branchname # 将本地branchname 分支推送到远程会强制覆盖。
      拉取远程最新的提交同时自动合并： git pull  # 后面一般都会有条件的
      查看远程库的名字： git remote 
      查看远程库的基本信息： git remote -v 
      查看远程库的详细信息：git remote show origin
      删除远程库： git remote rm originname 
      在一个远程库中添加多个url：git remote set-url --add origin <url>  # 也可以直接去config中添加，不用执行命令。

### Git ProTips
      给命令取别名： 
    ### git alias 
    * git config --global alias.co 'checkout' # git co => git checkout
    * git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
    * git config --global alias.lg "log --pretty=format:'%h - %an, %ar : %s' "


    
    