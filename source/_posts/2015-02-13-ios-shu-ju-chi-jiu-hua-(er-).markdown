---
layout: post
title: "iOS 数据持久化（二）"
date: 2014-08-12 22:59:06 +0800
comments: true
categories: 
---
iOS中有五种持久化数据的方式：属性列表、对象归档、NSUserDefaults、SQLite3和Core Data。
本文介绍对象归档来持久化数据。归档的作用就是将对象以文件的形式保存到磁盘中，以使得数据序列化和持久化。

<!--more-->

使用归档的时候读取该文件保存路径来读取文件的内容，归档的文件是进行过保密处理的，在磁盘上是无法查看文件的内容的，这也是和属性列表的区别：属性列表是明文，可以直接从磁盘查看其内容。
使用归档需要使用NSCoding协议中的代理两个方法

[objc] view plaincopy在CODE上查看代码片派生到我的代码片

```
@protocol NSCoding  
  
- (void)encodeWithCoder:(NSCoder *)aCoder;  
- (id)initWithCoder:(NSCoder *)aDecoder;  
  
@end  
```

#### 自定义类进行归档：

```
//  
//  ScoreCard.h  
//  ArchiverDemo  
//  
//  Created by swplzj on 13-11-19.  
//  Copyright (c) 2013年 swplzj. All rights reserved.  
//  
  
#import <Foundation/Foundation.h>  
//遵循NSCoding协议  
@interface ScoreCard : NSObject<NSCoding>  
{  
    NSString *_bestTime;  
    NSMutableArray *_allTimes;  
}  
  
@property (copy, nonatomic) NSString *bestTime;  
@property (copy) NSMutableArray *allTimes;  
  
@end  
```

```
//  
//  ScoreCard.m  
//  ArchiverDemo  
//  
//  Created by swplzj on 13-11-19.  
//  Copyright (c) 2013年 swplzj. All rights reserved.  
//  
  
#import "ScoreCard.h"  
  
@implementation ScoreCard  
  
@synthesize bestTime = _bestTime;  
@synthesize allTimes = _allTimes;  
  
- (void)dealloc  
{  
    [_bestTime release];  
    [_allTimes release];  
      
    [super dealloc];  
}  
  
- (id)init  
{  
    if (self = [super init]) {  
        _bestTime = [[NSString alloc] init];  
        _allTimes = [[NSMutableArray alloc] init];  
    }  
    return self;  
}  
  
//解码方法  
- (id)initWithCoder:(NSCoder *)aDecoder  
{  
    if (self = [super init]) {  
        _bestTime = [[aDecoder decodeObjectForKey:@"bestTime"] retain];  
        _allTimes = [[aDecoder decodeObjectForKey:@"allTimes"] retain];  
    }  
    return self;  
}  
  
//编码方法  
- (void)encodeWithCoder:(NSCoder *)aCoder  
{  
    [aCoder encodeObject:_bestTime forKey:@"bestTime"];  
    [aCoder encodeObject:_allTimes forKey:@"allTimes"];  
}  
  
@end  
```

```
//  
//  Athlete.h  
//  ArchiverDemo  
//  
//  Created by swplzj on 13-11-19.  
//  Copyright (c) 2013年 swplzj. All rights reserved.  
//  
  
#import <Foundation/Foundation.h>  
  
@class ScoreCard;  
  
//遵守NSCoding 协议  
@interface Athlete : NSObject<NSCoding>  
{  
    NSString *_name;  
    NSString *_bio;  
    NSString *_phoneNumber;  
    ScoreCard *_scoreCard;  
    BOOL _eligible;  
}  
  
@property (copy) NSString *name, *bio, *phoneNumber;  
@property (retain) ScoreCard *scoreCard;  
@property (getter = isEligible) BOOL eligible;  
  
- (void)print;  
  
@end  
```

```
//  
//  Athlete.m  
//  ArchiverDemo  
//  
//  Created by swplzj on 13-11-19.  
//  Copyright (c) 2013年 swplzj. All rights reserved.  
//  
  
#import "Athlete.h"  
#import "ScoreCard.h"  
  
@implementation Athlete  
  
@synthesize name = _name, bio = _bio, phoneNumber = _phoneNumber, scoreCard = _scoreCard, eligible = _eligible;  
  
- (void)dealloc  
{  
    [_name release];  
    [_bio release];  
    [_scoreCard release];  
  
    [super dealloc];  
}  
  
- (id)init  
{  
    if (self = [super init]) {  
        _name = [[NSString alloc] init];  
        _bio = [[NSString alloc] init];  
        _phoneNumber = [[NSString alloc] init];  
        _scoreCard = [[ScoreCard alloc] init];  
        _eligible = YES;  
    }  
    return self;  
}  
  
//解码方法  
- (id)initWithCoder:(NSCoder *)aDecoder  
{  
    if (self = [super init]) {  
        _name = [[aDecoder decodeObjectForKey:@"name"] retain];  
        _bio = [[aDecoder decodeObjectForKey:@"bio"] retain];  
        _scoreCard = [[aDecoder decodeObjectForKey:@"scoreCard"] retain];  
        //BOOL类型变量解码  
        _eligible = [aDecoder decodeBoolForKey:@"eligible"];  
    }  
      
    return self;  
}  
  
//编码方法  
- (void)encodeWithCoder:(NSCoder *)aCoder  
{  
    [aCoder encodeObject:_name forKey:@"name"];  
    [aCoder encodeObject:_bio forKey:@"bio"];  
    [aCoder encodeObject:_scoreCard forKey:@"scoreCard"];  
    //BOOL类型变量归档  
    [aCoder encodeBool:_eligible forKey:@"eligible"];  
}  
  
- (void)print  
{  
    NSLog(@"Name: %@\nBio: %@\nTel: %@\n\nBest Time: %@\n\nAll Times:", _name, _bio, _phoneNumber, [_scoreCard bestTime]);  
    for (NSString *time in [_scoreCard allTimes]) {  
        NSLog(@"%@", time);  
    }  
}  
  
@end  
```

```
//  
//  Roster.h  
//  ArchiverDemo  
//  
//  Created by swplzj on 13-11-19.  
//  Copyright (c) 2013年 swplzj. All rights reserved.  
//  
  
#import <Foundation/Foundation.h>  
  
@class Athlete;  
  
//遵循归档NSCoding协议  
@interface Roster : NSObject<NSCoding>  
{  
    NSMutableArray *_athletes;  
    int _rank;  
}  
  
@property (retain) NSMutableArray *athletes;  
@property int rank;  
  
- (void)create;  
- (void)print;  
- (void)addAthlete:(Athlete *)athlete;  
  
@end  

[objc] view plaincopy在CODE上查看代码片派生到我的代码片
//  
//  Roster.m  
//  ArchiverDemo  
//  
//  Created by swplzj on 13-11-19.  
//  Copyright (c) 2013年 swplzj. All rights reserved.  
//  
  
#import "Roster.h"  
#import "ScoreCard.h"  
#import "Athlete.h"  
  
static NSString *names [] = { @"John Doe",  
    @"Jane Doe", @"Shaun White", @"Jeff Beck",  
    @"Eric Clapton", @"Angus Young", @"Flavius Josephus" };  
  
@implementation Roster  
  
@synthesize athletes = _athletes, rank = _rank;  
  
- (void)dealloc  
{  
    [_athletes release];  
      
    [super dealloc];  
}  
  
- (void)create  
{  
    NSMutableArray *scoreArray = [NSMutableArray arrayWithObjects:@"21:32:21", @"12:48:11", @"16:11:32", nil nil];  
    for(int i = 0; i < 7; ++i){  
        Athlete *athlete = [[Athlete alloc] init];  
        athlete.name = names[i];  
        athlete.bio = @"I'm a boss";  
        [athlete setPhoneNumber:@"321-3241"];  
        athlete.scoreCard.bestTime = @"12:30:22";  
        athlete.scoreCard.allTimes = scoreArray;  
        [self addAthlete:athlete];  
        [athlete release];  
    }  
}  
  
- (id)init  
{  
    if (self = [super init]) {  
        _rank = 0;  
        _athletes = [[NSMutableArray alloc] init];  
    }  
    return self;  
}  
  
//解码方法  
- (id)initWithCoder:(NSCoder *)aDecoder  
{  
    if (self = [super init]) {  
        _athletes = [[aDecoder decodeObjectForKey:@"athletes"] retain];  
        //解码int类型变量  
        _rank = [aDecoder decodeIntForKey:@"rank"];  
    }  
    return self;  
}  
  
- (void)encodeWithCoder:(NSCoder *)aCoder  
{  
    [aCoder encodeObject:_athletes forKey:@"athletes"];  
    [aCoder encodeInt:_rank forKey:@"rank"];  
}  
  
- (void)addAthlete:(Athlete *)athlete  
{  
    [_athletes addObject:athlete];  
}  
  
- (void)print  
{  
    NSLog(@"Roster info:\nRank: %d", _rank);  
    for (Athlete *athlete in _athletes) {  
        NSLog(@"%@", [athlete name]);  
    }  
}  
  
@end  
```

#### 在main函数里面进行归档

```
//  
//  main.m  
//  ArchiverDemo  
//  
//  Created by swplzj on 13-11-19.  
//  Copyright (c) 2013年 swplzj. All rights reserved.  
//  
  
#import <UIKit/UIKit.h>  
#import "Roster.h"  
  
#define ARCHIVE 0  
#define UNARCHIVE 1  
  
int main(int argc, charchar *argv[])  
{  
    NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];  
    //获得根路径  
    NSString *homePath = [[NSBundle mainBundle] executablePath];  
    //按照“/”分解字符串到数组  
    NSArray *strings = [homePath componentsSeparatedByString:@"/"];  
    //  
    NSString *executbleName = [strings objectAtIndex:[strings count] - 1];  
    NSString *baseDirectory = [homePath substringToIndex:[homePath length] - [executbleName length] - 1];  
    //文件名字  
    NSString *fileName = [NSString stringWithFormat:@"%@/roster.archive", baseDirectory];  
    NSLog(@"filePath: %@", fileName);  
  
#if ARCHIVE  
    //创建并归档一个roster  
    Roster *roster = [[Roster alloc] init];  
    [roster create];  
    //把对象写到二进制流中去  
    [NSKeyedArchiver archiveRootObject:roster toFile:fileName];  
    [roster release];  
#endif  
      
#if UNARCHIVE  
    //已经写入到磁盘，直接从二进制流读取对象  
    Roster *unarchive = [NSKeyedUnarchiver unarchiveObjectWithFile:fileName];  
    [unarchive print];  
    for (Athlete *athlete in [unarchive athletes]) {  
        [athlete print];  
    }  
    [unarchive release];  
#endif  
      
    [pool release];  
      
    return 0;  
}  
```

#### 运行程序，前往文件夹：

	/Users/issuser/Library/Application Support/iPhone Simulator/6.1/Applications/74C8943C-BE5F-4BB7-8385-8E922AF29589/ArchiverDemo.app/roster.archive

#### 归档成功。
[点击这里下载ArchiverDemo](http://download.csdn.net/detail/swplzj/6581137)

这就是使用归档来达到数据的持久化。你也来试试吧。