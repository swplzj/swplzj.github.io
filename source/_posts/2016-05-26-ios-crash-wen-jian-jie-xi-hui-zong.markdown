---
layout: post
title: "iOS Crash 文件解析汇总"
date: 2016-05-26 17:03:52 +0800
comments: true
categories: 
---
{% img center /2016_05_25_workplace.jpg workplace %}


所有程序都是无法避免有 Bug 的，有了 Bug 要通过有效的手段技术跟踪、定位、解析、解决，普通的 Bug 看过报崩溃报告就可以很容易解决，当然也会遇到很棘手的 Bug，这是时候就要使尽浑身解数来对付了，下面总结一下自己解决难缠 Bug 的一些方法，希望能够帮助到你。:)
 
<!--more-->

## 方法汇总

1. 友盟分析工具解析崩溃报告；
2. 使用命令行对单个十六进制地址进行解析；
3. 使用 Xcode 对崩溃报告进行解析；

### 友盟分析工具解析崩溃报告
	
我们在项目中加入了友盟统计，此服务中提供的有 Crash 收集和分析工具，这样能够帮助我们监测 App 在设备上的运行状况，还可以及时发现问题并解决问题。通过友盟我们收集 Bug 并准确定位错误所在的类以及行数，具体步骤如下：

1. 登录友盟后台，查看并导出崩溃报告，文件格式是`.csv`；
2. [下载错误分析工具](http://dev.umeng.com/files/download/umcrashtool.zip)并解压 zip 得到 `umcrashtool`文件，可将 `umcrashtool` 与已下载的 xxx.csv 文件放入同一目录下；
3. 在 `Terminal` 中运行 `umcrashtool` 命令，参数为错误分析的.csv文件绝对路径，如下：
	
		$ ./umcrashtool [absolutely_path_of_csv_file] 

也可以按照先后顺序拖拽 `umcrashtool`、xxx.csv 文件到`Terminal`，简单方便；
4. 在 `Terminal` 中运行 `umcrashtool`，提示如下: Usage: umcrashtool [export-file-path]，定位后的代码及行数会写入错误分析-symbol.csv文件，与原文件在同一目录下。用工具打开新生成的xxx-symbol.csv文件，便可查看错误发生的源码文件及行数。
	
`注意`：如果错误分析没有成功，请先确保对应的 xxx.dSYM 文件在` ~/Library/Developer/Xcode/` 或该路径的子目录下。（对于每一个产品发布时archive操作会将dsym文件存放到~/Library/Developer/Xcode/Archives路径下，因此建议保留该路径下的文件，以便后续用工具分析错误。）
	
	
### 使用命令行对单个十六进制地址进行解析

使用命令行进入到包含xxx.app.dSYM的路径，并执行下面命令:

	dwarfdump --arch=arm64 --lookup 0x10051bc2c xxx.app.dSYM

其中，`arm64` 针对具体 `crash` 对应的机型来改变，`lookup` 后面的地址为具体的崩溃地址，xxx.app.dSYM为对应该崩溃信息的版本。

还有一种命令：

	atos -arch armv7 -o xxxApp 0x1153b9. 

也是可以分析出这处内存地址反编译回来的源码行。

但是，还是有分析不出来的 Bug

{% img center /2016-05-25-crash-report.png crash report %}

### 使用 Xcode 对崩溃报告进行解析



操作是打开 `Xcode > Window > Organizer`，上图：

{% img center /2016_05_25_xcode.png Xcode View Crash Report %}

可以选择相应的 Crash 来进行查看了。

'注意：'前提是在 `Xcode` 登录 Apple ID，才能看到线上 App 的崩溃报告。

## 总结

这就是几个具体追踪定位 Bug 的方法和技巧，当然，你也可以在 App 中自己加入抓去崩溃的功能，总之分析崩溃原因，越是把范围缩小，越容易发现问题，进而解决问题。












































































































































































































