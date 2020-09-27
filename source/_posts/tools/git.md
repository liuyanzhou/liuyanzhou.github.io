---
title: 'git'
date: 2020-09-08 22:49:05
categories: tools
top: false
summary: webpack常规配置 
tags: 
 - tools
---

### Git

什么是版本控制：

版本控制是指对软件开发过程中各种程序代码、说明文档等文件的变更进行管理，它将追踪文件变化，记录文件的变更时间、变更内容、甚至变更执行人进行记录，同时对每一个阶段变更（不仅仅只是一个文件的变化）添加版本编号，方便将来进行查阅特定阶段的变更信息，甚至是回滚。

什么是git：它是我们程序员的项目版本控制工具，可以取代我们曾经人为对项目代码的管控，使我们在对项目代码有更加智能、科学的管理。

人工版本控制器：通过人工的复制行为来保存项目的不同阶段内容，添加适当的一些描述文字加以区别，繁琐、容易出错。产生大量重复（冗余）数据

版本控制根据（git）：通过程序完成上述人工版本控制行为，方便且功能强大，只记录不同版本之间变化的部分。

常见的版本控制根据：CVS、SVN、Git....

### 二、Git如何工作

那么我们就得了解两个概念：状态、区域

git文件生命周期

![git文件生命周期](/medias/imges/tools/git/lifecycle.png)

状态：同时，git又通过了三种（也可以说是四种）不同的记录状态:

* 已修改（modified）
* 已暂存（staged）
* 已提交（commited）
* 未修改（Unmodified）
* 未追踪（Untracked）

区域：git提供了三个不同的工作区，用来存放不同的内容

* 工作目录
* 暂存区域
* Git仓库

![工作区](/medias/imges/tools/git/areas.png)

### 三、Git使用

* 安装：

官网：https://git-scm.com/

* 配置：

当安装完Git应该做的第一件事就是设置你的用户名称与邮件地址。这样做很重要，因为每一个Git的提交都会使用这些信息，并且它会写入到你的第一次提交中，不可更改

```bash
git config user.name "你的名字"
git config user.email "你的邮箱"
```

--global：通过`--global`选项可以设置全局配置信息

```bash
git config --global user.name "你的名字"
git config --global user.email "你的邮箱"
```

检查配置

```bash
# 打印所有config 
git config --list
# 打印指定config
git config user.name
```

* 创建仓库 - repository

进入希望纳入 git 版本控制的项目目录，使用`git init`初始化

```bash
git init 
```

该命令将创建一个名为`.git`的子目录，这个子目录含有你初始化的 Git 仓库中所有的必须文件，这个目录也是上面我们说的 三个区域之一（Git仓库），这个目录也是 Git 保存数据记录的地方，非常重要，如非必要，不要轻易改动

### 四、工作流与基本操作

当一个项目被 Git 初始化以后，只是表示我们希望通过 Git 来管理当前的这个项目文件不同时期版本记录，但是这个时候项目中已存在的文件，或者以后新增的文件都是没有进入版本控制管理的，它们是`未追踪（Untracked）`的状态。

#### 4.1 查看工作区的文件状态

查看工作区的文件状态

```bash
git status
```

#### 4.2 乱码

git  status 显示乱码

```bash
git config --global core.quotepath fasle 
```

终端乱码

菜单 -> 设置 -> 文本 -> 本地/编码

或修改配置文件

```
[gui]  
    encoding = utf-8  
    # 代码库统一使用utf-8  
[i18n]  
    commitencoding = utf-8  
    # log编码
[svn]  
    pathnameencoding = utf-8  
    # 支持中文路径
[core]
    quotepath = false 
    # status引用路径不再是八进制（反过来说就是允许显示中文了）
```

#### 4.3 添加工作区文件到暂存区

```bash
# 添加指定的文件
git add 1.txt
# 添加多个文件
git add 2.txt 3.txt
# 添加整个目录
git add ./a
# 添加多个目录
git add ./b ./c
# 添加所有文件
git add .
```

#### 4.4 创建版本

`git commit`:将暂存区里的改动提交到本地 git 仓库。（一般把某个具有特定意义的工作作为一个版本，它可以是多个文件的变化），每一次提交同时会生成一个 40 位的哈希值，作为该次提交版本的唯一 id

提交备注：每次提交都需要填写备注信息

```bash
# 会调用默认（或自定义）的文本编译器
git commit 
# 备注简短的信息
git commit -m"备注的信息"
```

修改默认编译器：百度

#### 4.5 查看提交日志

```bash
# 完整模式
git log
# 简要格式（单行注释）
```

#### 4.6 修复提交

`git commit --amend`：修复（替换上一次）提交，在不增加一个新的提交版本的情况下将新修改的代码追加到前一次的提交中

```js
git commit --amend -m "替换提交的备注"
```

#### 4.7 删除

```bash
# 从 git 仓库与工作区中删除指定文件
git rm 文件

# 只删除 git 仓库中的文件
git rm --cached 文件

# rm以后，需要 commit 这次操作，否则 rm 将保留在暂存区
git commit -m '重新提交备注信息'
```

### 4.8 撤销重置

git reset

* 从暂存去中撤销到工作区

```bash
# 从暂存区中撤销一个指定文件
git reset HEAD 文件名称
# 从暂存去中撤销所有文件
git reset HEAD .
```

* 该命令也可用于回退版本

```bash
# 回退到指定的 commitID 版本
git reset --hard commitID
```

#### 4.9 比较

我们的文件能够在 工作区、暂存区、仓库之间相互比较

```bash
# 比较 工作区和暂存区
git diff 文件
# 比较 暂存区和仓库
git diff --cached [commitid]文件
# 比较 工作和仓库
git diff commitId filename
# 比较 仓库不同版本
git diff commitId1 commitId2
```

### 五、分支

我们的开发就像是游戏的任务，默认是在主线（master）上进行开发的。许多时候，还要各种直线任务，git 支持我们创建分支来进行项目开发

* 查看分支

```bash
git branch
```

* 创建分支

```bash
git branch 分支名称
```

* 切换分支

```bash
git checkout 分支名称
# 也可以使用 checkout -b 来新建分支，它会检查分支是否存在，不存在就自动创建
git checkout -b 分支名称
```

* 分支合并

```bash
# B 合并到 A，需要切换到A分支，也就是谁被合并就切换到谁去合并的那个目标分支下操作
git merge 被合并的分支

# 查看已经合并的分支
git branch --merged
# 查看未合并的分支
git branch --no-merged
```

* 删除分支

```bash
# 如果分支为未合并状态，则不允许删除
git branch -d 分支名称
# 强制删除
git branch -D 分支名称
```

### 六、合并记录

```bash
# 合并 HEAD 前两个祖先记录
git rebase -i HEAD~2
```

~与^：~：纵向，^：横向

![记录](/medias/imges/tools/git/path.png)

**rebase操作**

```bash
# p, pick = use commit => 使用
# r, reword = use commit, but edit the commit message => 使用，但重新编辑说明
# e, edit = use commit, but stop for amending => 使用
# s, squash = use commit, but meld into previous commit => 使用，但合并上一次
# f, fixup = like "squash", but discard this commit's log message => 就像 squash 那样，但会抛弃这个 Commit 的 Commit message
# x, exec = run command (the rest of the line) using shell => 执行脚本
# d, drop = remove commit => 移除
```

```bash
git rebase -i HEAD~3
# 弹出编译器，根据需要的进行修改，然后保存
# 如果为 r ，s 则会在再次弹起编译器，修改新的 commit message 修改之后保存
```

> 如果出现一些问题，可以通过`git rebase --edit-todo`和`git rebase --continue`机芯重新编辑保存

### 七、合并冲突

有的时候，不同的分支可能回对同一个文件内容和位置上进行操作，这样在合并的过程中就会产生冲突，这时我们要做：

* 查看冲突
* 修复冲突
* 提交

HEAD 是代表你当前合并分支的分支名

### 八、标签

有的时候，我们希望给某一个特定的历史提交打上一些标签

* 新建tag

```bash
git tag -a v1.0.0/ HEAD/commitId
```

* 查看tag

```bash
git tag 
```

### 九、协同开发

以上所有的操作都是建立在本地的，如果我们希望进行团队协同开发，那么这个时候，我们就需要把 git 仓库信息与团队中的所有人进行共享，我们现在使用的是 分布式 也就是 去中心化模式。

协同开发我们免不了需要一个作为我们项目团队成员之间的一个转发载体，而且还能够要求成员之间都能访问。这时我们可以借助第三方平台`github`或`gitee`

我们上交代码需要对提交者的身份进行验证，有两种方式，一种是填写web凭证（填写提交平台的账号和密码），另一种这是 ssh

第一种太简单，我们来说 ssh

教程配置：

https://help.github.com/cn/articles/connecting-to-github-with-ssh

https://help.github.com/cn/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent

* 生成 ssh 秘钥

```bash
ssh-keygen -t rsa -c '邮箱'
```

* 添加代理：使用`ssh-add`代理，如果没有启动，可以手动启动

```bash
eval $(ssh-agent -s)
```

* 添加私钥

```bash
ssh-add 私钥路径
```

* 在github 上添加公钥

个人中心->设置->ssh->添加

测试

```bash
ssh -T git@github.com
```

### 十、git远程

链接：

```bash
git remote add origin 库路径
```

提交（同步）流程

同步本地仓库到远程

```bash
git push -u origin master
# -u 简化后续操作
git push origin master
```

远程分支

```bash
# 提交到远程（分支）
git push origin [本地分支名称]:[远程分支名称]

# 远程先创建好分支然后拉去到本地
git checkout -b [本地分支名称] origin/[远程分支名称]

# 拉取远程分支到本地
git pull origin [远程分支名称]:[本地分支名称]

# 查看远程仓库
git remote show origin

# 查看远程本地分支
git branch

# 查看远程分支
git branch -r

# 查看所有分支
git branch -a

# 删除本地分支
git branch -d [本地分支名称]

# 删除远程分支
git push origin --delete [远程分支名称]
# or
git push origin :[远程分支名称]

# 设置默认提交分支
git branch --set-upstream-to=origin/[远程分支名称] [本地分支名称]
```

### 十一、工作流-git work flow

![记录](/medias/imges/tools/git/gitflow.png)



### 十二、图形化工具（GUI）

https://git-scm.com/download/gui/win

- Sourcetree
- other editor