---
layout: post
title: "iOS5以上：设置控件全局显示样式《APPEARANCE PROXY》"
date: 2014-04-14 23:34:41 +0800
comments: true
categories: 
---

Appearance是Apple在iOS5.0上加的一个协议，它让程序员可以很轻松地改变某控件的全局样式（背景）

     @selector(appearance)

支持UIAppearance协议的类可以访问appearance selector ,它为receiver返回appearance proxy，我们可以给proxy发一些消息，诸如setTintColor:等

但是它并不是支持所有的UI类。下面列出它支持的类

<!--more-->


   1.UIActivitiIndicatorView 
　　	 
　　2.UIBarButtonItem

　　3.UIBarItem

　　4.UINavgationBar

　　5.UIPopoverControll

　　6.UIProgressView

　　7.UISearchBar

　　8.UISegmentControll 

　　9.UISlider

　　10.UISwitch

　　11.UITabBar

　　12.UITabBarItem

　　13.UIToolBar

　　14.UIView

　　15.UIViewController

例如：

```
 [[UINavigationBar appearance] setTintColor：[UIColor blackColor]];

 [[UISearchBar appearance] setTintColor: [UIColor blackr]];
 ```
 
注意：

初学者肯定会任意调用方法，大部分方法时无效的，如果调用时会抛出unknown selector 异常

那么如何查看你调用的方法时有效的呢，我们可以在此类的头文件中查看包含“UI_APPEARANCE_SELECTOR”常量的方法。

例如UIToolBar

它支持下列方法

```
@property(nonatomic,retain) UIColor   *tintColor  UI_APPEARANCE_SELECTOR;

- (void)setBackgroundImage:(UIImage *)backgroundImage forToolbarPosition:(UIToolbarPosition)topOrBottom barMetrics:(UIBarMetrics)barMetrics NS_AVAILABLE_IOS(5_0) UI_APPEARANCE_SELECTOR;

- (UIImage *)backgroundImageForToolbarPosition:(UIToolbarPosition)topOrBottom barMetrics:(UIBarMetrics)barMetrics NS_AVAILABLE_IOS(5_0) UI_APPEARANCE_SELECTOR;

- (void)setShadowImage:(UIImage *)shadowImage forToolbarPosition:(UIToolbarPosition)topOrBottom NS_AVAILABLE_IOS(6_0) UI_APPEARANCE_SELECTOR;

- (UIImage *)shadowImageForToolbarPosition:(UIToolbarPosition)topOrBottom NS_AVAILABLE_IOS(6_0) UI_APPEARANCE_SELECTOR;
  
```


[了解更多请点击访问](http://developer.apple.com/library/ios/#documentation/uikit/reference/UIAppearance_Protocol/Reference/Reference.html)