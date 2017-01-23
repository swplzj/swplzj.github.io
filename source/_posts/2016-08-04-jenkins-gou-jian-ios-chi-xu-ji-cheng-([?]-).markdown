---
layout: post
title: "Jenkins 构建 iOS 持续集成"
date: 2016-08-04 14:35:31 +0800
comments: true
categories: 
---

{% img center /2016_08_04_jenkins_topic.png %}
做重复乏味的工作就是在浪费生命和犯罪，尤其是在软件工程中更为明显，生命应该浪费在美好的事物上的原则，就开始了接下来的折腾。项目的持续集成前期看似是浪费时间，但随着项目数量增多，渠道的增加，测试人员、产品经理、项目负责人、大老板等都向你伸手要测试包，也就经常会有：Leo，把最新测试包上传到 Confluence 上了吗？Leo，给我发个最新安装包。来，Leo，这是我们的销售，先给他们安装一下咱们的应用，体验一下。。。。。。我是一个重度 GTD 使用者，决不允许别人这么无休止的打扰我的工作。。。。。。噢，好像跑偏了，简单如题。

<!--more-->

### 摘要：

<a href="#01">1. 什么是持续集成</a></br>
<a href="#02">2. 为什么要做持续集成</a></br>
<a href="#03">3. Jenkins 安装及配置</a></br>
<a href="#04">4. 问题总结</a></br>

<a id="01"></a>
## 什么是持续集成

* 持续集成, 简称CI（continuous integration）。
* CI作为敏捷开发重要的一步，其目的在于让产品快速迭代的同时，尽可能保持高质量。
* CI一种可以增加项目可见性，降低项目失败风险的开发实践。其每一次代码更新，都要通过自动化测试来检测代码和功能的正确性，只有通过自动测试的代码才能进行后续的交付和部署。
* CI 是团队成员间（产研测）更好地协调工作，更好的适应敏捷迭代开发，自动完成减少人工干预，保证每个时间点上团队成员提交的代码都能成功集成的，可以很好的用于对Android/iOS项目的打包。

<a id="02"></a>
## 为什么要做持续集成

从我理解的角度和我遇到的问题来看，有三点促使我做 iOS 的持续化集成：

1. 避免工作时间碎片化。

	学过面向对象的同学都知道，想要创建某个类都是首先申请内存环境进行init初始化，其实，我们的工作也是这样的，当你深入做某项工作的时候，其他人来打断你的思考，这就会破坏那种精深学习研究的氛围，你的大脑就会切换到另外一件事情上来，这种频繁切换造成了时间碎片化，造成了很多时间到浪费。
	
2. 不做重复乏味的体力活。

	我们的应用分为 In－House 版本和 AppStore 版本，同时维护两个项目，然后在上线前期测试环节会有比较频繁的编译、打包、分发、安装的体力活，手动打包是一件很不美好的事情，所要是机器为我们来做这些无聊的事情。我们值得研究更好的技术。
	
3. 避免手动打包造成的不必要的错误。

	同时维护多个项目，一个项目中又有多个 Schemes，多个 Targets，电脑中安装了很多证书和描述文件，在每次打包的时候就会面对选择证书，选择描述文件的问题，稍不留神选错证书或选错 Scheme 或选错 Target，轻则编译失败浪费点时间，增加点烦躁，更严重的是造成线上事故，丝毫没有夸张，线上无小事。所以，使用持续集成，可以精确的对项目进行配置，避免人工手动操作的失误。



<a id="03"></a>
## Jenkins 安装及配置
搭建构建平台之前，先要对整个流程有个清楚的理解，这里我画了一个简单的流程图方便我们理解其中要做的关键的事情。


{% img center /2016_08_04_jenkins_workflow_chart.png Jenkins集成流程图 %}

由于 Jenkins 经过多年的发展，其活跃的社区和丰富的插件让其成为了业界最受欢迎的CI服务器。通过使用Xcode插件，可以非常方便在Jenkins中运行iOS项目的构建脚本，所以我们选择 Jenkins 作为 CI 工具。

### 一、Jenkins的安装与启动

#### 安装
1.使用命令行安装

		$ brew install jenkins

如果没有安装java环境的话会出现以下错误，需要先安装jenkins需要安装java环境， [JDK下载地址](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
	
	jenkins: Java 1.7+ is required to install this formula. You can install with Homebrew Cask: brew install Caskroom/cask/java You can download from: http://www.oracle.com/technetwork/java/javase/downloads/index.html Error: An unsatisfied requirement failed this build.
	
2.直接从 Jenkins	的[官网](https://jenkins-ci.org)下载最新安装包。

#### 启动

安装完成后，启动jenkins，命令：

	$ jenkins

在浏览器中输入以下地址访问：

	http://localhost:8080/

### 二、插件的安装
我们需要安装相关的插件才能顺利对项目进行编译，需要安装以下插件：

- 安装GitLab插件

	因为我们用的是GitLab来管理源代码，Jenkins本身并没有自带GitLab插件，所以我们需要依次选择 系统管理->管理插件，在“可选插件”中选中“GitLab Plugin”和“Gitlab Hook Plugin”这两项，然后安装。

- 安装Xcode插件
	
	同安装GitLab插件的步骤一样，我们依次选择系统管理->管理插件，在“可选插件”中选中“Xcode integration”安装。

- 安装签名证书管理插件
	
	iOS打包内测版时，需要发布证书及相关签名文件，因此这两个插件对于管理iOS证书非常方便。还是在系统管理->管理插件，在“可选插件”中选中“Credentials Plugin”和“Keychains and Provisioning Profiles Management”安装。

- 安装FTP插件
	
	在系统管理->管理插件，在“可选插件”中选中“Publish over FTP”安装。

- 安装脚本插件
	
	这个插件的功能主要是用于在build后执行相关脚本。在系统管理->管理插件，在“可选插件”中选中“Post-Build Script Plug-in”安装。


以上插件安装完成后，重启 Jenkins!

### 三、Jenkins 全局配置

- 源码管理：

	我们代码管理使用的是GitLab，先需要配置SSH，我们可以在Jenkins的证书管理中添加SSH。在Jenkins管理页面，选择“Credentials”，然后选择“Global credentials (unrestricted)”，点击“Add Credentials”，如下图所示，我们填写自己的SSH信息，然后点击“Save”，这样就把SSH添加到Jenkins的全局域中去了。

{% img center /2016_08_05_ssh_public_key.png SSH Private Key Setup %}

- 上传配置编译打包证书及描述文件

	iOS 项目编译打包需要证书和签名文件，这里我们又需要用到Jenkins的插件，在系统管理页面，选择“Keychains and Provisioning Profiles Management”。

	{% img center /2016_08_05_profiles_management.png Keychains and Provisioning Profiles Management %}

	进入插件设置里面

	{% img center /2016_08_05_keychain_sign_cer_upload.png 上传登录钥匙串及签名证书文件 %}

	{% img center /2016_08_05_profile_upload.png 上传描述文件 %}

	点击保存就可以了。

- 自动化构建

	接下来就是重头戏了，包括Xcode编译、打包、上传测试分发平台、发送通知邮件等。

	 - 创建项目
	 
		 在Jenkins中，所有的任务都是以“item”为单位的。接下来我们就新建一个iOS的项目来开始自动化构建。点击“新建”，输入item的名称，选择“构建一个自由风格的软件项目”，然后点击“OK”。
	 
	 {% img center /2016_08_05_create_item.png 创建项目 %}
	 
	{% img center /2016_08_05_general.png 编辑项目 %}

	 - 源码管理
	 
		在源码管理中，选择Git，按下图进行配置：

		{% img center /2016_08_05_source_code_management.png 源码管理配置 %}

	- 构建触发器
			
		{% img center /2016_08_05_build_trigger.png 构建触发器 %}
		
	- 编译环境
	
		在编译环境中，勾选“Keychains and Code Signing Identities”和“Mobile Provisioning Profiles”。按照下图进行配置：
		
		{% img center /2016_08_05_build_environment_setup.png 编译环境配置 %}
	
	- 编译

		{% img center /2016_08_05_build_general.png 编译设置 %}
	
		{% img center /2016_08_05_build_keychain.png 编辑设置 %}
	
		{% img center /2016_08_05_build_cocoapods.png CocoaPods 项目设置 %}
				
		{% img center /2016_08_05_post_build_action.png 设置执行脚本 %}
		
		
		到目前为止所有的构建设置完了，保存，可以构建项目了。


<a id="04"></a>
## 问题总结

1. Jenkins 编译 cocoapods 项目遇到的问题: "Jenkins xcodebuild There are no schemes in workspace "
	
	出现原因：
	
	使用cocoapods管理项目时，会出现，多个 Schemes，然而这些 Schemes 都是私有的而不是共享的。
	
	解决方法：

	a. 打开项目的 workspace 文件；
	b. 从项目菜单中选择 Scheme > Manage Schemes；
	c. 修改项目后边的"Shared"为勾选状态，“Container”设置为 `Workspace` 而不是 `project`；
	d. 这时你项目中的＊.xcworkspace/xcshareddata/xcschemes目录下就会出现.xcscheme文件；
	e. 打开项目下Git .gitignore文件，确保没有过滤相关文件；
	f. 提交项目修改到Git仓库，重新拉取代码就好了。
	
	</br>
	参考图如下：
	
	{% img center /2016_08_05_schemes_management.png Git ignore 文件设置 %}
	</br>	
	{% img center /2016_08_05_git_ignore.png Git ignore 文件设置 %}
	</br>
	
2. 构建触发器相关知识

	```
	This field follows the syntax of cron (with minor differences). Specifically, each line consists of 5 fields separated by TAB or whitespace: MINUTE HOUR DOM MONTH DOW MINUTE Minutes within the hour (0-59) HOUR The hour of the day (0-23) DOM The day of the month (1-31) MONTH The month (1-12) DOW The day of the week (0-7) where 0 and 7 are Sunday. To specify multiple values for one field, the following operators are available. In the order of precedence, * '*' can be used to specify all valid values. * 'M-N' can be used to specify a range, such as "1-5" * 'M-N/X' or '*/X' can be used to specify skips of X''s value through the range, such as "*/15" in the MINUTE field for "0,15,30,45" and "1-6/2" for "1,3,5" * 'A,B,...,Z' can be used to specify multiple values, such as "0,30" or "1,3,5" Empty lines and lines that start with '#' will be ignored as comments. In addition, @yearly, @annually, @monthly, @weekly, @daily, @midnight, @hourly are supported.
	```
	
	
3. 发送邮件失败

	`我使用的是163邮箱进行发件`
	
	编译打包完成后，执行 python 脚本时，报错：(550, 'User has no permission’)。

	错误可能原因：

	a. 滥发邮件被锁了；
	b. 邮箱刚注册；
	c. 发送的邮件里面有HTML文本；
	d. 邮箱申请时间不够长；

	</br>
	
	解决方法：
	
	a. 进入你的163邮箱；
	b. 设置里选择POP3/SMTP/IMAP；
	c. 开启POP3/SMTP/IMAP 这三个服务；
	
	</br>
	
	新问题（自己的疏忽）：

	开启之后构建又返回新的错误：163邮箱报错WARN: 535 Error: authentication failed.

	解决：
	
	开启服务之后，需要使用授权码代替邮箱的密码进行发邮件。	
	后来测试了几次，163邮箱又报错，索性直接注册了新浪邮箱，再没出现过问题。
	
- 安装包上传至测试分发平台及发送邮件 Python 脚本
	
	[下载脚本文件](http://7xkrxl.com1.z0.glb.clouddn.com/auto.python)

<a id="05"></a>


