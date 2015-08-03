---
layout: post
title: "iOS 数据持久化(一)"
date: 2014-08-09 21:46:33 +0800
comments: true
categories: 
---
iOS中有五种持久化数据的方式：属性列表、对象归档、NSUserDefault、SQLite3和Core Data
本文章讲述通过属性列表的方式持久化数据，这个方法也是我们平时最经常用到的方式。比如应用程序的配置和个性化的设置，一般都是通过属性列表（properties list） plist文件来存储和读取的。

<!--more-->

[objc] view plaincopy在CODE上查看代码片派生到我的代码片

	FOUNDATION_EXPORT NSArray *NSSearchPathForDirectoriesInDomains(NSSearchPathDirectory directory, NSSearchPathDomainMask domainMask, BOOL expandTilde);  
	
这个函数的作用：创建一个目录搜索路径的列表。
创建一个路径字符串列表指定域中指定的目录。列表中的顺序，你应该搜索目录。

PS：
通过此方法返回的目录可能不存在。这种方法只是给你请求目录的适当位置。根据应用的需求，它可能是开发者创建合适的目录或者在任何两者之间。

下面是一个把数据以plist的形式保存到本地的自定义类

```
//  
//  Plist.h  
//  PlistDemo  
//  
//  Created by swplzj on 13-11-20.  
//  Copyright (c) 2013年 swplzj. All rights reserved.  
//  
  
#import <Foundation/Foundation.h>  
  
@interface Plist : NSObject  
  
- (void)modifyData:(NSString *)dataKey Value:(NSString *)dataValue;  
- (void)deleteDataWithDataKey:(NSString *)key;  
  
@end  
```

```
//  
//  Plist.m  
//  PlistDemo  
//  
//  Created by swplzj on 13-11-20.  
//  Copyright (c) 2013年 swplzj. All rights reserved.  
//  
  
#import "Plist.h"  
  
@implementation Plist  
  
- (id)init  
{  
    if (self = [super init]) {  
        NSArray *paths = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES);  
        //获取完整路径  
        NSString *documentsDirectory = [paths objectAtIndex:0];  
        NSLog(@"homeDirectory = %@", documentsDirectory);  
        NSString *plistPath = [documentsDirectory stringByAppendingPathComponent:@"student.plist"];  
        //判断是否已经创建文件  
        if ([[NSFileManager defaultManager] fileExistsAtPath:plistPath]) {  
            NSLog(@"student.plist文件已经存在！");  
        }else {  
            //plist文件没有被创建  
            NSMutableDictionary *rootDic = [[NSMutableDictionary alloc] init];  
            NSMutableDictionary *subDic = [[NSMutableDictionary alloc] init];  
            [rootDic setObject:subDic forKey:@"student"];  
            [subDic release];  
            //写入到文件  
            [rootDic writeToFile:plistPath atomically:YES];  
            [rootDic release];  
        }  
    }  
      
    return self;  
}  
  
//把数据写入到plist文件中  
- (void)modifyData:(NSString *)dataKey Value:(NSString *)dataValue  
{  
    //获取路径  
    NSString *path = [[NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) objectAtIndex:0] stringByAppendingPathComponent:@"student.plist"];  
    NSMutableDictionary *rootDic = [[[NSMutableDictionary alloc] initWithContentsOfFile:path] mutableCopy];  
    NSMutableDictionary *studentInfo = [rootDic objectForKey:@"student"];  
    NSString *name = [studentInfo objectForKey:dataKey];  
    name = dataValue;  
    [studentInfo setValue:name forKey:dataKey];  
    //写入到文件  
    [rootDic writeToFile:path atomically:YES];  
    NSLog(@"Key - Value: %@ - %@",dataKey, dataValue );  
    [rootDic release];  
}  
  
//根据key来删除某一行的数据  
- (void)deleteDataWithDataKey:(NSString *)key  
{  
    //获取路径  
    NSString *path = [[NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) objectAtIndex:0] stringByAppendingPathComponent:@"student.plist"];  
    NSMutableDictionary *rootDic = [[[NSMutableDictionary alloc] initWithContentsOfFile:path] mutableCopy];  
    NSMutableDictionary *studentInfo = [rootDic objectForKey:@"student"];  
    [studentInfo removeObjectForKey:key];  
    NSLog(@"Delete data key - value: %@ - %@", key, [studentInfo objectForKey:key]);  
    [rootDic setValue:studentInfo forKey:@"student"];  
    //写入到文件  
    [rootDic writeToFile:path atomically:YES];  
    [rootDic release];  
}  
  
@end  
```

##### 在RootViewController里面的代码

```
//  
//  RootViewController.m  
//  PlistDemo  
//  
//  Created by swplzj on 13-11-20.  
//  Copyright (c) 2013年 swplzj. All rights reserved.  
//  
  
#import "RootViewController.h"  
#import "Plist.h"  
  
@interface RootViewController ()  
  
@property (retain, nonatomic) Plist *plistFile;  
  
@property (retain, nonatomic) IBOutlet UITextField *keyTF;  
@property (retain, nonatomic) IBOutlet UITextField *valueTF;  
@property (retain, nonatomic) IBOutlet UITextField *deleteKeyTF;  
@end  
  
@implementation RootViewController  
  
- (id)initWithNibName:(NSString *)nibNameOrNil bundle:(NSBundle *)nibBundleOrNil  
{  
    self = [super initWithNibName:nibNameOrNil bundle:nibBundleOrNil];  
    if (self) {  
        // Custom initialization  
    }  
    return self;  
}  
  
- (void)viewDidLoad  
{  
    [super viewDidLoad];  
    // Do any additional setup after loading the view from its nib.  
    _plistFile = [[Plist alloc] init];  
}  
  
- (void)didReceiveMemoryWarning  
{  
    [super didReceiveMemoryWarning];  
    // Dispose of any resources that can be recreated.  
}  
  
- (IBAction)addOrModifyBtnClicked:(id)sender {  
    if (![_keyTF.text isEqualToString:@""] && ![_valueTF.text isEqualToString:@""]) {  
        [_plistFile modifyData:_keyTF.text Value:_valueTF.text];   
    }  
}  
  
- (IBAction)deleteBtnClicked:(id)sender {  
    if (![_deleteKeyTF.text isEqualToString:@""]) {  
        [_plistFile deleteDataWithDataKey:_deleteKeyTF.text];  
    }  
}  
  
- (void)dealloc {  
    [_plistFile release];  
    [_keyTF release];  
    [_valueTF release];  
    [_deleteKeyTF release];  
    [super dealloc];  
}  
@end  
```

##### 效果

![id1](http://img.blog.csdn.net/20131120140040890?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3dwbHpq/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

##### 前往文件夹：

	/Users/issuser/Library/Application Support/iPhone Simulator/6.1/Applications/A40F6B2C-002A-4797-B501-3ABCBE59723A/Documents
	
![id2](http://img.blog.csdn.net/20131120140111546?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3dwbHpq/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



##### plist文件中的内容：

![id3](http://img.blog.csdn.net/20131120140135937?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3dwbHpq/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


好了，这就是通过属性列表plist文件来达到数据持久化的目的。