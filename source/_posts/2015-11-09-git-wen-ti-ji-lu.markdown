---
layout: post
title: "Git-问题记录"
date: 2015-11-09 15:39:22 +0800
comments: true
categories: 
---

在做项目的过程中，遇到很多关于Git相关的问题，记录下来与大家分享，也便于自己回顾。

#### 介绍一个成功的 Git 分支模型

当自己从 SVN 转到 Git 后，首先面临的就是创建Git项目，参照网上给出的Git 模型，来创建合理的分支，实际项目也是参照于下面这篇文章，创建了 master 和 develop 本地分支和远程分支，然后每个开发者都有自己的本地分支。
<!--more-->
[参照Git模型](http://www.oschina.net/translate/a-successful-git-branching-model)

#### 搜索已执行的命令

 terminal 下, `control + r` 可以搜索已执行的命令，然后按 `ESC` ，回车

#### 配置用户名和邮箱

全局的通过vim ~/.gitconfig来查看

```
git config --global user.name "Your Name"
gigit config --global user.email you@example.com
```
      
局部的通过当前路径下的 .git/config文件来查看

```
git config user.name "Your Name"
git config user.email you@example.com
```

修改最后一次提交的用户名和邮箱地址

```
git commit --amend --author='Your Name <you@example.com>'

```
#### Git使用gitignore建立项目过滤规则

 git中提供两种过滤机制，一种是全局过滤机制，即对所有的git都适用；另一种是针对某个项目使用的过滤规则。

`问题:`
`.gitignore` 只适用于尚未添加到 `git` 库的文件。如果已经添加了，则需用 `git rm` 移除后再重新 `commit`。


#### 删除文件

```
git rm -r .
rm -rf HIFramework
git rm HIFramework
rm 'HIFramework'
git commit -m 'delete'
```

#### 远程仓库相关命令


	检出仓库：$ git clone git://github.com/jquery/jquery.git
	
	查看远程仓库：$ git remote -v
	
	添加远程仓库：$ git remote add [name] [url]
	
	删除远程仓库：$ git remote rm [name]
	
	修改远程仓库：$ git remote set-url --push [name] [newUrl]
	
	拉取远程仓库：$ git pull [remoteName] [localBranchName]
	
	推送远程仓库：$ git push [remoteName] [localBranchName]
	
	强制推送更改：$ git push --force origin master
	

如果想把本地的某个分支test提交到远程仓库，并作为远程仓库的master分支，或者作为另外一个名叫test的分支，如下：

```
$ git push origin test:master         // 提交本地test分支作为远程的master分支
$ git push origin test:test              // 提交本地test分支作为远程的test分支
```




#### 分支(branch)操作相关命令


	查看本地分支：$ git branch -av
	查看远程分支：$ git branch -r （如果还是看不到就先 git fetch origin 先）
	创建本地分支：$ git branch [name] ----注意新分支创建后不会自动切换为当前分支
	切换分支：$ git checkout [name]
	创建新分支并立即切换到新分支：$ git checkout -b [name]
	直接检出远程分支：$ git checkout -b [name] [remoteName] (如：git checkout -b myNewBranch origin/dragon)
	删除分支：$ git branch -d [name] ---- -d选项只能删除已经参与了合并的分支，对于未有合并的分支是无法删除的。如果想强制删除一个分支，可以使用-D选项
	合并分支：$ git merge [name] ----将名称为[name]的分支与当前分支合并
	合并最后的2个提交：$ git rebase -i HEAD~2 ---- 数字2按需修改即可（如果需提交到远端$ git push -f origin master 慎用！）
	创建远程分支(本地分支push到远程)：$ git push origin [name]
	删除远程分支：$ git push origin :heads/[name] 或 $ git push origin :[name] 
	修改分支名称：git branch -m <old_branch_name> <new_branch_name>  

创建空的分支：(执行命令之前记得先提交你当前分支的修改，否则会被强制删干净没得后悔)

```
$ git symbolic-ref HEAD refs/heads/[name]
$ rm .git/index
$ git clean -fdx
```


#### 版本(tag)操作相关命令

	
	查看版本：$ git tag
	
	创建版本：$ git tag [name]
	
	删除版本：$ git tag -d [name]
	
	查看远程版本：$ git tag -r
	
	创建远程版本(本地版本push到远程)：$ git push origin [name]
	
	删除远程版本：$ git push origin :refs/tags/[name]
	
	合并远程仓库的tag到本地：$ git pull origin --tags
	
	上传本地tag到远程仓库：$ git push origin --tags
	
	创建带注释的tag：$ git tag -a [name] -m 'yourMessage'

#### 删除和回退

	删除当前仓库内未受版本管理的文件：$ git clean -f
	
	恢复仓库到上一次的提交状态：$ git reset --hard
	
	回退所有内容到上一个版本：$ git reset HEAD^
	
	回退a.py这个文件的版本到上一个版本：$ git reset HEAD^ a.py
	
	回退到某个版本：$ git reset 057d 
	
	将本地的状态回退到和远程的一样：$ git reset –hard origin/master  
	
	向前回退到第3个版本：$ git reset –-soft HEAD~3
	
	修改最后的提交日志：$ git commit --amend
	
	也可以修改提交的用户名和Email：
	
	git commit --amend --author='Your Name <you@example.com>'


#### 查看提交日志
使用 $ git log --oneline --graph --name-status 既可以看到简介的日志信息，也可以看到改了哪些文件，一举两得：

暂时就是这么多了，持续更新中，待续。。。