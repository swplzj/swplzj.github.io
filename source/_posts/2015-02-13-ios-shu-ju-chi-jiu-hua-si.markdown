---
layout: post
title: "iOS 数据持久化(四)"
date: 2014-08-29 23:11:36 +0800
comments: true
categories: 
---
iOS中有五种持久化数据的方式：属性列表、对象归档、NSUserDefaults、SQLite3和Core Data

<!--more-->

#### SQLite3的数据类型

数据库在应用中最常用的，方便说数据的存取。
所有存在Sqlite 3.0版本当中的数据都拥有以下之一的数据类型：
空（NULL）：该值为空
整型（INTEGEER）：有符号整数，按大小被存储成1,2,3,4,6或8字节。
实数（REAL）：浮点数，以8字节指数形式存储。
文本（TEXT）：字符串，以数据库编码方式存储（UTF-8, UTF-16BE 或者 UTF-16-LE）。
BLOB：BLOB数据不做任何转换，以输入形式存储。

在关系数据库中，CLOB和BLOB类型被用来存放大对象。BOLB表示二进制大对象，这种数据类型通过用来保存图片，图象，视频等。CLOB表示字符大对象，能够存放大量基于字符的数据。


#### iOS中数据库SQLite3的使用

具体使用方法如下:

1.添加开发包libsqlite3.0.dylib

首先是设置项目文件，在项目中添加iPhone版的sqlite3的数据库的开发包，在项目下的Frameworks点击右键，然后选择libsqlite3.0.dylib文件。

![id1](http://img.blog.csdn.net/20131220172258390?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3dwbHpq/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

libsqlite3.0.dylib文件地址: 

	/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS2.2.sdk/usr/lib/libsqlite3.0.dylib

2.有码有真相：

加入FMDB这个第三方库文件，项目编程开始。

#### AppDelegate.h文件

```
//  
//  AppDelegate.h  
//  SQLite3Demo  
//  
//  Created by 李振杰 on 13-11-22.  
//  Copyright (c) 2013年 swplzj. All rights reserved.  
//  
  
#import <UIKit/UIKit.h>  
  
@interface AppDelegate : UIResponder <UIApplicationDelegate>  
  
@property (strong, nonatomic) UIWindow *window;  
  
/** 
 *  @brief  在状态栏处显示的提示框 
 * 
 *  @param string   显示的字符串 
 *  @param duration 显示的时长 
 */  
+ (void)showStatusWithText:(NSString *)string duration:(NSTimeInterval)duration;  
  
@end  
```

#### AppDelegate.m文件

```
//  
//  AppDelegate.m  
//  SQLite3Demo  
//  
//  Created by 李振杰 on 13-11-22.  
//  Copyright (c) 2013年 swplzj. All rights reserved.  
//  
  
#import "AppDelegate.h"  
#import "RootViewController.h"  
  
@interface AppDelegate ()  
  
@property (strong, nonatomic) UIWindow *statusWindow;  
@property (retain, nonatomic) UILabel *statusLabel;  
  
- (void)dismissStatusLabel;  
  
@end  
  
@implementation AppDelegate  
  
- (void)dealloc  
{  
    [self.statusWindow release];  
    [self.statusLabel release];  
      
    [_window release];  
    [super dealloc];  
}  
  
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions  
{  
    self.window = [[[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]] autorelease];  
   
    self.statusWindow = [[UIWindow alloc] initWithFrame:CGRectZero];  
    self.statusWindow.backgroundColor = [UIColor clearColor];  
    self.statusWindow.windowLevel = UIWindowLevelStatusBar + 1;  
  
    self.statusLabel = [[UILabel alloc] initWithFrame:CGRectZero];  
    self.statusLabel.backgroundColor = [UIColor clearColor];  
    self.statusLabel.textColor = [UIColor whiteColor];  
    self.statusLabel.font = [UIFont systemFontOfSize:13];  
    [self.statusWindow addSubview:self.statusLabel];  
    [self.statusWindow makeKeyAndVisible];  
      
    RootViewController *rootVC = [[RootViewController alloc] initWithNibName:@"RootViewController" bundle:nil];  
    UINavigationController *rootNav = [[UINavigationController alloc] initWithRootViewController:rootVC];  
    [rootVC release];  
      
    self.window.rootViewController = rootNav;  
    [rootNav release];  
      
    // Override point for customization after application launch.  
    self.window.backgroundColor = [UIColor whiteColor];  
    [self.window makeKeyAndVisible];  
    return YES;  
}  
  
#pragma mark - Custom methods  
  
- (void)dismissStatusLabel  
{  
    CGRect rect = self.statusWindow.frame;  
    rect.origin.y -= rect.size.height;  
    [UIView animateWithDuration:0.8 animations:^{  
        self.statusWindow.frame = rect;  
    }];  
}  
  
/** 
 *  @brief  在状态栏处显示的提示框 
 * 
 *  @param string   显示的字符串 
 *  @param duration 显示的时长 
 */  
+ (void)showStatusWithText:(NSString *)string duration:(NSTimeInterval)duration  
{  
    AppDelegate *delegate = (AppDelegate *)[UIApplication sharedApplication].delegate;  
      
    delegate.statusLabel.text = string;  
    [delegate.statusLabel sizeToFit];  
    CGRect rect = [UIApplication sharedApplication].statusBarFrame;  
    CGFloat width = delegate.statusLabel.frame.size.width;  
    CGFloat height = rect.size.height;  
    rect.origin.x = rect.size.width = width - 5;  
    rect.size.width = width;  
    delegate.statusWindow.frame = rect;  
    delegate.statusLabel.backgroundColor = [UIColor blackColor];  
    delegate.statusLabel.frame = CGRectMake(100, 0, width, height);  
    duration = (duration < 1.0 ? 1.0 : duration);  
    duration = (duration > 4.0 ? 4.0 : duration);  
    [delegate performSelector:@selector(dismissStatusLabel)];  
}  
  
@end  
```

#### 使用FMDB第三方库，数据库管理文件

```
//  
//  DBManager.h  
//  SQLite3Demo  
//  
//  Created by 李振杰 on 13-11-26.  
//  Copyright (c) 2013年 swplzj. All rights reserved.  
//  
  
#import <Foundation/Foundation.h>  
  
#import "FMDatabaseAdditions.h"  
  
@class FMDatabase;  
  
/** 
 *  @brief 对数据连接进行管理，包括连接，关闭连接 
 *  可以建立长连接 
 * 
 */  
@interface DBManager : NSObject{  
    NSString *_dbPath;  
}  
  
@property (readonly, nonatomic) FMDatabase *dataBase;  
  
//单例模式  
+ (DBManager *)defaultDBManager;  
  
//关闭数据库  
- (void)close;  
  
@end  
```

```
//  
//  DBManager.m  
//  SQLite3Demo  
//  
//  Created by 李振杰 on 13-11-26.  
//  Copyright (c) 2013年 swplzj. All rights reserved.  
//  
  
#import "DBManager.h"  
#import "FMDatabase.h"  
  
//#define kDefaultDBName @"user.sqlite"  
#define kDefaultDBName @"user.db"  
  
@interface DBManager ()  
  
@end  
  
static DBManager *_sharedDBManager;  
  
@implementation DBManager  
  
- (void)dealloc  
{  
    [self close];  
      
    [super dealloc];  
}  
  
- (id)init  
{  
    if (self = [super init]) {  
        int state = [self initializeDBWithName:kDefaultDBName];  
        if (-1 == state) {  
            NSLog(@"数据库初始化失败！");  
        }else {  
            NSLog(@"数据库初始化成功！");  
        }  
    }  
    return self;  
}  
  
- (int)initializeDBWithName:(NSString *)name  
{  
    if (!name) {  
        return -1;  //数据库创建失败  
    }  
      
    //沙盒Doc目录  
    NSString *doc = [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) objectAtIndex:0];  
    _dbPath = [doc stringByAppendingString:[NSString stringWithFormat:@"/%@", name]];  
    NSFileManager *fileManager = [NSFileManager defaultManager];  
    BOOL exist = [fileManager fileExistsAtPath:_dbPath];  
    [self connect];  
    return (exist == YES ? 0 : 1);  
}  
  
//连接数据库  
- (void)connect  
{  
    if (!_dataBase) {  
        _dataBase = [[FMDatabase alloc] initWithPath:_dbPath];  
    }  
    if (![_dataBase open]) {  
        NSLog(@"不能打开数据库");  
    }  
}  
  
- (void)close  
{  
    [_dataBase close];  
    [_sharedDBManager release];  
    _sharedDBManager = nil;  
}  
  
+ (DBManager *)defaultDBManager  
{  
    if (!_sharedDBManager) {  
        _sharedDBManager = [[DBManager alloc] init];  
    }  
    return _sharedDBManager;  
}  
  
  
@end  
```

#### 向创建的数据库文件中添加数据表，对表的增删改查

```
//  
//  UserDB.h  
//  SQLite3Demo  
//  
//  Created by 李振杰 on 13-11-26.  
//  Copyright (c) 2013年 swplzj. All rights reserved.  
//  
  
#import <Foundation/Foundation.h>  
#import "DBManager.h"  
#import "User.h"  
  
@interface UserDB : NSObject{  
    FMDatabase *_db;  
}  
//创建数据表  
- (void)createTable;  
//增：添加用户  
- (void)updateTableWithUser:(User *)user;  
//删：删除用户  
- (void)deleteTableWithUserID:(NSString *)uid;  
//改：修改用户信息  
- (void)modifyTableWithUser:(User *)user;  
//查：查找用户  
- (NSArray *)queryTableWithUid:(NSString *)uid count:(int)userCount;  
  
@end  
```

```
//  
//  UserDB.m  
//  SQLite3Demo  
//  
//  Created by 李振杰 on 13-11-26.  
//  Copyright (c) 2013年 swplzj. All rights reserved.  
//  
  
#import "UserDB.h"  
#import "AppDelegate.h"  
  
#define kUserTableName                             @"User"  
  
@implementation UserDB  
  
- (id)init  
{  
    self = [super init];  
    if (self) {  
        //首先查看有没有数据库文件，没有就创建  
        _db = [DBManager defaultDBManager].dataBase;  
    }  
    return self;  
}  
  
//创建数据表  
- (void)createTable  
{  
    FMResultSet *set = [_db executeQuery:[NSString stringWithFormat:@"select count(*) from sqlite_master where type = 'table' and name = '%@'", kUserTableName]];  
    [set next];  
      
    NSInteger count = [set intForColumnIndex:0];  
    BOOL existTable = !!count;  
    if (existTable) {  
        //更新数据库  
        [AppDelegate showStatusWithText:@"数据库已经存在！" duration:2];  
    } else {  
        //插入到数据库  
        NSString *sqlStr = @"CREATE TABLE User (uid INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL, name VARCHAR(50), description VARCHAR(100))";  
        BOOL res = [_db executeUpdate:sqlStr];  
        if (!res) {  
            [AppDelegate showStatusWithText:@"数据库创建失败！" duration:2];  
        } else {  
            [AppDelegate showStatusWithText:@"数据库创建成功！" duration:2];  
        }  
          
    }  
}  
  
//保存数据  
- (void)updateTableWithUser:(User *)user  
{  
    NSMutableString *query = [NSMutableString stringWithFormat:@"INSERT INTO User"];  
    NSMutableString *keys = [NSMutableString stringWithFormat:@" ("];  
    NSMutableString *values = [NSMutableString stringWithFormat:@" ( "];  
    NSMutableArray *arguments = [NSMutableArray arrayWithCapacity:5];  
    if (user.name) {  
        [keys appendString:@"name,"];  
        [values appendString:@"?,"];  
        [arguments addObject:user.name];  
    }  
    if (user.description) {  
        [keys appendString:@"description,"];  
        [values appendString:@"?,"];  
        [arguments addObject:user.description];  
    }  
      
    [keys appendString:@")"];  
    [values appendString:@")"];  
    [query appendFormat:@" %@ VALUES%@", [keys stringByReplacingOccurrencesOfString:@",)" withString:@")"], [values stringByReplacingOccurrencesOfString:@",)" withString:@")"]];  
    NSLog(@"插入一条语句：%@", query);  
    [AppDelegate showStatusWithText:@"插入一条数据!" duration:2];  
    [_db executeUpdate:query withArgumentsInArray:arguments];  
      
}  
  
//删除用户信息  
- (void)deleteTableWithUserID:(NSString *)uid  
{  
    NSString *query = [NSString stringWithFormat:@"DELETE FROM User WHERE uid = '%@'", uid];  
    [AppDelegate showStatusWithText:@"删除一条数据!" duration:2];  
    [_db executeUpdate:query];  
}  
  
//修改用户信息  
- (void)modifyTableWithUser:(User *)user  
{  
    if (!user.uid) {  
        return;  
    }  
    NSString *query = @"UPDATE User SET";  
    NSMutableString *temp = [NSMutableString stringWithCapacity:0];  
    if (user.name) {  
        [temp appendFormat:@"name = '%@',", user.name];  
    }  
    if (user.description) {  
        [temp appendFormat:@"description = '%@',", user.description];  
        [temp appendFormat:@")"];  
        query = [query stringByAppendingFormat:@"%@ WHERE uid = '%@'", [temp stringByReplacingOccurrencesOfString:@",)" withString:@""], user.uid];  
        NSLog(@"修改一条数据：%@", query);  
        [AppDelegate showStatusWithText:@"修改了一条数据!" duration:2];  
        [_db executeUpdate:query];  
    }  
}  
  
//查找用户  
- (NSArray *)queryTableWithUid:(NSString *)uid count:(int)userCount  
{  
    NSString *query = @"SELECT uid, name, description FROM User";  
    if (!uid) {  
        query = [query stringByAppendingFormat:@" ORDER BY uid DESC limit %d", userCount];  
    } else {  
        query = [query stringByAppendingFormat:@" WHERE uid > %@ ORDER BY uid DESC limit %d", uid, userCount];  
    }  
    NSLog(@"query = %@", query);  
    FMResultSet *rs = [_db executeQuery:query];  
    NSMutableArray *array = [NSMutableArray arrayWithCapacity:[rs columnCount]];  
    while ([rs next]) {  
        User *user = [[User alloc] init];  
        user.uid = [rs stringForColumn:@"uid"];  
        user.name = [rs stringForColumn:@"name"];  
        user.description = [rs stringForColumn:@"description"];  
        NSLog(@"user.uid = %@\nuser.name = %@\nuser.des = %@\n", user.uid, user.name, user.description);  
        [array addObject:user];  
        [user release];  
    }  
    [rs close];  
    NSLog(@"array = %@\n", array);  
    return array;  
}  
  
  
@end  
```

#### 数据模型类

```
//  
//  User.h  
//  SQLite3Demo  
//  
//  Created by 李振杰 on 13-11-26.  
//  Copyright (c) 2013年 swplzj. All rights reserved.  
//  
  
#import <Foundation/Foundation.h>  
  
@interface User : NSObject  
  
@property (copy, nonatomic) NSString *uid;  
@property (copy, nonatomic) NSString *name;  
@property (copy, nonatomic) NSString *description;  
  
@end  

[objc] view plaincopy在CODE上查看代码片派生到我的代码片
//  
//  User.m  
//  SQLite3Demo  
//  
//  Created by 李振杰 on 13-11-26.  
//  Copyright (c) 2013年 swplzj. All rights reserved.  
//  
  
#import "User.h"  
  
@implementation User  
  
@synthesize uid, name, description;  
  
@end  
```

关于数据库的操作在上面代码中基本都包含，你也可以[点击下载SQLite3Demo源码](http://download.csdn.net/detail/swplzj/6746635)。