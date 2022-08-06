---
title: in-deep-practice-of-Git
date: 2022-08-06 21:58:52
tags:
- Git
- git命令
- git学习
- git撤销
- git底层原理
---

# Git的深入实践与底层原理

本文主要面向于有一定Git使用经验的小伙伴，对于开发初学者，可以先行学习Git的基础文档（[官方中文文档](https://git-scm.com/book/zh/v2)）。

## 零、目录
- 快速理解 Git 是什么？
    - 简单介绍
    - 基础概念与模型
        - Git的三个阶段与状态
        - Git的三个树的概念
- Git 场景的最佳实践
    - 分支管理场景
    - 重写提交
    - 重置实践
    - 压缩提交
    - reset 与 checkout
    - 合并实践
- Git 相关命令说明
    - git rebase
    - git stash
    - git cherry-pick
    - git reset
    - git revert
    - git restore
    - git 基础命令
- Git内部实现模式
    - 数据对象
    - 树对象
    - 提交对象

## 一、快速理解 Git 是什么？

简单的说，Git是以文件快照模式实现的分布式版本控制系统。与以前的版本管理系统不同之处在于不再基于差异来进行版本管理，而是将所有文件以快照流的形式进行版本管理。

**基于差异的版本控制与基于快照的版本控制：**

![基于差异的版本控制](https://image.freefe.cc/in-deep-practice-of-Git%2FUntitled.png)

![基于快照的版本控制](https://image.freefe.cc/in-deep-practice-of-Git%2FUntitled%201.png)


### **基础概念与模型**

**Git的三个阶段与状态：**

**工作目录（Working Directory）、暂存区（Staging Area）以及本地Git仓库（Repository）**，于此对应的三类状态为 **已修改（modified）、已暂存（staged）和已提交（committed）**。

![Untitled](https://image.freefe.cc/in-deep-practice-of-Git%2FUntitled%202.png)

基本上我们在具体分支上的开发操作行为，均为文件在三个状态上进行变更与切换，生成不同的blob对象、树对象以及提交对象（下文会减少），为版本管理做最基础的内容准备。

![Untitled](https://image.freefe.cc/in-deep-practice-of-Git%2FUntitled%203.png)

**Git的三个树的概念（文件快照对象）：**

- **HEAD**：**当前分支引用的指针，指向与本分支上最后一次提交的 提交对象。**相当于本地分支仓库的最后提交快照。

```jsx
// 可以通过 git cat-file 底层命令来查看指向的对象内容
$ git cat-file -p HEAD
tree c0642170219eca3b6ab5dc09d33a38f76cdccabf
author niyingfeng <niyingfeng@bilibili.com> 1648899190 +0800
committer niyingfeng <niyingfeng@bilibili.com> 1648899190 +0800

project init
```

- **index**：**预期的下一次提交**。 可以看做当前暂存区当前指向的树对象内容，在commit提交之后，会根据当前的树对象生成提交对象，给到HEAD进行最新提交对象的指向。

```jsx
$ git ls-files -s
100644 214388fe43cdfd7ce1c29cd3e401541ded620dba 0	.browserslistrc
100644 57bdc954a7c1146e4cd15fdaeff49142f6e03ad0 0	.eslintignore
100644 085c046a63e3e170b22ab95976be489776c00b22 0	.eslintrc.js
100644 11f5d7142359f5ab9e3af8ed88151e17f7c74d53 0	.gitignore
100644 8f072e4648034ba953d11019ae0c82f5d826ee0e 0	README.md
```

- **Working Directory**：工作区在提交变更文件到暂存区后，更新 index 指向的树对象中变更的具体文件的hash，达到提交暂存区的操作。

## 二、Git 场景的最佳实践

### 分支管理场景

一般情况，我们会依赖自身业务模式选取一定的分支管理模式。这边简单列举下目前较为流行的三种分支管理策略，各自业务也可以根据自身业务复杂度、团队大小等来决定使用哪种分支管理策略或者进行一定的变形。

**GitFlow**

![有一定复杂度，因其复杂度在简单的项目中容易引发落地问题。](https://image.freefe.cc/in-deep-practice-of-Git%2FUntitled%204.png)

有一定复杂度，因其复杂度在简单的项目中容易引发落地问题。

**GitHub Flow**

![过于简洁后，会有关于部署、环境、发布等问题](https://image.freefe.cc/in-deep-practice-of-Git%2FUntitled%205.png)

过于简洁后，会有关于部署、环境、发布等问题

**GitLab Flow**

![Untitled](https://image.freefe.cc/in-deep-practice-of-Git%2FUntitled%206.png)

### 重写提交

****修改最后一次提交：****`$ git commit --amend` 

- 若仅仅只是修改提交信息，则可直接修改提交内容。
- 若要修改文件变更，则可将变更放入暂存区后执行。类似将HEAD指向新的提交对象，改提交对象中为新的树对象（有内容变更的话）。

**修改多次提交**：

可以使用reset方式重置历史提交，并且重新进行提交处理（重置实践会进行说明）

### 重置实践

**git文档的 reset 图解，reset的基础操作：**

![初始状态](https://image.freefe.cc/in-deep-practice-of-Git%2FUntitled%207.png)

![—soft 模式状态](https://image.freefe.cc/in-deep-practice-of-Git%2FUntitled%208.png)

![—mixed 模式状态，默认状态](https://image.freefe.cc/in-deep-practice-of-Git%2FUntitled%209.png)

![-hard](https://image.freefe.cc/in-deep-practice-of-Git%2FUntitled%2010.png)

其他任何形式的 `reset` 调用都可以轻松撤消，但是 `--hard` 选项不能，因为它强制覆盖了工作目录中的文件。 在这种特殊情况下，我们的 Git 数据库中的一个提交内还留有该文件的 **v3** 版本， 我们可以通过 `reflog` 来找回它。

`reset` 命令会以特定的顺序重写这三棵树，在你指定以下选项时停止：

- 移动 HEAD 分支的指向 **（若指定了 `--soft`，则到此停止，保留工作区与暂存区reset前的内容）**
- 将Index（暂存区）内容重置对齐 HEAD **（若未指定 `--hard`，则到此停止，默认 `--mixed`）**
- 使工作目录内容重置对齐 Index（暂存区）

**运行 `git reset file.txt`（ `git reset --mixed HEAD file.txt` 简写形式，没有指定一个提交的 SHA-1 或分支，也没有指定 `--soft` 或 `--hard`）：**

![本质上只是将 `file.txt` 从 HEAD 复制到索引中，当然可以知道具体的hash版本的文件](https://image.freefe.cc/in-deep-practice-of-Git%2FUntitled%2011.png)

本质上只是将 `file.txt` 从 HEAD 复制到索引中，当然可以知道具体的hash版本的文件

### **压缩提交**

借助于reset的能力，重置多个历史提交，保留暂存区内容就行重新提交。

![Untitled](https://image.freefe.cc/in-deep-practice-of-Git%2FUntitled%2012.png)

![Untitled](https://image.freefe.cc/in-deep-practice-of-Git%2FUntitled%2013.png)

### **reset 与 checkout**

和 `reset` 一样，`checkout` 也操纵三棵树，不过它有一点不同，这取决于你是否传给该命令一个文件路径。【git 2.23后，独立了 restore 和 switch 命令来对齐 checkout 恢复工作区文件和切换分支的功能场景】

 **不带文件路径 `git checkout [branch]` 与  `git reset --hard [branch]`**

- 不同于 `reset --hard`，`checkout` 对工作目录是安全的，它会通过检查来确保不会将已更改的文件弄丢。 会在工作目录中先试着简单合并一下，这样所有 ***还未修改过的*** 文件都会被更新。 而 `reset --hard` 则会不做检查就全面地替换所有东西。
- `reset` 是移动 HEAD 分支的指向，而 `checkout`只会移动 HEAD 自身来指向另一个分支。

**带文件路径** `**git checkout [branch] file**`

会像 `reset` 一样不会移动 HEAD。 它就像 `git reset [branch] file` 那样用该次提交中的那个文件来更新索引，但是它也会覆盖工作目录中对应的文件。 它就像是 `git reset --hard [branch] file`（如果 `reset` 允许你这样运行的话）， 这样对工作目录并不安全，它也不会移动 HEAD。

|  | HEAD | Index | Workdir | WD Safe? |
| --- | --- | --- | --- | --- |
| Commit Level |  |  |  |  |
| reset --soft [commit] | REF | NO | NO | YES |
| reset [commit] | REF | YES | NO | YES |
| reset --hard [commit] | REF | YES | YES | NO |
| checkout <commit> | HEAD | YES | YES | YES |
| File Level |  |  |  |  |
| reset [commit] <paths> | NO | YES | NO | YES |
| checkout [commit] <paths> | NO | YES | YES | NO |

### 合并实践

一般Git中整合来自不同分支的修改主要有两种方式，merge 与 rebase。

**Merge**：基于两分支公共组件C2，以及分支各自最新版本C4与C5，三方合并产出C6。

![Untitled](https://image.freefe.cc/in-deep-practice-of-Git%2FUntitled%2014.png)

![Untitled](https://image.freefe.cc/in-deep-practice-of-Git%2FUntitled%2015.png)

**Rebase（变基）**：提取C4内的补丁与修改，在C3的基础上进行应用。

![Untitled](https://image.freefe.cc/in-deep-practice-of-Git%2FUntitled%2016.png)

![Untitled](https://image.freefe.cc/in-deep-practice-of-Git%2FUntitled%2017.png)

无论是通过变基，还是通过三方合并，整合的最终结果所指向的快照始终是一样的，只不过提交历史不同罢了。 变基是将一系列提交按照原有次序依次应用到另一分支上，而合并是把最终结果合在一起。

## Git 相关命令说明

### git rebase

git 中的一种merge方式，merge 命令为基于两分支的最新快照以及共同的祖先快照进行三方的合并，生成一个新的快照并且提交。而 rebase 命令则提取补丁与修改后在原有的历史上生成新的提交。最终结果产物一致而历史不同。

简单模式下除了历史都一致，而复杂场景下需要注意若 merge 与 rebase 混合使用可能会存在历史合并的一些问题。

【拉取更新远程分支代码时，记得使用 git pull - -rebase， 避免 pull 的直接 merge 操作】

![Untitled](https://image.freefe.cc/in-deep-practice-of-Git%2FUntitled%2018.png)

![Untitled](https://image.freefe.cc/in-deep-practice-of-Git%2FUntitled%2017.png)

### git stash

当在某一分支进行开发后，内容则进入开发状态，此时若需要切换分支做其他高优事宜，而当前代码状态又不适合做一个提交时，则可使用 stash 就行贮藏。其将当前工作目录以及暂存区的内容进行存储，在必要时候可以就行提取。

```jsx
// 当前状态
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	modified:   activity.config.js

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   babel.config.js

// 贮藏操作
$ git stash
Saved working directory and index state WIP on master: 77d2c3e test 1 123

// 再次查看状态
$ git status
On branch master
nothing to commit, working tree clean

// 查看贮藏列表
$ git stash list
stash@{0}: WIP on master: 77d2c3e test

// 当前分支应用贮藏
$ git stash apply
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   activity.config.js
	modified:   babel.config.js

no changes added to commit (use "git add" and/or "git commit -a")
```

### git cherry-pick

将指定单个或者多个提交应用于当前分支。参数也可以为分支，将指定分支的最新的提交应用于当前分支。

```jsx
$ git cherry-pick <commitHash>

// 目前分支提交状态
a - b - c - d   Master
         \
           e - f - g Feature

# 切换到 master 分支
$ git checkout master

# Cherry pick 操作
$ git cherry-pick f

// 操作后分支提交状态
a - b - c - d - f   Master
         \
           e - f - g Feature
```

### git reset

在上文重置的实践中已经较为详细的说明，

- **`--soft`** 移动HEAD分支的指向，保留工作区与暂存区reset前的内容
- **`--mixed`** 移动HEAD分支的指向，并且将Index（暂存区）内容重置对齐 HEAD
- **`--hard`**  移动HEAD分支的指向，并且将Index（暂存区）以及工作目录区内容重置对齐 HEAD

![初始状态](https://image.freefe.cc/in-deep-practice-of-Git%2FUntitled%207.png)

![—soft 模式状态](https://image.freefe.cc/in-deep-practice-of-Git%2FUntitled%208.png)

![—mixed 模式状态，默认状态](https://image.freefe.cc/in-deep-practice-of-Git%2FUntitled%209.png)

![--hard](https://image.freefe.cc/in-deep-practice-of-Git%2FUntitled%2010.png)

其他任何形式的 `reset` 调用都可以轻松撤消，但是 `--hard` 选项不能，因为它强制覆盖了工作目录中的文件。 在这种特殊情况下，我们的 Git 数据库中的一个提交内还留有该文件的 **v3** 版本， 我们可以通过 `reflog` 来找回它。

### git revert

revert也是用于回滚某一个（或多个）提交引入的更改。与reset回退HEAD分支指针不同，revert 是对于具体某次提交做一次完全相反的操作来达到撤销的操作，而不会对历史的提交做任何的修改。

![git revert 动图](https://camo.githubusercontent.com/3b53e7318fb979b099ceeff8e9596c6b64e0565fa91cee31d0eb9cd2e46c56da/68747470733a2f2f75706c6f61642d696d616765732e6a69616e7368752e696f2f75706c6f61645f696d616765732f31323839303831392d376463396333366235626538363161642e6769663f696d6167654d6f6772322f6175746f2d6f7269656e742f7374726970)

### **git restore**

restore 是 git2.23版本新增的命令，专门用来还原以及撤销修改，恢复工作区域文件。其行为和 checkout file 功能一致，由于 checkout 命令职责较多，所以有了特定的 restore 命令。【类似 switch 命令作为切换分支的命令】

```jsx
$ git status
On branch master
Changes to be committed:
  **(use "git restore --staged <file>..." to unstage)**
	modified:   package.json

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  **(use "git restore <file>..." to discard changes in working directory)**
	modified:   activity.config.js
	modified:   babel.config.js
```

### git 基础命令汇总

```jsx
$ git --help
usage: git [--version] [--help] [-C <path>] [-c <name>=<value>]
           [--exec-path[=<path>]] [--html-path] [--man-path] [--info-path]
           [-p | --paginate | -P | --no-pager] [--no-replace-objects] [--bare]
           [--git-dir=<path>] [--work-tree=<path>] [--namespace=<name>]
           <command> [<args>]

These are common Git commands used in various situations:

start a working area (see also: git help tutorial)
   clone     Clone a repository into a new directory
   init      Create an empty Git repository or reinitialize an existing one

work on the current change (see also: git help everyday)
   add       Add file contents to the index
   mv        Move or rename a file, a directory, or a symlink
   restore   Restore working tree files
   rm        Remove files from the working tree and from the index

examine the history and state (see also: git help revisions)
   bisect    Use binary search to find the commit that introduced a bug
   diff      Show changes between commits, commit and working tree, etc
   grep      Print lines matching a pattern
   log       Show commit logs
   show      Show various types of objects
   status    Show the working tree status

grow, mark and tweak your common history
   branch    List, create, or delete branches
   commit    Record changes to the repository
   merge     Join two or more development histories together
   rebase    Reapply commits on top of another base tip
   reset     Reset current HEAD to the specified state
   switch    Switch branches
   tag       Create, list, delete or verify a tag object signed with GPG

collaborate (see also: git help workflows)
   fetch     Download objects and refs from another repository
   pull      Fetch from and integrate with another repository or a local branch
   push      Update remote refs along with associated objects
```

## Git内部实现模式

根本性上来讲，Git是一个内容寻址（content-addressable）的文件系统。所有的关于版本、分支等信息，均在项目的 .git 文件夹下。

```jsx
config      // 包含项目特有的配置选项
description // 仅供 GitWeb 程序使用，我们无需关心
HEAD        // 指向目前被检出的分支
hooks/      // 包含客户端或服务端的钩子脚本（hook scripts）
info/       // 包含一个全局性排除（global exclude）文件， 用以放置那些不希望被记录在 .gitignore 文件中的忽略模式（ignored patterns）
objects/     // 目录存储所有数据内容
refs/        // 目录存储指向数据（分支、远程仓库和标签等）的提交对象的指针
```

### 数据对象

Git 的核心就是一个简单的键值对数据存储系统，你向 Git 仓库存储任意类型内容，就会返回一个对应的 key，通过 key 可以再次取回内容。

```jsx
// 无需关心 git 的一些底层命令，此处只是展现 Git 数据存储的模式

// git hash-object 创建一个新的数据对象并将它手动存入你的新 Git 数据库
$ echo 'test content' | git hash-object -w --stdin
d670460b4b4aece5915caf5c68d12f560a9fe3e4

// 查看 objects 目录
$ find .git/objects -type f
.git/objects/d6/70460b4b4aece5915caf5c68d12f560a9fe3e4

// 通过 cat-file 命令从 Git 那里取回数据，-p 选项可指示该命令自动判断内容的类型
$ git cat-file -p d670460b4b4aece5915caf5c68d12f560a9fe3e4
test content

// 简单的版本控制
$ echo 'version 1' > test.txt
$ git hash-object -w test.txt
83baae61804e65cc73a7201a7252750c76066a30

$ echo 'version 2' > test.txt
$ git hash-object -w test.txt
1f7a7a472abf3dd9643fd615f6da379c4acb3e3a

// 对象数据库记录下了该文件的两个不同版本
$ find .git/objects -type f
.git/objects/1f/7a7a472abf3dd9643fd615f6da379c4acb3e3a
.git/objects/83/baae61804e65cc73a7201a7252750c76066a30
.git/objects/d6/70460b4b4aece5915caf5c68d12f560a9fe3e4

// 删掉 test.txt 的本地副本，然后用 Git 从对象数据库中取回它的第一个版本
$ git cat-file -p 83baae61804e65cc73a7201a7252750c76066a30 > test.txt
$ cat test.txt
version 1

// 文件名并没有被保存——我们仅保存了文件的内容
// 利用 git cat-file -t 命令，可以让 Git 告诉我们其内部存储的任何对象类型
$ git cat-file -t 1f7a7a472abf3dd9643fd615f6da379c4acb3e3a
blob
```

自此，我们大致能理解了我们项目里所有的文件的基本存储方式。不过有一点，我们只是存储了所有文件的二进制内容，那么又是在哪里存储整体项目所有文件对于的具体的版本号（key）以及文件内容更新后的版本号（key）呢？

### 树对象

答案是，上述的blob类型用来存储文件的具体内容，另一种树对象类型用来存储我们所需要的映射map，解决文件名保存的问题，也允许我们将多个文件组织到一起。

一个树对象包含了一条或多条树对象记录（tree entry），每条记录含有一个指向数据对象或者子树对象的 SHA-1 指针，以及相应的模式、类型、文件名信息。

```jsx
$ git cat-file -p master^{tree}
100644 blob a906cb2a4a904a152e80877d4088654daad0c859      README
100644 blob 8f94139338f9404f26296befa88755fc2598c289      Rakefile
040000 tree 99f1a6d12cb4b6f19c8655fca46c3ecf317074e0      lib
```

![**简化版的 Git 数据模型**](https://image.freefe.cc/in-deep-practice-of-Git%2FUntitled%2019.png)

**简化版的 Git 数据模型**

```jsx
// 底层命令 git update-index 为一个单独文件——我们的 test.txt 文件的首个版本——创建一个暂存区
// --cacheinfo 选项，因为将要添加的文件位于 Git 数据库中，而不是位于当前目录下
$ git update-index --add --cacheinfo 100644 \ // 文件模式
  83baae61804e65cc73a7201a7252750c76066a30 test.txt

// 通过 git write-tree 命令将暂存区内容写入一个树对象
$ git write-tree
d8329fc1cc938780ffdd9f94e0d364e0ea74f579
$ git cat-file -p d8329fc1cc938780ffdd9f94e0d364e0ea74f579
100644 blob 83baae61804e65cc73a7201a7252750c76066a30      test.txt

// test.txt 文件的第二个版本，以及一个新的文件
$ echo 'new file' > new.txt
$ git update-index --add --cacheinfo 100644 \
  1f7a7a472abf3dd9643fd615f6da379c4acb3e3a test.txt
$ git update-index --add new.txt

$ git write-tree
0155eb4229851634a0f03eb265b69f5a2d56f341
$ git cat-file -p 0155eb4229851634a0f03eb265b69f5a2d56f341
100644 blob fa49b077972391ad58037050f2a75f74e3671e92      new.txt
100644 blob 1f7a7a472abf3dd9643fd615f6da379c4acb3e3a      test.txt

// 你可以将第一个树对象加入第二个树对象，使其成为新的树对象的一个子目录。
$ git read-tree --prefix=bak d8329fc1cc938780ffdd9f94e0d364e0ea74f579
$ git write-tree
3c4e9cd789d88d8d89c1073707c3585e41b0e614
$ git cat-file -p 3c4e9cd789d88d8d89c1073707c3585e41b0e614
040000 tree d8329fc1cc938780ffdd9f94e0d364e0ea74f579      bak
100644 blob fa49b077972391ad58037050f2a75f74e3671e92      new.txt
100644 blob 1f7a7a472abf3dd9643fd615f6da379c4acb3e3a      test.txt
```

好的，树对象与二进制文件则很好的支持了我们整体的项目结构与文件内容，那么对于分支，版本等更高层级的功能，又是如何实现的？

 

### 提交对象

谁保存了这些快照，在什么时刻保存的，以及为什么保存这些快照。 而以上这些，正是提交对象（commit object）能为你保存的基本信息。也是实现分支，版本等最基础的原理支持。

```jsx
$ echo 'first commit' | git commit-tree d8329f
fdf4fc3344e67ab068f836878b6c4951e3b15f3d

// 通过 git cat-file 命令查看这个新提交对象
$ git cat-file -p fdf4fc3
tree d8329fc1cc938780ffdd9f94e0d364e0ea74f579
author Scott Chacon <schacon@gmail.com> 1243040974 -0700
committer Scott Chacon <schacon@gmail.com> 1243040974 -0700

first commit

// 我们将创建另两个提交对象，它们分别引用各自的上一个提交（作为其父提交对象）
$ echo 'second commit' | git commit-tree 0155eb -p fdf4fc3
cac0cab538b970a37ea1e769cbbde608743bc96d
$ echo 'third commit'  | git commit-tree 3c4e9c -p cac0cab
1a410efbd13591db07496601ebc7a059dd55cfe9
```

在我们运行 `git add` 和 `git commit` 时，Git 的本质就是将改写的文件保存为数据对象，更新暂存区所指向的树对象，最后创建一个含有树对象和提交信息的提交对象。数据对象、树对象和提交对象都以独立的文件的形式保存在 `.git/objects` 目录下。

```jsx
$ find .git/objects -type f
.git/objects/01/55eb4229851634a0f03eb265b69f5a2d56f341 # tree 2
.git/objects/1a/410efbd13591db07496601ebc7a059dd55cfe9 # commit 3
.git/objects/1f/7a7a472abf3dd9643fd615f6da379c4acb3e3a # test.txt v2
.git/objects/3c/4e9cd789d88d8d89c1073707c3585e41b0e614 # tree 3
.git/objects/83/baae61804e65cc73a7201a7252750c76066a30 # test.txt v1
.git/objects/ca/c0cab538b970a37ea1e769cbbde608743bc96d # commit 2
.git/objects/d6/70460b4b4aece5915caf5c68d12f560a9fe3e4 # 'test content'
.git/objects/d8/329fc1cc938780ffdd9f94e0d364e0ea74f579 # tree 1
.git/objects/fa/49b077972391ad58037050f2a75f74e3671e92 # new.txt
.git/objects/fd/f4fc3344e67ab068f836878b6c4951e3b15f3d # commit 1
```

![**Git 目录下所有可达的对象**](https://image.freefe.cc/in-deep-practice-of-Git%2FUntitled%2020.png)