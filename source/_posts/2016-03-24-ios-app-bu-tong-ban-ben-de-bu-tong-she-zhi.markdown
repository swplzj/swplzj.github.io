---
layout: post
title: "iOS App 使用 Icon 区分不同渠道版本"
date: 2016-03-24 18:30:56 +0800
comments: true
categories: 
---

Hey, 今天有空就针对之前测试人员提出的一些建议进行了汇总，评估了哪些建议是有助于提高产品的体验的，所以就挑出其中一条进行了优化。原因：我们的应用有 OTA 内部测试版和 AppStore 正式发布版，但是是同一套图标和同一个名字，所以，只要测过我们应用的人，装过两个不同环境的应用之后，就对我们的 App 傻傻分不清楚了，对测试人员造成一定的困惑，所以，也就有了这篇文章，和这边文章背后的折腾......
<!--more-->
### 啰嗦一下 Bundle Identifier

大家都知道 iOS 是根据 `Bundle Identifier`来区别 App 的，所以如果要在手机上同时安装同一款 App 看似不行的，但是你可以通过设置不同的 `Bundle Identifier` 来区分不同版本的，其实可以是同一个二进制文件。在安装一个程序时，系统是根据 `Bundle Identifier` 来判断是全新安装还是升级。

### 折腾方法
通过折腾，大概找到了两种方法来解决这个问题，原理还是不一样的，对象也是不一样的。

#### 一、通过添加不同的 `Build Configuration` 来实现

我们的目的是在测试机上可以同时安装不同版本且是同一个应用，所以现在要做的就是正式版使用一个 `Bundle Identifier`，OTA 版本/ Debug 版本使用另一个 `Bundle Identifier`，假设 `AppStore` 版的 `Bundle Identifier` 是 "com.mycompany.myapp"，OTA版的是 "com.mycompany.myapp-beta"。同时为了直观的区分两个App，一般也会使用两套图标, 假设 `AppStore` 版的图标名称为Icon.png, Icon@2x.png, OTA版是Icon-beta.png, Icon-beta@2x.png. 那如果做到自动化的配置呢？答案在Build设置(Build Setting)里。

##### 1. 添加 Build 配置

默认Xcode会提供2个Build配置(Build Configuration)：Debug和Release，我们再加一个AppStore,三种不同的配置的作用也是不一样的：

 * Debug： 用来直接连机调试
 * Release：用于发布OTA的测试版
 * AppStore：用户提交到AppStore

{% img center /2016_04_11_add_configuration_rename.png / %}

##### 2. 在添加 Build Setting 里添加自定义设置

我们添加两个自定义的设置，一个命名为 `APP_ICON_NAME`, 另一个命名为`BUNDLE_IDENTIFIER_SUFFIX`，如下图这样设置：

{% img center /2016_04_11_add_user_defined.png / %}

`APP_ICON_NAME`用来定义图标的名称，`BUNDLE_IDENTIFIER_SUFFIX`用来定义 `Bundle Identifier`来区分不同的应用。

##### 3. 在Info.plist添加自定义的参数

在Info.plist中修改 `Bundle Identifier` 和 `Icon` 图标名称，把 `Bundle identifier` 值设置为 `${BUNDLE_IDENTIFIER_SUFFIX}`。添加 `Icon file`将图标值设置为 `${APP_ICON_NAME}@2x.png `。

{% img center /2016_04_11_add_plist_defined.png / %}

##### 4. 选择不同的配置进行打包发布

{% img center /2016_04_11_select_different_config.png / %}
现在我们就可以在Schema里选择使用不同的Build配置，比如选择Debug和Release配置来得到的安装包就可以用来上传到OTA进行测试；选择AppStore的自定义配置来打包，就可以用来提交到AppStore进行发布了。整个过程是自动化的，包括BundleId和图标文件的名称，当然，你也可以添加其他自定义的参数，比如 App 显示的名称之类的，添加与此类似。

#### 二、通过添加不同的 `Target` 编译方案来实现

##### 0. 先简单介绍一下 Xcode 中 target 的概念

苹果在文档中写道：

>Targets that define the products to build. A target organizes the files and instructions needed to build a product into a sequence of build actions that can be taken.”

在 Xcode 的一个项目中，可以允许建立多个编译的 target，每个 target 代表着
最终编译出来的一个 App 文件，在每个 target 中，可以添加不同的编译源文件和资源文件。最终，通过我们在不同 target 之间，修改其 Copy Bundle Resources 和 Compile Sources 配置，使课程之间的差异性得到实现。

##### 1. 创建新的 target

在Project Navigator面板中找到项目设置。在Targets区域，右键单击现有target，并选择Duplicate复制现有的target。

{% img center /2016_04_11_add_target.png /%}

##### 2. 根据 target 设置不同的 Icon

选择Assets.xcassets，并添加一个新的图标。右击icon> App Icons & Launch Images > New iOS App Icon。将新图标重命名为“AppIcon-Debug”，然后添加自己的图片。

{% img center /2016_04_11_add_icon.png  / %}

接着选择不同的 App Icon，用来区分不同版本。

{% img center /2016_04_11_select_different_target.png  / %}

##### 3. 选择不同的 Scheme 进行编译打包

现在我们就可以选择不同的 Target 来进行打包了，得到的 App 也是不一样的。

{% img center /2016_04_11_select_target_build.png  / %}

### 总结

以上两种方法的使用场景还是有所不同，在这就不在赘述。但不管使用上面哪种方法，应用的生产和开发版本将具有相同的基础代码，但可以有不同的图标，bundle ID，并指向不同的数据库。发布和提交过程非常简单。最重要的是，测试人员可以在同一设备上，安装应用程序的两个版本，所以他们完全清楚正在测试的是哪个版本。这也是此次折腾的目的所在啊。

Have fun！

参考文章：

[How to Use Xcode Targets to Manage Development and Production Builds](http://www.appcoda.com/using-xcode-targets/)