# 前言
其实关于这个问题，老早都想整理了，只是一直没有腾出空来。最近刚好有空，索性整理了下。

这里就不过多介绍什么是`Git`了，本文的重点是`Commit Log`，如果还不清楚`Git`是什么，可以看一下我的`Git`系列的其他笔记。

## 为什么要关注提交信息
1. 加快`Reviewing Code`的过程
2. 提醒自己或他人，某个提交具体增加了什么功能，改动了哪些地方
3. 提高项目的整体质量

## Angular 规范的 Commit message 格式
这种格式（规范）是我目前觉得相对其他格式（规范）而言，最容易接受、上手的一种。

其核心是每次提交，Commit message 都包括三个部分：Header，Body 和 Footer。
```
<type>(<scope>): <subject>
// 空一行
<body>
// 空一行
<footer>
```
其中，Header 是必需的，Body 和 Footer 可以省略。

### Header
Header 部分只有一行，包括三个字段：**type**（必需）、**scope**（可选）和 **subject**（必需）。

**type** 用于说明 `commit` 的类别，只允许使用下面 7 个标识。

* **feat** 新功能（feature）
* **fix** 修补 bug
* **docs** 文档（documentation）
* **style** 格式（不影响代码运行的变动）
* **refactor** 重构（即不是新增功能，也不是修改 bug 的代码变动）
* **test** 增加测试
* **chore** 构建过程、辅助工具的变动
* **perf** 提高性能
* **typo** 打字错误

**scope** 用于说明 `commit` 影响的范围，比如数据层、控制层、视图层等等，视项目不同而不同。

**subject** 是 `commit` 目的的简短描述，不超过 50 个字符。

### Body
Body 部分是对本次 commit 的详细描述，可以分成多行。

### Footer
Footer 部分只用于不兼容变动和关闭 Issue。

### 总结
本来我自己一直使用的方式就是：`git commit -am "fix login bug`，虽然并没有绝对的对错，但这显然不是最好的方式。

这种东西并没有强制性的规定，只要团队之间约定好，然后按照这个约定协作就好了。

所以我觉得在团队之间`commit`时，可以不用完全按照`Angular 规范的Commit message`格式去提交，可以按照以下约定来执行。
* `commit`时，只用保留 Header 部分就好。
* `pull request`时，才需要 Header、Body、Footer 这三部分。

另外`commit`时需要注意以下几点：
* 创建短小而明确的`commit`，一句话说清楚。
* 一个小改动对应一次`commit`，不建议一大堆改动，一次`commit`。
* 如果添加的代码会使项目发生极大的变化，那么需要及时更新`remade`文件以向他人说明此次更改。

#### 最佳实践

```
docs: add FAQ in readme file
feat: increase user login function
fix: fix user login bug
```

### 参考链接
* [Git 如何写好 Commit Log？](https://ruby-china.org/topics/15737)
