---
layout: post
title: "Mac使用技巧"
date: 2013-12-12 22:25:20 +0800
comments: true
categories: 
---

##### 1. [技巧]终端模式下用Finder打开当前目录 
方法如下 

open .

##### 2.强制退出应用

如果您无法从无响应的 app 进行切换，请按住 Command-Option-Shift-Esc 键三秒钟，以强制其退出。该组合键将命令 OS X 强制退出最前面的 app。

<!--more-->

##### 3.如何为 Mac OS X 启用 Root 账户?
 
尽管 Apple 不推荐开启 Mac OS X 的 Root 账户，但有时会遇到一些操作上的麻烦不得不开启 Root 账户来解决。那么怎么开启 Mac OS X 的 Root 账户呢？

###### 开启 Root 账户

打开“系统偏好设置”，进入“用户与群组”面板，记得把面板左下角的小锁打开，然后选择面板里的“登录选项”。在面板右边你会看到“网络账户服务器”，点击它旁边的“加入…”按钮，再点击弹出的窗口中“打开目录实用工具”。这时会弹出一个新窗口，把左下角的小锁打开，然后点击菜单栏上的“编辑” – “启用 Root 用户”，在弹出的窗口中设置 Root 的密码就可以了。

###### 如何为 OS X 启用 Root 账户?

上面的步骤有些啰嗦，你可以在 Finder 中按组合键“shift + command + G”直接前往文件夹 /System/Library/CoreServices，在里面找到“目录实用工具”来开启 Root 账户。

启用之后，你可以注销系统然后用刚刚开启的 Root 帐号登录 Mac OS X 了。

###### 关闭 Root 账户

Root 账户的权限系统中最高级别了，在我们日常生活中几乎用不到，为了避免发生各种安全问题，MacGG建议在用 Root 账户解决完问题后关闭 Root 账户。关闭的步骤跟上面差不多，只是在打开“目录实用工具”后，点击“编辑”菜单里的“停用 Root 用户”即可。
##### 4.同时按住 control + command + 电源键，这样也会强制关闭电脑，但好处是不会损坏系统文件。

##### 5.[Mac]如何让 OS X 定时截图？

	screencapture -t png -T 10 ~/Desktop/screenshot01.png

##### 6.[Mac]如何删除Mac系统里面的所有 DS_Store 文件呢？

DS_Store (英文全称 Desktop Services Store)是一种由苹果公司的Mac OS X操作系统所创造的隐藏文件，目的在于存贮文件夹的自定义属性，例如文件们的图标位置或者是背景色的选择等。

如何删除Mac系统里面的所有 DS_Store 文件呢？

启动“终端”，再/应用程序/工具文件夹中。
输入以下命令：

	sudo find / -name “.DS_Store” -depth -exec rm {} \;

接着输入你的管理员密码，这时你的 .DS_Store 全都没了。