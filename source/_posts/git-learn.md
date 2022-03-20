---
title: Git 简单理解学习
date: 2016-09-22
tags: 
- git
- git 命令
- git 基础
- git 学习
---

### Git 基础
![](http://image.freefe.cc/20160922141.png)

*   Workspace：工作区
*   Index / Stage：暂存区
*   Repository：仓库区（或本地仓库）
*   Remote：远程仓库

### Git 命令

### 一、新建代码库

```
$ git init ([project-name]) 
# 在当前目录新建一个Git代码库(新建一个目录，将其初始化为Git代码库) 

$ git clone [url]
# 下载一个项目和它的整个代码历史
```

### 二、配置

Git的设置文件为.gitconfig，它可以在用户主目录下（全局配置），也可以在项目目录下（项目配置）。

```
$ git config –list
# 显示当前的Git配置 

$ git config -e [--global]
# 编辑Git配置文件
```

### 三、增加/删除文件

```
$ git add ./[dir]/[file1] [file2] ...   
# 添加所有文件/指定目录/指定文件到暂存区 

$ git rm [file1] [file2] ...    
# 删除工作区文件，并且将这次删除放入暂存区

$ git mv [file-original] [file-renamed] 
# 改名文件，并且将这个改名放入暂存区
```

### 四、代码提交

```
$ git commit -m [message]   # 提交暂存区到仓库区 
$ git commit [file1] [file2] ... -m [message] # 提交暂存区的指定文件到仓库区 

$ git commit -a     # 提交工作区自上次commit之后的变化，直接到仓库区 
$ git commit -v     # 提交时显示所有diff信息 

$ git commit --amend -m [message] 
# 使用一次新的commit，替代上一次提交，如果代码没有任何新变化，则用来改写上一次commit的提交信息 
$ git commit --amend [file1] [file2] ...    
# 重做上一次commit，并包括指定文件的新变化
```

### 五、分支

```
$ git branch /-r/-a 
# 列出所有本地分支/所有远程分支/所有本地分支和远程分支

$ git branch [branch-name]/[branch] [commit]
# 新建一个分支/新建一个分支 指向指定commit，但依然停留在当前分支 

$ git checkout -b [branch]  
# 新建一个分支，并切换到该分支 

$ git branch --track [branch] [remote-branch] 
# 新建一个分支，与指定的远程分支建立追踪关系 

$ git checkout [branch-name]    
# 切换到指定分支，并更新工作区 

$ git branch --set-upstream [branch] [remote-branch] 
# 建立追踪关系，在现有分支与指定的远程分支之间 

$ git merge [branch]    
# 合并指定分支到当前分支 

$ git branch -d [branch-name]   
# 删除分支 

$ git push origin --delete [branch-name]    
# 删除远程分支
```

### 六、标签

```
$ git tag       
# 列出所有tag 
$ git tag [tag] ([commit])  
# 新建一个tag在当前commit(在指定commit) 
$ git tag -d [tag]      
# 删除本地tag 
$ git push origin :refs/tags/[tagName]   
# 删除远程tag 
$ git show [tag]            
# 查看tag信息 
$ git push [remote] [tag]    
# 提交指定tag 
$ git push [remote] --tags   
# 提交所有tag 
$ git checkout -b [branch] [tag]     
# 新建一个分支，指向某个tag
```

### 七、查看信息

```
$ git status     
# 显示有变更的文件 
$ git log    
# 显示当前分支的版本历史 
$ git log --stat     
# 显示commit历史，以及每次commit发生变更的文件 
$ git log -S [keyword]   
# 搜索提交历史，根据关键词 
$ git log [tag] HEAD --pretty=format:%s 
# 显示某个commit之后的所有变动，每个commit占据一行 
$ git log [tag] HEAD --grep feature 
# 显示某个commit之后的所有变动，其"提交说明"必须符合搜索条件
```

### 八、远程同步

```
$ git fetch [remote]        
# 下载远程仓库的所有变动 
$ git remote -v             
# 显示所有远程仓库 
$ git remote show [remote]  
# 显示某个远程仓库的信息 
$ git remote add [shortname] [url]
# 增加一个新的远程仓库，并命名 

$ git pull [remote] [branch]    
# 取回远程仓库的变化，并与本地分支合并 
$ git push [remote] [branch]    
# 上传本地指定分支到远程仓库 

$ git rebase    
# 从上游分支获取最新commit信息，并有机的将当前分支和上游分支进行合并
```

### 九、撤销

```
$ git checkout [file]    
# 恢复暂存区的指定文件到工作区 
$ git checkout [commit] [file] 
# 恢复某个commit的指定文件到暂存区和工作区
$ git checkout .         
# 恢复暂存区的所有文件到工作区 

$ git reset [file]   
# 重置暂存区的指定文件，与上一次commit保持一致，但工作区不变 
$ git reset --hard   
# 重置暂存区与工作区，与上一次commit保持一致 
$ git reset [commit] 

$ git revert [commit]    
# 后者的所有变化都将被前者抵消，并且应用到当前分支
```

### Git 命令图解

### git reset

reset将一个分支的末端指向另一个提交。这可以用来移除当前分支的一些提交。``git reset HEAD~2``![](http://image.freefe.cc/20160922142.png) ![](http://image.freefe.cc/20160922143.png)

### git checkout

将HEAD移到一个新的分支

```
`git checkout Hotfix`
```

![](http://image.freefe.cc/20160922144.png) ![](http://image.freefe.cc/20160922145.png)

### git revert

Revert撤销一个提交的同时会创建一个新的提交。这是一个安全的方法，因为它不会重写提交历史``git revert HEAD~2``![](http://image.freefe.cc/20160922146.png) ![](http://image.freefe.cc/20160922147.png)

### git rebase

基于已有提交的远程分支"origin"，创建一个叫"mywork"的分支 再在这个分支做一些修改，然后生成两个提交(commit)，并且同时远程分支也有其他同学的提交，这时候 ![](http://image.freefe.cc/20160922148.png) ![](http://image.freefe.cc/20160922149.png) 在此，一般我们都会用"pull"命令把"origin"分支上的修改拉下来与当前的分钟修改合并，其结果看起来就像一个新的"合并的提交"(merge commit) ![](http://image.freefe.cc/2016092214a.png) 如果使用 git rebase，会把你的"mywork"分支里的每个提交(commit)取消掉，并且把它们临时 保存为补丁(patch)(这些补丁放到".git/rebase"目录中),然后把"mywork"分支更新 为最新的"origin"分支，最后把保存的这些补丁应用到"mywork"分支上 ![](http://image.freefe.cc/2016092214b.png)

### git merge --no-ff

--no-ff 指的是强行关闭fast-forward方式 ![](http://image.freefe.cc/2016092214c.png)
### Git 分支
Git 保存的不是文件差异或者变化量，而只是一系列文件快照 Git 仓库中有五个对象：三个表示文件快照内容的 blob 对象；一个记录着目录树内容及其中各个文件对应 blob 对象索引的 tree 对象；以及一个包含指向 tree 对象（根目录）的索引和其他提交信息元数据的 commit 对象。 ![](http://image.freefe.cc/2016092214d.png) 作些修改后再次提交，那么这次的提交对象会包含一个指向上次提交对象的指针 ![](http://image.freefe.cc/2016092214e.png) Git 中的分支，其实本质上仅仅是个指向 commit 对象的可变指针。Git 会使用 master 作为分支的默认名字。在若干次提交后，你其实已经有了一个指向最后一次提交对象的 master 分支，它在每次提交的时候都会自动向前移动。 Git 是如何知道你当前在哪个分支上工作的呢？其实答案也很简单，它保存着一个名为 HEAD 的特别指针 ![](http://image.freefe.cc/2016092214f.png)
### Git 分支图解

```
$ git checkout -b iss53
```

![](http://image.freefe.cc/2016092214g.png)

```
$ vim a.js 
$ git commit -a -m 'added a new footer [issue 53]'
```

![](http://image.freefe.cc/2016092214h.png)

```
$ git checkout master
$ git checkout -b 'hotfix'
$ vim index.html 
$ git commit -a -m 'fixed the broken email address'
```

![](http://image.freefe.cc/2016092214i.png)

```
$ git checkout master 
$ git merge hotfix
```

![](http://image.freefe.cc/2016092214j.png)

```
$ git branch -d hotfix
$ git checkout iss53 
$ vim index.html 
$ git commit -a -m 'finished the new footer [issue 53]'
```

![](http://image.freefe.cc/2016092214k.png)

```
$ git checkout master 
$ git merge iss53
```

![](http://image.freefe.cc/2016092214l.png)
### Git 分支的艺术
`1、 GIT在技术层面上，是一个无中心的分布式版本控制系统，但在管理层面上，建议保持一个中心版本库。` ![](http://image.freefe.cc/2016092214m.png) `2、 建议一个中心版本库(origin)至少包括两个分支，即“主分支(master)”和“开发分支(develop)”` 要确保：团队成员从主分支(master)获得的都是处于可发布状态的代码，而从开发分支(develop)应该总能够获得最新开发进展的代码。 ![](http://image.freefe.cc/2016092214n.png)

> 3、在一个团队开发协作中，建议要有“辅助分支”的概念。 “辅助分支”，大体包括如下几类：“管理功能开发”的分支、“帮助构建可发布代码”的分支、“可以便捷的修复发布版本关键BUG”的分支。 “辅助分支”的最大特点就是“生命周期十分有限”，完成使命后即可被清除。

建议至少还应设置三类“辅助分支”，我们称之为“Feature branches”，“Release branches”，“Hotfix branches”。 ![](http://image.freefe.cc/2016092214o.png) `4、 “Feature branches”，起源于develop分支，最终也会归于develop分支。`

> “Feature branches”常用于开发一个独立的新功能，且其最终的结局必然只有两个，其一是合并入“develop”分支，其二是被抛弃。最典型的“Fearture branches”一定是存在于团队开发者那里，而不应该是“中心版本库”中。

“Feature branches”起源于“develop”分支，实现方法是：

```
git checkout -b myfeature develop
```

“Feature branches”最终也归于“develop”分支，实现方式是：

```
git checkout devleop
git merge --no-ff myfeature（--no-ff，即not fast forward，其作用是：要求git merge即使在fast forward条件下也要产生一个新的merge commit）（采用--no-ff的方式进行分支合并，其目的在于，希望保持原有“Feature branches”整个提交链的完整性）
git branch -d myfeature
git push origin develop
```

![](http://image.freefe.cc/2016092214p.png) `5、“Release branch”，起源于develop分支，最终归于“develop”或“master”分支。这类分支建议命名为“release-*”`

> “Relase branch”通常负责“短期的发布前准备工作”、“小bug的修复工作”、“版本号等元信息的准备工作”。与此同时，“develop”分支又可以承接下一个新功能的开发工作了 “Release branch”产生新提交的最好时机是“develop”分支已经基本到达预期的状态，至少希望新功能已经完全从“Feature branches”合并到“develop”分支了

创建“Release branches”，方法是：

```
git checkout -b release-2 develop./bump-version.sh 2 （这个脚本用于将代码所有涉及版本信息的地方都统一修改到2，另外，需要用户根据自己的项目去编写适合的bump-version.sh）
git commit -a -m "Bumped version number to 2"
```

在一段短时间内，在“Release branches”上，我们可以继续修复bug。在此阶段，严禁新功能的并入，新功能应该是被合并到“develop”分支的。 经过若干bug修复后，“Release branches”上的代码已经达到可发布状态，此时，需要完成三个动作：

*   第一是将“Release branches”合并到“master”分支
*   第二是一定要为master上的这个新提交打TAG（记录里程碑）
*   第三是要将“Release branches”合并回“develop”分支。 git checkout mastergit merge --no-ff release-1.2git tag -a 1.2（使用-u/-s/-a参数会创建tag对象，而非软tag） git checkout developgit merge --no-ff release-1.2git branch -d release-1.2

`6、“Hotfix branches”源于“master”，归于“develop”或“master”，通常命名为“hotfix-*”`

> “Hotfix branches”类似于“Release branch”，但产生此分支总是非预期的关键BUG。

建议设立“Hotfix branches”的原因是：希望避免“develop分支”新功能的开发必须为BUG修复让路的情况。 建立“Hotfix branches”，方法是：

```
git checkout -b hotfix-1 master./bump-version.sh 1
git commit -a -m "Bumpt version to 1" （然后可以开始问题修复工作）
git commit -m "Fixed severe production problem" （在问题修复后，进行第二次提交）
```

BUG修复后，需要将“Hotfix branches”合并回“master”分支，同时也需要合并回“develop”分支，方法是：

```
git checkout mastergit merge --no-ff hotfix-1
git tag -a 1
git checkout developgit merge --no-ff hotfix-1
git branch -d hotfix-1
```

![](http://image.freefe.cc/2016092214q.png)