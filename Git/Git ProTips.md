# 前言
整理一些Git 相关的小技巧，持续更新。

## 查看历史提交记录
虽然使用默认的`git log`查看历史提交记录，但相对有用的信息并不多。

所以可以通过一些额外的参数来改变输出内容。

```
git config --global alias.logs "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```
通常设置别名的方式，输入`git logs`执行命令，可以看整个历史记录会以非常简洁的形式所展示，到除了有基本的`commit log`之外，还有`commit id`、时间以及提交人。

![image.png](https://i.loli.net/2020/05/11/gRbldQyh1eLxKvA.png)

## 统计个人提交次数
有时候，我们会想要统计每个人的历史提交次数，这会很有意思。

```
git shortlog -sn 
140  yuli
11  macf2000
 8  lja
 5  Boo
 3  liu45449
 2  bruce
```
这只是提交数量，但是代码行呢？~~每个优秀的工程师都知道这是最重要的指标。~~ 有大量简洁的CLI项目可帮助您分析Git数据。

放心，一些勤劳的人已经为我们完成了艰苦的工作，并编写了一个非常方便的 CLI。
* @arzzen的[git-quick-stats](https://github.com/arzzen/git-quick-stats)很好地概述了存储库活动。您甚至可以按天，小时和月查看贡献。
```
$ git-quick-stats

Generate:
    1) Contribution stats (by author)
    2) Contribution stats (by author) on a specific branch
    3) Git changelogs (last 10 days)
    4) Git changelogs by author
    5) My daily status
    6) Save git log output in JSON format

 List:
    7) Branch tree view (last 10)
    8) All branches (sorted by most recent commit)
    9) All contributors (sorted by name)
   10) Git commits per author
   11) Git commits per date
   12) Git commits per month
   13) Git commits per weekday
   14) Git commits per hour
   15) Git commits by author per hour
```
* @casperdcl的[git-fame](https://github.com/casperdcl/git-fame)还总结了贡献，包括代码行。
```
$ git-fame
Total commits: 5190
Total ctimes: 17640
Total files: 1058
Total loc: 113683
| Author                   |   loc |   coms |   fils |  distribution   |
|:-------------------------|------:|-------:|-------:|:----------------|
| Stanley Goldman          | 75955 |   3164 |    414 | 66.8/61.0/39.1  |
| Andreia Gaita            | 34438 |   1549 |    477 | 30.3/29.8/45.1  |
| Emil "AngryAnt" Johansen |  2230 |    191 |     35 | 2.0/ 3.7/ 3.3   |
| Don Okuda                |   312 |    195 |     51 | 0.3/ 3.8/ 4.8   |
| Meaghan Lewis            |   228 |     15 |     31 | 0.2/ 0.3/ 2.9   |
```
* @IonicaBizau的[git-stats](https://github.com/IonicaBizau/git-stats)包含各种有趣的数据，包括贡献值和饼图。就像他的许多项目一样，CLI 输出令人愉快。👏🏻

![image](https://i0.wp.com/user-images.githubusercontent.com/121322/78825418-cd9d9a00-7994-11ea-8fe4-9353059e0da9.png?ssl=1)

### 参考链接
[GitHub Protips: Tips, tricks, hacks, and secrets from Lee Reilly](https://github.blog/2020-04-09-github-protips-tips-tricks-hacks-and-secrets-from-lee-reilly/#9-a-numbers-game)