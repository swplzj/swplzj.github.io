---
layout: post
title: "使用神奇的PONYDEBUGGER调试iOS应用"
date: 2014-12-14 17:30:40 +0800
comments: true
categories: 
---

### 前言

Debug算是开发者的基本技能之一吧，对于iOS开发者而言，可能会遇到如下一些debug需求：

调试网络请求问题
调试一个层次结构复杂的用户界面
查看应用内Core Data数据变化
虽然你可以通过NSLog日志，打断点，抑或LLDB的调试命令等一些常见手段来解决以上问题，但这些方式始终不够直观，效率也不高，特别是对于调试UI这个需求。要提高效率，我们可以使用一些第三方的调试工具，比如本文要介绍的PonyDebugger。

<!--more-->

### 关于PonyDebugger

PonyDebugger是Square开源的一个远程调试工具包，它可以通过Chrome开发者工具来调试iOS应用的网络流量，数据存储以及用户界面。

### 安装和使用

#### 服务端安装

curl -sk https://cloud.github.com/downloads/square/PonyDebugger/bootstrap-ponyd.py | \ python - --ponyd-symlink=/usr/local/bin/ponyd ~/Library/PonyDebugger

安装时遇到的一些坑记录如下：

移动宽带下偶尔出现连接不上pypi的情况，使用电信宽带则正常，移动宽带用户可以改用国内的镜像地址，比如V2EX的pypi镜像http://pypi.v2ex.com/simple
服务端依赖pybonjour，但是在zsh终端下使用pip安装会提示找不到这个包，需要手动安装，添加allow-external和allow-unverified
手动安装方法如下：

```
source ~/Library/PonyDebugger/bin/activate

pip install -U -e git+https://github.com/square/PonyDebugger.git#egg=ponydebugger --allow-external pybonjour --allow-unverified pybonjour

ponyd update-devtools
```

#### 客户端安装

使用CocoaPods，在PodFile中添加以下依赖：

```
pod 'PonyDebugger', '~> 0.3.1'
```

也可以直接指向Github开源地址，这样可以随时使用最新版本的代码：

```
pod 'PonyDebugger', :git => 'https://github.com/square/PonyDebugger.git'
```

#### 使用

终端输入以下命令启动服务端，然后Chrome打开http://localhost:9000 ，使用开发者工具即可进行调试

ponyd serve --listen-interface=127.0.0.1

为了便于随时开启和关闭PonyDebugger，客户端在Preprocessor Macros中定义了一个宏ENABLE_PONYDEBUGGER

在AppDeleagate的didFinishLaunchingWithOptions方法中使用如下代码进行一些初始化工作：

```
#if ENABLE_PONYDEBUGGER

    PDDebugger *debugger = [PDDebugger defaultInstance];
    
    // Enable Network debugging, and automatically track network traffic that comes through any classes that NSURLConnectionDelegate methods.
    [debugger enableNetworkTrafficDebugging];
    [debugger forwardAllNetworkTraffic];
    
    // Enable Core Data debugging, and broadcast the main managed object context.
    [debugger enableCoreDataDebugging];
    [debugger addManagedObjectContext:self.managedObjectContext withName:@"Twitter Test MOC"];
    
    // Enable View Hierarchy debugging. This will swizzle UIView methods to monitor changes in the hierarchy
    // Choose a few UIView key paths to display as attributes of the dom nodes
    [debugger enableViewHierarchyDebugging];
    [debugger setDisplayedViewAttributeKeyPaths:@[@"frame", @"hidden", @"alpha", @"opaque", @"accessibilityLabel", @"text"]];
    
    // Connect to a specific host
    [debugger connectToURL:[NSURL URLWithString:@"ws://localhost:9000/device"]];
    // Or auto connect via bonjour discovery
    //[debugger autoConnect];
    // Or to a specific ponyd bonjour service
    //[debugger autoConnectToBonjourServiceNamed:@"MY PONY"];

    // Enable remote logging to the DevTools Console via PDLog()/PDLogObjects().
    [debugger enableRemoteLogging];
    
#endif
```

Chrome看到网页显示如下说明连接成功：


![id1](http://ww4.sinaimg.cn/large/674a21d2gw1eemkgp4t9rj216c0ea40f.jpg)

点击网页中间的应用名即可跳转到debug工具

#### 实现原理

简单看了一下PonyDebugger的源码，服务端使用Tornado框架提供WebSocket服务，客户端则是基于Square自家开源的SocketRocket，关于WebSocket相关的知识，可以参考我之前的这篇博文。

网络请求抓包功能是通过注入到NSURLConnectionDelegate中实现的，代码如下：


```
+ (void)injectIntoAllNSURLConnectionDelegateClasses;
{
    // Only allow swizzling once.
    static BOOL swizzled = NO;
    if (swizzled) {
        return;
    }
    
    swizzled = YES;

    // Swizzle any classes that implement one of these selectors.
    const SEL selectors[] = {
        @selector(connectionDidFinishLoading:),
        @selector(connection:didReceiveResponse:)
    };
    
    const int numSelectors = sizeof(selectors) / sizeof(SEL);

    Class *classes = NULL;
    NSInteger numClasses = objc_getClassList(NULL, 0);
    
    if (numClasses > 0) {
        classes = (__unsafe_unretained Class *)malloc(sizeof(Class) * numClasses);
        numClasses = objc_getClassList(classes, numClasses);
        for (NSInteger classIndex = 0; classIndex < numClasses; ++classIndex) {
            Class class = classes[classIndex];
            
            if (class_getClassMethod(class, @selector(isSubclassOfClass:)) == NULL) {
                continue;
            }
            
            if (![class isSubclassOfClass:[NSObject class]]) {
                continue;
            }
            
            if ([class isSubclassOfClass:[PDNetworkDomainController class]]) {
                continue;
            }
            
            for (int selectorIndex = 0; selectorIndex < numSelectors; ++selectorIndex) {
                if ([class instancesRespondToSelector:selectors[selectorIndex]]) {
                    [self injectIntoDelegateClass:class];
                    break;
                }
            }
        }
        
        free(classes);
    }
}
```

从这个实现也可以发现，像ASIHttpRequest这样的直接基于底层CFNetwork封装的库，以及iOS 7中新引入的NSURLSession都是无法抓到请求包的，这种情况下如果需要抓包就只能使用Charles这类工具了。

调试视图功能调用了一些runtime的私有API，部分代码如下：

```
+ (void)startMonitoringUIViewChanges;
{
    // Swizzle UIView add/remove methods to monitor changes in the view hierarchy
    // Only do it once to avoid swapping back if this method is called again
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        Method original, swizzle;
        Class viewClass = [UIView class];

        // Using sel_registerName() because compiler complains about the swizzled selectors not being found.
        original = class_getInstanceMethod(viewClass, @selector(addSubview:));
        swizzle = class_getInstanceMethod(viewClass, sel_registerName("pd_swizzled_addSubview:"));
        method_exchangeImplementations(original, swizzle);
        
        ···
       	        
        original = class_getInstanceMethod(viewClass, @selector(exchangeSubviewAtIndex:withSubviewAtIndex:));
        swizzle = class_getInstanceMethod(viewClass, sel_registerName("pd_swizzled_exchangeSubviewAtIndex:withSubviewAtIndex:"));
        method_exchangeImplementations(original, swizzle);
    });
}
```

这部分可以看到是通过Method Swizzling来动态交换了UIView的方法实现，以addSubview为例，替换成了pd_swizzled_addSubview，至于pd_swizzled_addSubview的实现请看下面

```
- (void)pd_swizzled_addSubview:(UIView *)subview;
{
    [[PDDOMDomainController defaultInstance] removeView:subview];
    [self pd_swizzled_addSubview:subview];
    [[PDDOMDomainController defaultInstance] addView:subview];
}
```

替换后的removeView和addView方法都是重新实现的，里面会将UIView的变动以DOM形式写到PDDOMDomain对象中输出。

除了以上两个主要功能，还有Core Data远程调试，日志远程输出等功能，其实现方式大同小异，就不一一赘述了。

总的来说，PonyDebugger的功能还是十分强大的，也能够有效地提高开发者的调试效率，有类似需求的很值得一试。

