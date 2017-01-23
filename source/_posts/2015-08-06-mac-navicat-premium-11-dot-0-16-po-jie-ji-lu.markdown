---
layout: post
title: "Mac - Navicat Premium 11.0.16 破解记录"
date: 2015-08-06 16:13:08 +0800
comments: true
categories: 
---

今天在测试聚信立相关接口时候，需要改变用户在数据库中聚信立认证状态，需要后台人员频繁帮忙修改改数据库中的值，有些麻烦。同事之前给推荐的数据库管理软件又过了试用期，只能重新安装，自己动手，丰衣足食。这是我安装 `Navicat Premium` 并破解的记录，供大家参考。

<!--more-->

1、安装 `Navicat Premium`

2、打开注册机(若注册机无法打开，右击-显示简介-选中以32位模式打开)

3、（默认选择）点击 `Generate` 获取激活码

4、点击 `Activata`，弹出选择程序的窗口，选择 `Navicat Premium`，操作系统支持10.10（本人系统是Yosimite 10.10.3）

{% img center  /2015_08_06_my_os_version.png %}

5、点击 `patched` 又弹出选择程序的窗口，选择 `Navicat Premium`

6、恭喜注册成功

{%img center /2015_08_06_crack_success.png %}

`注意`　　
　　

如果还不能激活请在终端运行以下命令（非常重要）：
　　
```

rm -Rf ~/Library/Application\ Support/PremiumSoft\ CyberTech/Navicat*

rm -Rf ~/Library/Caches/com.prect.NavicatPremium 

rm -Rf ~/Library/Preferences/com.prect.NavicatPremium.plist

sudo rm -Rf /Applications/Navicat\ Premium.app/

```

执行完成后重启（一定要重启）再安装 `Navicat Premium`，并再次执行破解过程！



附破解注册机及 `navicat Premimum11.0.16 ` [安装包下载地址](http://pan.baidu.com/s/1eQmwQky)

