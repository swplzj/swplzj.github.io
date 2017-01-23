---
layout: post
title: "iOS 应用程序目录结构"
date: 2014-03-13 21:22:19 +0800
comments: true
categories: 
---
iOS应用程序采用的是沙盒原理设计，普及一下知识：沙盒是在受限的安全环境中运行应用程序的一种做法，这种做法是要限制授予应用程序的代码访问权限。

<!--more-->

具体的特点有三点：

1. 每个应用程序都有自己的存储空间； 
2. 应用程序不能翻过自己的围墙去访问别的存储空间的内容；
3. 应用程序请求的数据都要通过权限检测，假如不符合条件的话，不会被放行。

在Finder上点->前往->前往文件夹，输入/Users/username/Library/Application Support/iPhone Simulator/  前往。username这里写你的用户名。这样就能在Mac上找到模拟器上应用程序的沙盒目录了。每一个应用程序都有自己独立的三个目录：Document, Library, tmp，这三个目录之间不能相互访问。
Documents：苹果建议将程序中建立的或在程序中浏览到的文件数据保存在该目录下，iTunes备份和恢复的时候会包括此目录

* Library：存储程序的默认设置或其它状态信息；
* Library/Caches：存放缓存文件，iTunes不会备份此目录，此目录下文件不会在应用退出删除；
* tmp：提供一个即时创建临时文件的地方。

iTunes在与iPhone同步时，备份所有的Documents和Library文件。
iPhone在重启时，会丢弃所有的tmp文件。

