---
layout: post
title: "valueForKeyPath的使用总结"
date: 2015-01-16 21:10:46 +0800
comments: true
categories: 
---
可能大家对- (id)valueForKeyPath:(NSString *)keyPath方法不是很了解。
其实这个方法非常的强大，具体有以下几个功能:

##### 对数组中的每个元素执行成员实例方法

<!--more-->

```
NSArray *array = @[@"name", @"w", @"aa", @"jimsa"];
NSLog(@"%@", [array valueForKeyPath:@"uppercaseString"]);
```

输出

```
(
    NAME,
    W,
    AA,
    JIMSA
)
```

相当于数组中的每个成员执行了uppercaseString方法，然后把返回的对象组成一个新数组返回。既然可以用uppercaseString方法，那么NSString的其他方法也可以，比如

```
[array valueForKeyPath:@"length"]
```

返回每个字符串长度的组成的数组。只要你能想到的成员实例方法都可以这么用。

##### 对NSNumber数组快速计算数组求和、平均数、最大值、最小值

```
NSArray *array = @[@1, @2, @3, @4, @10];
NSNumber *sum = [array valueForKeyPath:@"@sum.self"];
NSNumber *avg = [array valueForKeyPath:@"@avg.self"];
NSNumber *max = [array valueForKeyPath:@"@max.self"];
NSNumber *min = [array valueForKeyPath:@"@min.self"];
```    
##### 或者指定输出类型

```
NSNumber *sum = [array valueForKeyPath:@"@sum.floatValue"];
NSNumber *avg = [array valueForKeyPath:@"@avg.floatValue"];
NSNumber *max = [array valueForKeyPath:@"@max.floatValue"];
NSNumber *min = [array valueForKeyPath:@"@min.floatValue"];
```
    
##### 剔除重复数据

```
NSArray *array = @[@"name", @"w", @"aa", @"jimsa", @"aa"];
NSLog(@"%@", [array valueForKeyPath:@"@distinctUnionOfObjects.self"]);
```
    
打印

```
(
name,
w,
jimsa,
aa
)
```

##### 对NSDictionary数组快速找出相应key对的值

```
NSArray *array = @[@{@"name" : @"cookeee",@"code" : @1},
                           @{@"name": @"jim",@"code" : @2},
                           @{@"name": @"jim",@"code" : @1},
                           @{@"name": @"jbos",@"code" : @1}];
NSLog(@"%@", [array valueForKeyPath:@"name"]);
```

直接得到字典中namekey对应的值组成的数组，显然比循环取值再加入到新数组中方便快捷

```
(
    cookeee,
    jim,
    jim,
    jbos
)
```

同样可以嵌套使用，先剔除name对应值的重复数据再取值

```
 NSArray *array = @[@{@"name" : @"cookeee",@"code" : @1},
                           @{@"name": @"jim",@"code" : @2},
                           @{@"name": @"jim",@"code" : @1},
                           @{@"name": @"jbos",@"code" : @1}];

NSLog(@"%@", [array valueForKeyPath:@"@distinctUnionOfObjects.name"]);
```

打印

```
(
cookeee,
jim,
jbos
)
```

##### 改变UITextfiedl的placeholder的颜色

```
[searchField setValue:[UIColor whiteColor] forKeyPath:@"_placeholderLabel.textColor"];
```
    
比起重写

```
- (void)drawPlaceholderInRect:(CGRect)rect;
```

要方便很多