---
layout: post
title: "面向 iOS 开发的优秀 XCode 插件"
date: 2015-02-13 21:05:50 +0800
comments: true
categories: 
---
一套理想的插件架构几乎能够决定整个集成开发环境的差异与特性。Sublime Text与TextMate就是很好的实例。但大家可能不了解的是，Xcode也支持插件。哈哈，如果各位真的前所未闻，我也不会感到惊讶，毕竟苹果并没有把太多精力用在Xcode的宣传之上。事实上，我们几乎找不到多少用于指导Xcode创建的说明文档。

幸运的是，这并没有妨碍开发人员在Xcode这片土地上耕耘并构建自己的插件果实。目前市面上存在大量插件，能够让我们的Xcode使用之旅更加平坦也更富乐趣，甚至足以弥合苹果在其中留下的所有沟壑。在今天的文章中，我将向大家展示自己在日常工作中频繁用到的几款优秀插件。

<!--more-->

##### Alcatraz

过去，安装Xcode插件往往令人抓狂不已，但Alcatraz的出现彻底改写了这一历史。今年早些时候，Alcatraz迎来了发展道路上的里程碑——1.0版本，现在如今已经可以与Xcode 5相兼容。如果大家还没有安装Alcatraz，我建议大家首先完成这项工作。

官方网站：[http://alcatraz.io/](http://alcatraz.io/)

##### XToDo

这款插件不仅能够着力强调TODO、FIXME、？？？以及！！！注释，同时可以按住 control + T 可以快速查看专用列表，并且可以自定义一些标签。

![id1](http://s9.51cto.com/wyfs02/M00/46/84/wKioL1Pyvc2iUbyMAABLbcwVzmM494.jpg)

官方网站：[https://github.com/trawor/XToDo](https://github.com/trawor/XToDo)

##### Dash for Xcode

Dash是一款用于浏览文档的优秀软件，我每天都会反复加以运用。更让人喜出望外的是，它现在已经与Xcode相集成，这要归功于其Xcode插件。大家可能知道，我们可以在Xcode编辑器中按住Option键同时点击以直接前往对应文档。不过我对于Xcode自带来的文档浏览器评价不高，在这种情况下Dash插件就成了最理想的解决方案。

![id2](http://s2.51cto.com/wyfs02/M00/46/83/wKiom1PyvYOBTuZFAACLZqMVukQ768.jpg)

该插件能够在我们按住Option并点击对应文档后利用Dash而非Xcode自带文档浏览器将其打开。Dash不仅运行速度极快，同时还能与CocoaDocs相集成。举例来说，如果大家用这种方式点击AFNetworking库中的一个方法，那么Dash将显示出该方法的说明文档。我超爱这项功能。

官方网站：[http://kapeli.com/dash](http://kapeli.com/dash)
