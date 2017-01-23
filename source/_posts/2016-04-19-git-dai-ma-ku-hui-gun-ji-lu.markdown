---
layout: post
title: "Git 代码库回滚记录"
date: 2016-04-19 16:32:03 +0800
comments: true
categories: 
---

项目有了一些新需求，需要做代码更改了。Pull 下来代码，结果运行不了，最近项目更新不是很频繁，所以想到了使用 Git 代码库回滚，记录一下。代码库回滚可以包含本地带啊库回滚和远程代码库回滚，下面针对这两种情况做一下使用介绍。

<!--more-->


git 代码库回滚指的是将代码库某分支退回到以前的某个 `commit id`

### 本地代码库回滚

找到你要回复到的版本的 `commit-id`，然后使用下面命令进行恢复。

```
git reset --hard commit-id //回滚到commit-id，将commit-id之后提交的commit进行清除

git reset --hard HEAD~3 //将最近3次的提交回滚
```

本地代码库回滚之后，做了新的更改，然后需要提交到远程分支，提交之前你需要 pull，这样问题就出来了，你又会把坏的、不想要的代码合并过来，这时候就需要使用远程代码库回滚了。
 

### 远程代码库回滚

过程代码库回滚要比本地代码库回滚要复杂，但是原理其实比较简单：先将本地分支退回到某个commit，然后删除远程分支，最后重新push本地分支。

具体操作步骤：

```
1、git checkout the_branch

2、git pull

3、git reset --hard the_commit_id //把the_branch本地回滚到the_commit_id

4、git push origin :the_branch //删除远程 the_branch(冒号前面的空格不能少，原理是把一个空分支push到server上，相当于删除该分支。)

5、git push origin the_branch //用回滚后的本地分支重新建立远程分支

```

这样就算是解决了问题了，希望能帮助到你。

