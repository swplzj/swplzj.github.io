---
layout: post
title: "Xcode中系统库文件libz.dylib的作用"
date: 2014-09-24 22:08:21 +0800
comments: true
categories: 
---
libz.dylib这个Xcode系统库文件经常用到。这个其实是个动态链接库。

后缀名为.dylib的文件是一个动态库，这个库是运行时加载而不是编译时加载。这个也说明了obj-C是运行时语言，也就是数据的类型不是在编译时确定的，而是在运行时确定的。

libz.dylib也是zip压缩解压缩的库，开发中经常用到的压缩库，PDF可以使用这个库文件来压缩。

<!--more-->

在Finder中查看，你会发现libz.dylib,libz.1.dylib,libz.1.1.3.dylib其实只是一个“替身”，实际上还是指向libz.1.2.5.dylib(Xcode 4.5)
A file ending in the extension .dylib is a dynamic library: it's a library that's loaded at runtime instead of at compile time. If you're familiar with DLLs from Windows or DSOs, it's more or less the same type of thing with a few twists. [The Dynamic Library Programming Topics](https://developer.apple.com/library/mac/#documentation/DeveloperTools/Conceptual/DynamicLibraries/000-Introduction/Introduction.html#//apple_ref/doc/uid/TP40001908-SW1) section of the Mac OS X Developer Library covers all the details about the format and what you should be aware of.

libz.dylib is the dynamic library for Zlib, a general compression library. PDFs can (and usually do) use zlib to compress different aspects of the data contained within them, but accessing the PDF data at that level is pretty low-level, and higher-level libraries would abstract most of that type of stuff.

总结：

1. .dylib意味着这是一个动态链接库.

2. libz.dylib是提供zip压缩解压缩的库

3. 库的接口请#import "zlib.h"