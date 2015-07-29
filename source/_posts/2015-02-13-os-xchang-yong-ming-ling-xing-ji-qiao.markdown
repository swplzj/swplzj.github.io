---
layout: post
title: "OS X常用命令行技巧"
date: 2014-05-11 23:08:13 +0800
comments: true
categories: 
---

#### 用“caffeinate”来停用屏幕保护和休眠

山狮（Mac OS X 10.8）引入的新功能caffeinate，它完全就是命令行版本的咖啡因（Caffeine），可以让你电脑保持精神。运行方法：

caffeinate -t 3600

其中， -t用来指定时间，单位是秒，所以上面的例子就是一个小时。

<!--more-->

#### 用pkgutil来释放PKG文件

如果你只是想从pkg文件里面抓取一些文件出来而不想安装整个软件？那pkgutil最适合你不过了：

pkgutil --expand sample.pkg ~/Desktop/

当然，你需要把sample.pkg的名字改成对应你要释出的pkg包的名字，上面的例子会把整个pkg包解压到桌面。

#### 用“purge”来清理内存

purge清理磁盘和内存的缓存，效果类似于重启电脑。虽然有人说这也就是个安慰剂，但是它的确会对那些不活动的进程的内存释放出来，当你机器的内存不够用的时候，它是可以起到加速作用的。

purge的用法非常简单：

purge

执行的过程中需要等一小会儿，之后各个进程的速度就会快起来啦！

（小编注：如果你机器内存很大，并且用不完，真的没有什么必要总执行这个命令，需要等那个时间不说，程序自己建立的一些缓存会因为这个命令重新构建，除非是你内存真的不够用了，否则得不偿失。）

#### 使用“open” 来启动同一个app的多个实例

你可能已经知道你可以通过命令行里的open命令来启动电脑上的程序，但是你知道吗，其实可以再加上-n的选项来创建多个实例。而且这非常简单，你所需要做的只是：

open -n /Applications/Safari.app/

上面的例子会创建另外的Safari ，你可以根据你的需要来启动其他的程序，别忘了.app的后缀

#### 不通过App Store更新系统

更新系统这样的事情，并不一定要麻烦App Store。你可以在命令行里直接搞定这个事情，而这也非常简单：

sudo softwareupdate -i -a

这个命令其实在Mac OS X系统中已经存在好多年了。所以各种版本都可以使用这种方法来更新系统。

列出来所有下载过的东西

我们都经历过这样的事情吧：之前下载了一个什么东西，但是忘记是从那里下载的了，或者记不清出它叫什么名字。不过还好，你很幸运，因为免疫服务（Quarantine Service）保存了一份你所有的下载内容的数据库，这样一来你就可以查询那个数据库来找到你想要的东西。可以通过sqlite3命令来实现：

sqlite3 ~/Library/Preferences/com.apple.LaunchServices.QuarantineEventsV* 'select LSQuarantineDataURLString from LSQuarantineEvent' |more

#### 用“chflags”来隐藏文件和文件夹

有什么想隐藏的文件或者文件见吗？用chflags命令就可以把任何文件或者文件夹从系统里面隐藏起来。对于文件和文件夹，使用方法都一样：

chflags hidden /path/to/file/or/folder/

幸好（或者但是）这样隐藏过的文件和文件夹依然可以通过ls看到，不过在Finder里面还是看不到的，除非你使用了nohidden选项之前，比如：

chflags nohidden /path/to/unhide/

这两种改变都是立即生效的。

#### 用拖拽的方式来自动写入文件

要是你们在命令行里面还苦逼兮兮的写很长的文件路径，就有点太对不住自己了。因为Mac OS X里面可以通过拖拽的方式输入路径：

sudo vi (drag file here to print the full path)

无论在命令行上还是终端程序里面，都生效。

#### 创建加密的压缩包

如果你想加密一个压缩包，其实不需要什么其他的软件，系统自己就可以完成：

zip -e protected.zip /file/to/protect/

如果没有-e就会创建一个普通的，没有密码的压缩包。