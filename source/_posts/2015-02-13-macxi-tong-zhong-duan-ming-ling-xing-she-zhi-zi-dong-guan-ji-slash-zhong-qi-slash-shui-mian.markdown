---
layout: post
title: "Mac系统终端命令行设置自动关机/重启/睡眠"
date: 2013-10-11 20:03:03 +0800
comments: true
categories: 
---
### mac设定定时关机/重启/睡眠，可以用终端命令来解决
命令的主体主要是 Shutdown，h/r/s 分别代表关机/重启/睡眠，然后在后面加上执行时间(yymmddhhmm)即可。

<!--more-->

###### 设定2013年11月4日14:10分关机：
	
	sudo shutdown -h 1311041410

也可以 

	sudo shutdown -h 14:10
	
###### 设定2013年11月4日14:10分重启：
	
		sudo shutdown -r 1311041410

也可以 

	sudo reboot 
	
或者

	sudo shutdown -r now
	
###### 设定2013年11月4日14:10分睡眠：

	sudo shutdown -s 1311041410
	
###### 如果想要立即关机/重启/睡眠，可以执行命令：

	sudo halt
	
或者
	
	sudo shutdown -h now
	
now也可以适用于重启和睡眠。


 

### 临时取消定时关机/睡眠/重启的方法也很简单

我们在终端中执行了关机的命令，终端中会显示如下：

```
swplzj:~ issuser$ sudo shutdown -s 1311041410
Shutdown at Mon Nov  4 14:10:00 2013.
shutdown: [pid 6609]
```

注意上面的 pid 6609，这是指当前运行的这个 shutdown 命令的进程号，如果要取消关机，只需要停止这个进程的运行就可以了。

命令为：
	
	sudo kill 6609