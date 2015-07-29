---
layout: post
title: "iOS NSInvocation的学习"
date: 2014-03-18 20:58:21 +0800
comments: true
categories: 
---
#### 用途：

NSInvocation的作用和performSelector:withObject:的作用是一样的：用于iOS编程中调用某个对象的消息。

performSelector:withObject:调用一些参数较少的消息是比较方便的，但是对于参数个数大于2的消息，使用NSInvocation还是比较方便的。

因为NSInvocation是静态的呈现Objective-C的消息，也就是说，它把一个行动变成了一个对象。NSInvocation对象用于对象之间和应用程序之间存储和转发消息，主要通过NSTimer对象和分布式对象系统来完成。

<!--more-->

```
//  
//  NSInvocation+Improved.h  
//  InvocationDemo  
//  
//  Created by 李振杰 on 13-12-11.  
//  Copyright (c) 2013年 swplzj. All rights reserved.  
//  
  
#import <Foundation/Foundation.h>  
  
@interface NSInvocation (Improved)  
  
+ (NSInvocation *)invocationWithTarget:(id)_target andSelector:(SEL)_selector;  
+ (NSInvocation *)invocationWithTarget:(id)_target andSelector:(SEL)_selector andArguments:(voidvoid *)_addressOfFirstArgument, ...;  
- (void)invokeOnMainThreadWaitUntilDone:(BOOL)wait;  
  
@end  
```

```
//  
//  NSInvocation+Improved.m  
//  InvocationDemo  
//  
//  Created by 李振杰 on 13-12-11.  
//  Copyright (c) 2013年 swplzj. All rights reserved.  
//  
  
#import "NSInvocation+Improved.h"  
  
@implementation NSInvocation (Improved)  
  
+ (NSInvocation *)invocationWithTarget:(id)_target andSelector:(SEL)_selector  
{  
    //方法签名类  
    //需要给定一个方法，用于必须创建一个NSInvocation对象的情况下，例如在消息的转发。  
    NSMethodSignature *methodSig = [_target methodSignatureForSelector:_selector];  
    //根据方法签名类来创建一个NSInvocation  
    /* 
     一个NSInvocation是静态的呈现Objective-C的消息，也就是说，它是一个行动变成了一个对象。 NSInvocation对象用于对象之间和在应用程序之间存储和转发消息，主要通过NSTimer对象和分布式对象系统来完成。 
    */  
    NSInvocation *invocation = [NSInvocation invocationWithMethodSignature:methodSig];  
    [invocation setTarget:_target];  
    [invocation setSelector:_selector];  
    return invocation;  
}  
  
+ (NSInvocation *)invocationWithTarget:(id)_target andSelector:(SEL)_selector andArguments:(voidvoid *)_addressOfFirstArgument, ...  
{  
    NSMethodSignature *methodSig = [_target methodSignatureForSelector:_selector];  
    NSInvocation *invocation = [NSInvocation invocationWithMethodSignature:methodSig];  
    [invocation setTarget:_target];  
    [invocation setSelector:_selector];  
    //获得签名类对象的参数个数  
    unsigned int numArgs = [methodSig numberOfArguments];  
    //PS:atIndex的下标必须从2开始。原因：0 1 两个参数已经被target 和selector占用  
    if (2 < numArgs) {  
        /* 
         VA_LIST 是在C语言中解决变参问题的一组宏，所在头文件：#include <stdarg.h> 
         VA_START宏，获取可变参数列表的第一个参数的地址（ap是类型为va_list的指针，v是可变参数最左边的参数） 
         VA_ARG宏，获取可变参数的当前参数，返回指定类型并将指针指向下一参数（t参数描述了当前参数的类型） 
         VA_END宏，清空va_list可变参数列表 
         */  
          
        /* 
         用法： 
         （1）首先在函数里定义一具VA_LIST型的变量，这个变量是指向参数的指针； 
         （2）然后用VA_START宏初始化刚定义的VA_LIST变量； 
         （3）然后用VA_ARG返回可变的参数，VA_ARG的第二个参数是你要返回的参数的类型（如果函数有多个可变参数的，依次调用VA_ARG获取各个参数）； 
         （4）最后用VA_END宏结束可变参数的获取。 
         */  
        va_list varargs;  
          
        va_start(varargs, _addressOfFirstArgument);  
        [invocation setArgument:_addressOfFirstArgument atIndex:2];  
          
        for (int argIndex = 3; argIndex < numArgs; argIndex++) {  
            voidvoid *argp = va_arg(varargs, voidvoid *);  
            [invocation setArgument:argp atIndex:argIndex];  
        }  
          
        va_end(varargs);  
    }  
    return invocation;  
}  
  
- (void)invokeOnMainThreadWaitUntilDone:(BOOL)wait  
{  
    [self performSelectorOnMainThread:@selector(invoke) withObject:nil waitUntilDone:wait];  
}  
  
@end  
```

#### 自定义类：

```
//  
//  SomeClass.h  
//  InvocationDemo  
//  
//  Created by 李振杰 on 13-12-11.  
//  Copyright (c) 2013年 swplzj. All rights reserved.  
//  
  
#import <Foundation/Foundation.h>  
  
@interface SomeClass : NSObject  
  
- (void)commonOperation;  
- (void)improvedOperation;  
- (void)fireTimer:(NSDictionary *)user andDate:(NSDate *)startTime;  
  
@end  
```

```
//  
//  SomeClass.m  
//  InvocationDemo  
//  
//  Created by 李振杰 on 13-12-11.  
//  Copyright (c) 2013年 swplzj. All rights reserved.  
//  
  
#import "SomeClass.h"  
#import "NSInvocation+Improved.h"  
  
@implementation SomeClass  
  
- (void)commonOperation  
{  
    NSDate *date = [NSDate date];  
    NSDictionary *user = [NSDictionary dictionaryWithObjectsAndKeys:@"value1", @"key1", nil nil];  
    SEL method = @selector(fireTimer:andDate:);  
    NSMethodSignature *signature = [[self class] instanceMethodSignatureForSelector:method];  
    NSInvocation *invocation = [NSInvocation invocationWithMethodSignature:signature];  
    [invocation setTarget:self];  
    [invocation setSelector:method];  
    [invocation setArgument:&user atIndex:2];  
    [invocation setArgument:&date atIndex:3];  
//    [NSTimer scheduledTimerWithTimeInterval:0.1 invocation:invocation repeats:YES];  
    [invocation invoke];  
}  
  
- (void)improvedOperation  
{  
    //1.创建一个没有参数的NSInvocation  
//    SEL selector = @selector(fireTimer:andDate:);  
//    NSInvocation *invocation = [NSInvocation invocationWithTarget:self andSelector:selector];  
      
    //2.创建带有两个参数的NSInvocation  
    NSDate *date = [NSDate date];  
    NSDictionary *user = [NSDictionary dictionaryWithObjectsAndKeys:@"value1", @"key1", nil nil];  
    NSInvocation *invocation = [NSInvocation invocationWithTarget:self andSelector:@selector(fireTimer:andDate:) andArguments:&user, &date];  
    [invocation invoke];  
}  
  
- (void)fireTimer:(NSDictionary *)user andDate:(NSDate *)startTime  
{  
    /* 
     sleep 与 sleepForTimeInterval的区别 
     sleep直接让线程停掉，sleepForTimeInterval是让runLoop停掉。比如说，你有2个APP，分别是A和B，A启动B，然后去取B的进程号，如果你用sleep等B启动再去取，你会发现取不到，因为你只是把代码加到runloop里面去，而runloop并没有执行到这句，sleep就直接让系统停在那里，所以取不到，而后者就没问题，因为它是让runloop执行到这句的时候停1s 
     */  
      
    [NSThread sleepForTimeInterval:2];  
    NSTimeInterval timeInterval = -11 * [startTime timeIntervalSinceNow];  
    NSString *timeStr = [NSString stringWithFormat:@"%.2f", timeInterval];  
    NSLog(@"fireTime: %@", timeStr);  
}  
  
@end  
```

#### 调用SomeClass：

```
//  
//  AppDelegate.m  
//  InvocationDemo  
//  
//  Created by 李振杰 on 13-12-11.  
//  Copyright (c) 2013年 swplzj. All rights reserved.  
//  
  
#import "AppDelegate.h"  
#import "SomeClass.h"  
  
@implementation AppDelegate  
  
- (void)dealloc  
{  
    [_window release];  
    [super dealloc];  
}  
  
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions  
{  
    self.window = [[[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]] autorelease];  
    // Override point for customization after application launch.  
      
    SomeClass *some = [[SomeClass alloc] init];  
    [some commonOperation];  
    [some improvedOperation];  
    [some release];  
      
    self.window.backgroundColor = [UIColor whiteColor];  
    [self.window makeKeyAndVisible];  
    return YES;  
}  
  
@end  
```