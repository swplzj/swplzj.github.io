---
layout: post
title: "如何下载和安装CocoaPods"
date: 2015-09-04 14:16:46 +0800
comments: true
categories: 
---
### CocoaPods安装和使用教程
------

#### CocoaPods是什么？


CocoaPods是iOS第三方开源类库管理工具。

<!--more-->

#### 如何下载和安装CocoaPods？

	sudo gem install cocoapods

GFW阻挡了cocoapods.org,

```
$ gem sources --remove https://rubygems.org/
//等有反应之后再敲入以下命令
$ gem sources -a http://ruby.taobao.org/
```
验证Ruby镜像

	$ gem sources -l
	
运行后出现

```
*** CURRENT SOURCES ***

http://ruby.taobao.org/	
```

安装
	
	$ sudo gem install cocoapods

#### 如何使用CocoaPods？
两种使用场景来具体说明如何使用CocoaPods。
##### 利用CocoaPods，在项目中导入AFNetworking类库

	$ pod search AFNetworking

先利用Xcode创建一个名字CocoaPodsDemo的项目，
有个文件来控制CocoaPods该下载什么。这个文件就叫做“Podfile”（注意，一定得是这个文件名，而且没有后缀）。你创建一个Podfile文件，然后在里面添加你需要下载的类库，每个项目只需要一个Podfile文件。

在终端中进入（cd命令）你项目所在目录，然后在当前目录下，利用vim创建Podfile，运行：

	$ vim Podfile

然后在Podfile文件中输入以下文字：

```
platform :ios, '7.0'
pod "AFNetworking", "~> 2.0"
```

然后保存退出。vim环境下，保存退出命令是：

	:wq

你会发现你的项目目录中，出现一个名字为`Podfile`的文件，而且文件内容就是你刚刚输入的内容。注意，`Podfile`文件应该和你的工程文件`.xcodeproj`在同一个目录下。

	$ pod install 

注意：以后打开项目就用 `CocoaPodsDemo.xcworkspace` 打开，而不是之前的`.xcodeproj`文件。

##### 如何正确编译运行一个包含CocoPods类库的项目

打开终端，进入`UAAppReviewManagerExample`所在的目录，也就是和`Podfile`在同一目录下，和场景1一样，输入以下命令（由于已经有`Podfile`，所以不需要再创建`Podfile`）：

	$ pod update

这时候，再回到UAAppReviewManagerExample文件夹看一看，会看到多了一个文件UAAppReviewManagerExample.xcworkspace：

注意，这里有个小问题，如果刚刚你不是输入`$ pod update`，而是输入`$ pod install`，会发现类库导入不成功，并且终端出现下面提示：

`$ pod install`只会按照`Podfile`的要求来请求类库，如果类库版本号有变化，那么将获取失败。但是 `$ pod update`会更新所有的类库，获取最新版本的类库。而且你会发现，如果用了 `$ pod update`，再用 `$ pod install` 就成功了。