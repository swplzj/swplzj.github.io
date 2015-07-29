---
layout: post
title: "iOS 数据持久化（三）"
date: 2014-08-17 22:03:35 +0800
comments: true
categories: 
---
iOS中有五种持久化数据的方式：属性列表、对象归档、NSUserDefaults、SQLite3和Core Data。

#### NSUserDefaults官方文档介绍：

NSUserDefaults类为和默认系统类交互提供了一种编程接口。默认系统允许应用程序去定制其行为，为了符合用户的喜好。举例来说，你可以允许用户去决定在应用程序中显示的计量单位或者文档多久会自动保存。应用程序在用户默认数据库中，通过给一系列参数赋值来记录类似的喜好。这些参数被称为默认值，因为它们通常被用于一个程序在启动时或默认状态下的默认工作状态。

<!--more-->

在运行时，你可以使用NSUserDefaults对象读取你的应用程序的从一个用户默认数据库使用的默认数据。NSUerDefaults缓存信息，以避免每次获取一个默认值都需要打开用户默认的数据库。同步方法，他会每隔一段时间自动调用，使内存中缓存的信息与用户默认数据库中的信息同步。

#### NSUserDefaults

NSUserDefaults适合存储轻量级的本地数据，比如要保存一个登录界面用户名、密码之类的，NSUserDefaults是比较合适。下次再登录的时候就可以直接从NSUserDefaults里面读取上次登录的信息，

```
//  
//  AppDelegate.m  
//  UserDefaultsDemo  
//  
//  Created by 李振杰 on 13-11-20.  
//  Copyright (c) 2013年 swplzj. All rights reserved.  
//  
  
#import "AppDelegate.h"  
#import "WelcomeViewController.h"  
#import "RootViewController.h"  
  
@implementation AppDelegate  
  
- (void)dealloc  
{  
    [_window release];  
    [super dealloc];  
}  
  
- (void)isFirstLaunch  
{  
    //还未登录过  
    if(![[NSUserDefaults standardUserDefaults] boolForKey:@"everLaunched"]){  
        //设置第二次使用的value值为yes  
        [[NSUserDefaults standardUserDefaults] setBool:YES forKey:@"everLaunched"];  
        //设置第一次使用的value值为yes  
        [[NSUserDefaults standardUserDefaults] setBool:YES forKey:@"firstLaunch"];  
    }else{  
        [[NSUserDefaults standardUserDefaults] setBool:NO forKey:@"firstLaunch"];  
    }  
      
    if ([[NSUserDefaults standardUserDefaults] boolForKey:@"firstLaunch"]) {  
        WelcomeViewController *welcomeVC = [[WelcomeViewController alloc] init];  
        [self.window setRootViewController:welcomeVC];  
        [welcomeVC release];  
    }else{  
        RootViewController *rootVC = [[RootViewController alloc] init];  
        [self.window setRootViewController:rootVC];  
        [rootVC release];  
    }  
}  
  
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions  
{  
    self.window = [[[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]] autorelease];  
    // Override point for customization after application launch.  
      
    [self isFirstLaunch];  
    self.window.backgroundColor = [UIColor whiteColor];  
    [self.window makeKeyAndVisible];  
    return YES;  
}  
  
@end  
```

NSUserDefaults类为访问普通类型的变量提供便利的方法，这些类型有floats,doubles,integers,booleans和URLs。一个默认的对象必须是一个属性列表，是NSData,NSString,NSNumber,NSDate,NSArray或者NSDictionary的实例。如果你想存储其它类型的对象，你应当将其转换成一个NSData的实例。

下面举例说明保存自定义对象Student到NSUserDefaults中，然后从NSUserDefaults中再读取出数据。

这里面包含了存取一个自定义对象和存取多个自定义对象这两种情况。

```
//  
//  AppDelegate.m  
//  UserDefaultsDemo  
//  
//  Created by 李振杰 on 13-11-20.  
//  Copyright (c) 2013年 swplzj. All rights reserved.  
//  
  
#import "AppDelegate.h"  
#import "WelcomeViewController.h"  
#import "RootViewController.h"  
#import "Student.h"  
  
@implementation AppDelegate  
  
- (void)dealloc  
{  
    [_window release];  
    [super dealloc];  
}  
  
- (void)isFirstLaunch  
{  
    //还未登录过  
    if(![[NSUserDefaults standardUserDefaults] boolForKey:@"everLaunched"]){  
        //设置第二次使用的value值为yes  
        [[NSUserDefaults standardUserDefaults] setBool:YES forKey:@"everLaunched"];  
        //设置第一次使用的value值为yes  
        [[NSUserDefaults standardUserDefaults] setBool:YES forKey:@"firstLaunch"];  
        Student *stu = [[Student alloc] init];  
        NSLog(@"stu.name = %@\nstu.age = %d\nstu.height = %f\n", stu.name, [stu.age integerValue], [stu.height floatValue]);  
  
        [self saveCustomObjectToUerDefaults:stu];  
        [stu release];  
    }else{  
        [[NSUserDefaults standardUserDefaults] setBool:NO forKey:@"firstLaunch"];  
        [self saveManyStudentInfo];  
    }  
      
    if ([[NSUserDefaults standardUserDefaults] boolForKey:@"firstLaunch"]) {  
        WelcomeViewController *welcomeVC = [[WelcomeViewController alloc] init];  
        [self.window setRootViewController:welcomeVC];  
        [welcomeVC release];  
    }else{  
        RootViewController *rootVC = [[RootViewController alloc] init];  
        [self.window setRootViewController:rootVC];  
        [rootVC release];  
    }  
}  
  
//保存多个自定义对象Student到NSUserDefaults中  
- (void)saveManyStudentInfo  
{  
    NSMutableArray *stuArray = [[NSMutableArray alloc] init];  
    for (int i = 0; i < 10; i++) {  
        Student *stu = [[Student alloc] init];  
        stu.name = [NSString stringWithFormat:@"Stu%d", i+1];  
        stu.age = [NSNumber numberWithInteger:i+1];  
        stu.height = [NSNumber numberWithFloat:170.3 + i];  
        [stuArray addObject:stu];  
        [stu release];  
    }  
    [[NSUserDefaults standardUserDefaults] setObject:[NSKeyedArchiver archivedDataWithRootObject:stuArray] forKey:@"students"];  
}  
  
//从nsuserDefaults加载多个自定义对象Student  
- (NSArray *)loadManyStudentWithKey:(NSString *)key  
{  
      
    NSData *data = [[NSUserDefaults standardUserDefaults] objectForKey:@"students"];  
    NSArray *stuArray = [NSKeyedUnarchiver unarchiveObjectWithData:data];  
    return [stuArray retain];  
}  
  
//保存自定义的对象到NSUserDefaults中  
- (void)saveCustomObjectToUerDefaults:(Student *)student  
{  
    NSData *encodedCustomObject = [NSKeyedArchiver archivedDataWithRootObject:student];  
    NSUserDefaults *userDefaults = [NSUserDefaults standardUserDefaults];  
    [userDefaults setObject:encodedCustomObject forKey:@"student"];  
    [userDefaults synchronize];  
}  
  
//从NSuserDefaults中加载自定义的对象  
- (Student *)loadCustomObjectWithKey:(NSString *)key  
{  
    NSUserDefaults *userDefaults = [NSUserDefaults standardUserDefaults];  
    NSData *stuData = [userDefaults objectForKey:key];  
    Student *student = [NSKeyedUnarchiver unarchiveObjectWithData:stuData];  
      
    return student;  
}  
  
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions  
{  
    self.window = [[[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]] autorelease];  
    // Override point for customization after application launch.  
  
    [self isFirstLaunch];  
      
    Student *stu = [self loadCustomObjectWithKey:@"student"];  
    NSLog(@"stu.name = %@\nstu.age = %d\nstu.height = %f\n", stu.name, [stu.age integerValue], [stu.height floatValue]);  
      
    NSArray *arr = [self loadManyStudentWithKey:@"students"];  
      
    for (Student *student in arr) {  
        NSLog(@"stu.name = %@\nstu.age = %d\nstu.height = %f\n", student.name, [student.age integerValue], [student.height floatValue]);  
    }  
      
    self.window.backgroundColor = [UIColor whiteColor];  
    [self.window makeKeyAndVisible];  
    return YES;  
}  
  
- (void)applicationWillResignActive:(UIApplication *)application  
{  
    // Sent when the application is about to move from active to inactive state. This can occur for certain types of temporary interruptions (such as an incoming phone call or SMS message) or when the user quits the application and it begins the transition to the background state.  
    // Use this method to pause ongoing tasks, disable timers, and throttle down OpenGL ES frame rates. Games should use this method to pause the game.  
}  
  
- (void)applicationDidEnterBackground:(UIApplication *)application  
{  
    // Use this method to release shared resources, save user data, invalidate timers, and store enough application state information to restore your application to its current state in case it is terminated later.   
    // If your application supports background execution, this method is called instead of applicationWillTerminate: when the user quits.  
}  
  
- (void)applicationWillEnterForeground:(UIApplication *)application  
{  
    // Called as part of the transition from the background to the inactive state; here you can undo many of the changes made on entering the background.  
}  
  
- (void)applicationDidBecomeActive:(UIApplication *)application  
{  
    // Restart any tasks that were paused (or not yet started) while the application was inactive. If the application was previously in the background, optionally refresh the user interface.  
}  
  
- (void)applicationWillTerminate:(UIApplication *)application  
{  
    // Called when the application is about to terminate. Save data if appropriate. See also applicationDidEnterBackground:.  
}  
  
@end  
```


从NSUserDefaults返回的值都是不可变的。即使你设置了一个可变的值。例如，你设置了一个可变的字符串作为“MyStringDefault”的值，你用stringForKey:获取到的字符串将是不可变的。

默认数据库是为用户自动创建的。NSUserDefaults目前不支持per-host的偏好(设置)。要做到这一点，你必须使用CFPreferences API.然而，NSUserDefaults正确的读取per-host的偏好，所以你可以安全地混合CFPreferences与NSUserDefaults的代码。

如果你的应用程序支持管理的环境，你可以使用一个NSUserDefaults对象去决定哪些对用户有利的喜好是由管理员管理的。受管理的环境中对应的计算机实验室或教室管理员或教师肯能需要以一种特定的方式配置系统。在这种情况下，教师会建立一组默认的偏好设置并且强制对用户使用。如果以这种方式管理偏好设置，应用程序应该防止用户编辑，禁用任何适当的控制。

NSUserDefaults类是线程安全的。

#### NSURL的持久性和文件引用的URLs
当使用NSURL实例参考文件内的过程中，重要的是要基于位置的跟踪与文件系统的身份跟踪之间的区别。当坚持一个NSURL，你应该考虑采取这种行为。如果你的应用程序跟踪位于其身份的资源，那么当用户移动文件的时候你可以发觉到，那么你应该明确的解NSURL的书签数据或者对文件引用的URL进行编码。

如果你想通过文件的引用跟踪文件，但是当解析时你需要显式控制，你需要关心的把书签数据写到NSUserDefaults中，而不是依靠[NSUserDefaults setURL:forKey:]，当你知道你的应用程序将能够处理潜在的I/O所需的用户界面交互时允许你调用
	
	[NSURL URLByResolvingBookmarkData:options:relativeToURL:bookmarkDataIsStale:error:]

#### 沙盒注意事项

一个沙盒应用不能访问和修改其他应用的偏好设置。例如，如果你使用addSuiteNamed:方法添加另一个应用程序，但是你不会获得该应用程序的偏好设置。

尝试去访问或者修改另一个应用的偏好设置不会导致错误，但是当你你做的时候，世纪上OS X读取和写入位于应用程序容器内的文件，而不是其他应用程序的偏好设置文件。

这就是使用NsuserDefaults来是数据持久化的，你可以[点击这里来下载NSUserDefaultsDemo](http://download.csdn.net/detail/swplzj/6587287)