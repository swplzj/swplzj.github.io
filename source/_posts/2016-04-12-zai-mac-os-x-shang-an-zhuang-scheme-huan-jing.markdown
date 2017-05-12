---
layout: post
title: "在 Mac OS X 上安装 Scheme 环境"
date: 2016-04-12 15:24:43 +0800
comments: true
categories: 
---

关于 SICP 这本书的讨论很多，该不该学就看自己决定，今天闲着无聊，就想学一下这本书，当然，首先还是配置运行环境，在折腾的过程中遇到一些问题，记录一下在 Mac OS X 上面安装 Scheme 的过程，或许其他人也会面临这样的问题，所以和大家分享一下学到的知识。

<!--more-->

### 说在前面

SICP 全称 Structure and Interpretation of Computer Programs，翻译过来叫《计算机程序的构造和解释》。SICP 要教你的不是那些细枝末节的东西，而是把你拉到一个极高的视角，一边分解程序，一边教你如何正确的构建和编写程序。具体来说，SCIP 教你如何熟练运用抽象以及程序如何运行。

### 安装 Scheme 过程

在安装过程中，遇到了其他安装不了的情况，现在就直奔主题，直接展示正确的安装方式。

#### 1. 下载并安装 MIT Scheme 程序

[32-bit system](http://ftp.gnu.org/gnu/mit-scheme/stable.pkg/9.2/mit-scheme-9.2-i386.dmg)

[64-bit system](http://ftp.gnu.org/gnu/mit-scheme/stable.pkg/9.2/mit-scheme-9.2-x86-64.dmg)

在 Finder 中找到安装包，双击安装包就会在新窗口出现像下面的画面：

{% img center /2016_04_12_mit-scheme-dmg.png / %}

你只需要拖拽 MIT/GNU Scheme.app 到 Applications 文件夹中，然后就会自动安装到你的系统里面。如果你以为到此就大功告成了，那么你大错特错了。当你打开该应用时，是没有任何反应的，莫着急，还需一步步来。

#### 2. 通过终端对 Scheme 来进行配置

打开终端，然后运行下面的命令：


`For 32-bit package:`

	sudo ln -s /Applications/MIT\:GNU\ Scheme.app/Contents/Resources /usr/local/lib/mit-scheme-i386
	
`For 64-bit package:`


	sudo ln -s /Applications/MIT\:GNU\ Scheme.app/Contents/Resources /usr/local/lib/mit-scheme-x86-64
	
你需要输入你的管理员密码，然后再在终端输入下面命令：	


`For 32-bit package:`

	sudo ln -s /usr/local/lib/mit-scheme-i386/mit-scheme /usr/bin/scheme

`For 64-bit package:`


	sudo ln -s /usr/local/lib/mit-scheme-x86-64/mit-scheme /usr/bin/scheme

`注意：`
可能上面的命令执行不成功，不过没关系，还有替换方案，你可以将上面的 ‘/usr/bin/scheme' 修改为  ‘/usr/local/bin/scheme’，然后再试试。

#### 3. 开始使用 Scheme

使用 Sublime Text 写了一个测试的Demo，然后跑一下：

{% img center /2016_04_12_use-mit-scheme.png / %}

### 写在后面

生命在于折腾，喜欢折腾一些带给人美好的东西，无聊的时候虐一虐自己的大脑也是很有意思的，接下来要把 SICP 这本难啃的书啃完！祝福我自己。
希望对你有帮助。


