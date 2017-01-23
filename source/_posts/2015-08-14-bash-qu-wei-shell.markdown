---
layout: post
title: "Bash - 趣味Shell"
date: 2015-08-14 11:27:23 +0800
comments: true
categories: 
---

今天抽闲玩了一下`Shell`，发现几个比较有意思的，记录一下。

* 终端彩虹字：`lolcat`
* 图形化文字：`figlet`
* 幸运人生格言：`fortune`
* `ASCII`牛说：`Cowsay`
* 黑客帝国中瀑布流文字：`cmatrix`
* 写在最后

很有趣的 `Shell`， 值得一试；

<!--more-->

### lolcat


 **安装**

由于 `Lolcat` 是一个 `ruby gem` 程序，所以在你的系统中必须安装有最新版本的 `ruby`。安装`ruby`过程不再详述；

使用下面命令进行安装`lolcat`	
	
	gem install lolcat

{%img center /2015_08_14_lolcat.png %}	

**使用**

`lolcat`作用是在终端中为文本添加彩虹般的色彩。安装完成之后就可以使用了。

首先看一下`lolcat`的帮助文档


	lolcat --help
	
{% img center /2015_08_14_lolcat_help.png %}

接着查看日历

	cal | lolcat

{% img center /2015_08_14_lolcat_cal.png %}

**高级技巧**

我们还可以给文本赋予彩虹颜色的动画:

	echo Hello World | lolcat -a -d 500

{% img center /2015_08_14_lolcat_echo.gif %}

**别名技巧:`alias`**

我们发现，假如一个经常使用的打印命令，比如`ls`，想打印彩虹色彩，我们每次都需要在敲完`ls`之后再加上`lolcat`，也就是`ls | lolcat`，这样很不方便，这时我们就可以想到别名，`alias`。我们可以设定一些别名：
	
	alias ls="ls | lolcat"

但是，有一个问题来了，只要把终端这个标签关闭掉或者重启终端，我们设置的别名就失效了，也就是说别名只针对当前标签页有效。所以，接下来我们要设置`alias`永久有效，我们输入命令`cd ~`到用户主目录，找到`.bash_profile`文件，如果没有，我们创建一个
	
	touch .bash_profile
然后在`.bash_profile`中加入我们的别名

	alias ls="ls | lolcat"	
保存，关闭，重启`Terminal`，看一下效果：

{% img center /2015_08_14_lolcat_ls.png %}

**BTW**

假如你的终端没有设置配色方案，可以选择自带的主题：`Silver Aerogel`，设置方法:找到`.bash_profile`文件，添加如下代码：

```
# for color
export CLICOLOR=1
# \h:\W \u\$
export PS1='\[\033[01;33m\]\u@\h\[\033[01;31m\] \W\$\[\033[00m\] '
```
保存，重启终端，就可以看到漂亮的配色方案了。		


### figlet

**安装**

我们使用 `Homebrew`来安装此插件，

	brew install figlet

{% img center /2015_08_14_figlet_install.png %}

**使用**

* figlet

```
lizhenjie@localhost ~$ figlet Leo Lee
 _                 _              
| |    ___  ___   | |    ___  ___ 
| |   / _ \/ _ \  | |   / _ \/ _ \
| |__|  __/ (_) | | |__|  __/  __/
|_____\___|\___/  |_____\___|\___|
```

* figlist：列出所有字体名称


```
lizhenjie@localhost ~$ figlist
Default font: standard
Font directory: /usr/local/Cellar/figlet/2.2.5/share/figlet/fonts
Figlet fonts in this directory:
3-d
3x5
5lineoblique
acrobatic
alligator
alligator2
alphabet
...
```
* showfigfonts：展示每个字体效果！

```
lizhenjie@localhost ~$ showfigfonts
3-d :
  ****             **
 */// *           /**
/    /*           /**
   ***  *****  ******
  /// */////  **///**
 *   /*      /**  /**
/ ****       //******
 ////         ////// 


3x5 :
            
###     ### 
  # # # #   
 ##  #  ### 
  # # #   # 
###     ### 
...
```
只能列两种字体了，太多了。

当然，可以结合上面的 `lolcat`使用更炫哦。

{% img center /2015_08_14_figlet_lolcat.png %}

**Dashboard Figlet**

安装过程中，意外发现`Figlet`也有`Dashboard`中的插件，可以到[苹果官网下载](http://www.apple.com/downloads/dashboard/email_messaging/figletwidget.html)，使用起来也是非常方便的。

{% img center /2015_08_14_lolcat_dashboard.png %}

### fortune

**安装**

	brew install fortune

**用法**

```
lizhenjie@localhost ~$ fortune
"To take a significant step forward, you must make a series of finite 
improvements."
-- Donald J. Atwood, General Motors
```

结合`lolcat`使用：

```
lizhenjie@localhost ~$ fortune | lolcat
```

{% img center /2015_08_14_fortune_lolcat.png %}


### Cowsay

**安装**

	brew install cowsay
	
**用法**	

* `cowsay`可以输出一个在终端用ASCII码组成的小牛，这个小牛会说出你想要它说的话。

```
lizhenjie@localhost ~$ cowsay God help them who help themselves!
 ____________________________________ 
< God help them who help themselves! >
 ------------------------------------ 
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
lizhenjie@localhost ~$ 

```

* 罗列出所有可以画出的动物形状

```
lizhenjie@localhost ~$ cowsay -l
Cow files in /usr/local/Cellar/cowsay/3.03/share/cows:
beavis.zen bong bud-frogs bunny cheese cower daemon default dragon
dragon-and-cow elephant elephant-in-snake eyes flaming-sheep ghostbusters
head-in hellokitty kiss kitty koala kosh luke-koala meow milk moofasa moose
mutilated ren satanic sheep skeleton small sodomized stegosaurus stimpy
supermilker surgery telebears three-eyes turkey turtle tux udder vader
vader-koala www
```

* 使用其他动物形状

```
lizhenjie@localhost ~$ cowsay -f dragon LEO
 _____ 
< LEO >
 ----- 
      \                    / \  //\
       \    |\___/|      /   \//  \\
            /0  0  \__  /    //  | \ \    
           /     /  \/_/    //   |  \  \  
           @_^_@'/   \/_   //    |   \   \ 
           //_^_/     \/_ //     |    \    \
        ( //) |        \///      |     \     \
      ( / /) _|_ /   )  //       |      \     _\
    ( // /) '/,_ _ _/  ( ; -.    |    _ _\.-~        .-~~~^-.
  (( / / )) ,-{        _      `-.|.-~-.           .~         `.
 (( // / ))  '/\      /                 ~-. _ .-~      .-~^-.  \
 (( /// ))      `.   {            }                   /      \  \
  (( / ))     .----~-.\        \-'                 .~         \  `. \^-.
             ///.----..>        \             _ -~             `.  ^-`  ^-_
               ///-._ _ _ _ _ _ _}^ - - - - ~                     ~-- ,.-~
                                                                  /.-~

```

* 结合`fortune`使用

```
lizhenjie@localhost ~$ fortune | cowsay
 _________________________________________ 
/ We are all worms. But I do believe I am \
| a glowworm.                             |
|                                         |
\ -- Winston Churchill                    /
 ----------------------------------------- 
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```

* 结合`lolcat`使用：

{% img center /2015_08_14_cowsay_lolcat.png %}

* 结合`lolcat + fortune + cowsay`

{% img center /2015_08_14_cowsay_lolcat_forturn.png %}

是不是很神奇！！！

### cmatrix


**安装**

黑客帝国‘Hacker’风格的桌面的生动画面

	brew install cmatrix

**用法**	
	
	cmatrix	

{% img center /2015_08_14_cmatrix.png %}

其他用法可以使用 `man`查看。


### 写在最后

上面的命令可能在实际中对自己没有多大用途，但是，在配置的过程中，学到了不少东西，还增加了对 `Shell`的了解和兴趣，我们对自己的定位不能只局限与写代码，而是让自己多去了解自己不了解的，时常去走出自己的舒适区，找一些有趣有挑战性的东西去做，总之，保证自己一直在进步，加油！