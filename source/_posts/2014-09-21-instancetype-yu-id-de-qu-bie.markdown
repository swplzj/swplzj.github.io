---
layout: post
title: "INSTANCETYPE 与 ID 的区别。"
date: 2014-09-21 18:31:05 +0800
comments: true
categories: 
---
新的LLVM编译器为我们带来了ARC, Object Literal and Scripting, Auto Synthesis等特性，同时也引入了instancetype关键字。instancetype用来表示Related Result Types(相关返回类型)，那么它与id有什么不同呢？

根据Cocoa的命名惯例，init, alloc这类的方法，如果以id作为返回类型，会返回类本身的类型。

<!--more-->

```
@interface Person
- (id)initWithName:(NSString *)name;
+ (id)personWithName:(NSString *)name;
```

但类方法的返回类型，LLVM(或者说Clang)却无法判断，我们来看一段代码：

```
// You may get two warnings if you're using MRC rather than ARC

[[[NSArray alloc] init] mediaPlaybackAllowsAirPlay]; //  "No visible @interface for `NSArray` declares the selector `mediaPlaybackAllowsAirPlay`"
[[NSArray array] mediaPlaybackAllowsAirPlay]; // It's OK. But You'll get a runtime error instead of a compile time one
```

[NSArray array]除非显式转换为(NSArray *)，否则编译器不会有错误提示。如果使用instancetype就不会有这样的问题：

```
@interface Person
- (instancetype)initWithName:(NSString *)name;
+ (instancetype)personWithName:(NSString *)name;
```

简单来说，instancetype关键字，保证了编译器能够正确推断方法返回值的类型。这种技术基本从iOS 5的UINavigationController里就开始应用了。

Clang的文档里提到 instancetype
is a contextual keyword that is only permitted in the result type of an Objective-C method. 也就是说，instancetype只能作为返回值，不能像id那样作为参数。

* [ClangClang Language Extensions](http://clang.llvm.org/docs/LanguageExtensions.html#objective-c-features)
* [NSHipster](http://nshipster.com/instancetype/)
* [Typed Collections With Self Types in Objective-C](http://www.jonmsterling.com/posts/2012-02-05-typed-collections-with-self-types-in-objective-c.html)

