---
layout: post
title: "NSTimer的用法-下载进度显示"
date: 2014-06-10 22:51:16 +0800
comments: true
categories: 
---
NSTimer的作用就是以一定的时间间隔向目标对象发送特殊的消息。最常见的是用来控制下载进度条的显示。

<!--more-->

#### 创建一个 Timer

```
+ scheduledTimerWithTimeInterval: invocation: repeats:
+ (NSTimer *)scheduledTimerWithTimeInterval:(NSTimeInterval)ti   invocation:(NSInvocation *)invocation   repeats:(BOOL)yesOrNo;
+ scheduledTimerWithTimeInterval: target: selector: userInfo: repeats:
+ (NSTimer *)scheduledTimerWithTimeInterval:(NSTimeInterval)ti   target:(id)aTarget   selector:(SEL)aSelector   userInfo:(id)userInfo   repeats:(BOOL)yesOrNo;
```

#### 启动 Timer

	– fire
	
#### 停止 Timer

```
– invalidate
Timer设置
– isValid
– fireDate
– setFireDate:
– timeInterval
– userInfo
```

#### 有码有真相：

```
//  
//  AppDelegate.m  
//  TimerDemo  
//  
//  Created by 李振杰 on 13-12-18.  
//  Copyright (c) 2013年 swplzj. All rights reserved.  
//  
  
#import "AppDelegate.h"  
  
//最大下载时间  
#define DOWNLOAD_TIMEOUT    60.0  
  
static int sec = 0;  
  
@interface AppDelegate ()  
{  
    NSTimer *_timer;  
}  
  
  
@end  
  
@implementation AppDelegate  
  
- (void)dealloc  
{  
    [_window release];  
    [super dealloc];  
}  
  
- (void)initView  
{  
    //创建一个进度条：用于显示下载的进度  
    UIProgressView *progress = [[UIProgressView alloc] initWithFrame:CGRectMake(20, 60, 220, 30)];  
    [progress setBackgroundColor:[UIColor brownColor]];  
    [progress setProgressViewStyle:UIProgressViewStyleBar];  
    [progress setProgress:0];  
    [progress setTag:0x123];  
    [self.window addSubview:progress];  
}  
  
//更新进度条  
- (void)updateView  
{  
    sec++;  
    UIProgressView *pro = (UIProgressView *)[self.window viewWithTag:0x123];  
    if (pro) {  
        [pro setProgress:sec / DOWNLOAD_TIMEOUT animated:YES];  
    }  
    if (sec > DOWNLOAD_TIMEOUT) {  
        //这是唯一的方法：从NSRunLoop中移除创建的定时器  
        [_timer invalidate];  
        _timer = nil;  
    }  
}  
  
  
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions  
{  
    self.window = [[[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]] autorelease];  
    // Override point for customization after application launch.  
  
    [self initView];  
    //在当前的runloop中添加一个定时器，每0.5s调用updateView函数一次  
    _timer = [NSTimer scheduledTimerWithTimeInterval:0.5 target:self selector:@selector(updateView) userInfo:nil repeats:YES];  
      
    self.window.backgroundColor = [UIColor whiteColor];  
    [self.window makeKeyAndVisible];  
    return YES;  
}  
  
@end  
```

[下载例子链接](http://download.csdn.net/detail/swplzj/6735607)