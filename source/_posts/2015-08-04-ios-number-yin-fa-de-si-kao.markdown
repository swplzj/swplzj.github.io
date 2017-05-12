---
layout: post
title: "iOS - '#'引发的思考"
date: 2015-08-04 18:37:45 +0800
comments: true
categories: 
---

在代码中使用 `Autolayout` 时，大家都会使用 `NSDictionaryOfVariableBindings` 这个宏，这个宏可以生成一个变量名到变量值映射的 `Dictionary`。比如 `NSDictionaryOfVariableBindings(button1, button2)` 将会生成一个`{ @"button1" = button1, @"button2 = button2 }` 的 `Dictionary`。它是怎么做到的呢？我们来看看这个宏的定义：

```
#define NSDictionaryOfVariableBindings(...) _NSDictionaryOfVariableBindings(@"" # __VA_ARGS__, __VA_ARGS__, nil)
```

这个宏定义中有3个参数，后两个参数不难理解，但第一个参数中间有个 `#` 符号，语法上看起来比较怪异，这个是什么呢？以前在做越狱的 `mobilesubstrate` 开发时，其中定义的一堆宏频繁使用了这个符号，下面就来揭开 `#` 这个符号在宏定义中的迷雾。

<!--more-->

### 预编译的一些知识

我们的代码在 `build` 时并不是直接进行编译的，在编译之前还进行了预编译处理。预编译会把 `include` 或 `import` 的文件导入到文件中，同时会将代码中用到的宏进行替换。注意宏是直接在代码中替换成宏的定义的，如果有嵌套也会逐层替换。

### “#”指示一些预编译命令

预编译命令一般都是以 `#` 开头的，比如 `#include`、`#import`、`#if` 等，在这里就不一一说明了，本文主要说明一下 `#` 在宏定义里面的一些作用。

### 宏参数字符串化

在一个参数前加一个`#`，预处理时将会变成这个参数名的字符串常量，即字符串化（`stringify`）。比如:

```
#define GET_NAME(X) #X
int a = 0;
NSLog(@"%s",GET_NAME(a));      //output: "a"
NSLog(@"%s",GET_NAME(a+3));    //output: "a+3"
```

将会得到以下输出：

```
a
a+3
```

可以看出`#`，将参数原样转换成字符串常量，如果参数是一个表达式，那么输出这个表达式的原样字符串常量。

回头再看看`NSDictionaryOfVariableBindings`的定义：

```
#define NSDictionaryOfVariableBindings(...) _NSDictionaryOfVariableBindings(@"" # __VA_ARGS__, __VA_ARGS__, nil)
```

如果这样生成两个`button`的映射：

	NSDictionaryOfVariableBindings(button1, button2);
	
那么预编译时就会转换成：

	_NSDictionaryOfVariableBindings(@"""button1, button2", button1, button2, nil);
	
由于两个常量字符串放在一起就是字符串常量串联，将变成两个字符串常量组合在一起的字符串常量，也就是上面是一个空字符串`""`和`"button1, button2"`串联，所以上面的代码等价于：

	_NSDictionaryOfVariableBindings(@"button1, button2", button1, button2, nil);
	
那么`_NSDictionaryOfVariableBindings`函数就可以将它的第一个参数按逗号,分割开作为`key`，后面就是各个`key`对应的值了。因此这段代码就创建了一个内容为`{ @"button1" = button1, @"button2 = button2 }`的`Dictionary`。

### 命名的串联

`#`在宏定义中的另一个作用就是用于命名的串联，用`##`就可以串联它左右两边的命名，比如以下代码：

```
#define CONCAT(X, Y) X ## Y
NSString *helloworld = @"Hello, world!";
NSLog(@"%@",CONCAT(hello, world)); //output: "Hello, world"
```

`CONCAT(hello, world)`实际被转换成`helloworld`。注意一下，因为宏是预编译阶段进行展开的，就是说在编译之前，因此代码中的`hello`和`world`即使没有定义其实也是没问题的，预编译处理后，这两个命名是不存在的。

### 可选可变参数

`##`在宏定义中可以放在`__VA_ARGS__`之前表示可变参数可以为空，否则的话可变参数至少为一个了。

```
#define MYLOG(format, ...) NSLog(format, ##__VA_ARGS__)
MYLOG(@"Don't make an error!");
```
上面代码中`MLOG`中只有一个参数，如果不加`##`，则`MLOG`至少需要两个参数，在`Xcode`里将会出现编译错误。

