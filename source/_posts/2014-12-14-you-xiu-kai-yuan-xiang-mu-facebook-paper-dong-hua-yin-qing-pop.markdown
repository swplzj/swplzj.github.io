---
layout: post
title: "优秀开源项目：FACEBOOK PAPER动画引擎 – POP"
date: 2014-11-14 19:02:27 +0800
comments: true
categories: 
---

![id1](http://cc.cocimg.com/cms/uploads/allimg/140429/4196_140429142234_1.gif)
  
Pop是一个适用于iOS和OS X平台的可扩展动画引擎，是一个成熟的经过多重测试的，可实现Facebook Paper中的所有动画和过渡效果。除了基本的静态动画，Pop还支持spring和decay动画，有助于打造一个逼真的，基于物理的交互。你可以通过Pop的API把Pop快速集成到现有的Objective-C代码库中，并在任何对象上实现动画的任何属性。

<!--more-->  

##### 开始，停止以及更新
开始动画–把它添加到你想要动画的对象上：

```
POPSpringAnimation *anim = [POPSpringAnimation animation]; 
… 
[layer pop_addAnimation:anim forKey:@"myKey"]; 
```

停止动画–把它从key在刚开始时指定的对象引用中移除。

```
[layer pop_removeAnimationForKey:@"myKey"]; 
key可以用来查询现有的动画。更新运行中动画的toValue，为改变进程提供了很好的方式：
anim = [layer pop_animationForKey:@"myKey"]; 
if (anim) { 
  /* update to value to new destination */ 
  anim.toValue = @(42.0); 
} else { 
  /* create and start a new animation */ 
  …. 
} 
```

虽然上述代码中使用了layer，但是Pop接口作为一个NSObject上附加类别执行。任何NSObject或者子类可以被动画。
##### 类型
Pop有四个具体的动画类型：spring, decay, basic和custom
Spring动画可以用来给对象一个令人愉悦的弹跳效果。在这个例子中，我们用spring动画来动画一个层次的弹跳效果，从现有的值设定为(0, 0, 400, 400)。

```
POPSpringAnimation *anim = [POPSpringAnimation animationWithPropertyNamed:kPOPLayerBounds]; 
anim.toValue = [NSValue valueWithCGRec 
t:CGRectMake(0, 0, 400, 400)]; 
[layer pop_addAnimation:anim forKey:@"size"]; 
```

Decay动画能用于逐渐减慢一个对象直至停止。在这个例子中，我们将layer的速率减小为每秒钟1000pts。

```
POPDecayAnimation *anim = [POPDecayAnimation animationWithPropertyNamed:kPOPLayerPositionX]; 
anim.velocity = @(1000.); 
[layer pop_addAnimation:anim forKey:@"slide"]; 
```

基本动画能用于在指定的时间段插入值。使用一个淡入淡出动画在默认时间段将视图的透明度从0.0设置为1.0。

```
POPBasicAnimation *anim = [POPBasicAnimation animationWithPropertyNamed:kPOPViewAlpha]; 
anim.timingFunction = [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseInEaseOut]; 
anim.fromValue = @(0.0); 
anim.toValue = @(1.0); 
[view pop_addAnimation:anim forKey:@"slide"]; 
```

通过处理CADisplayLink和相关的time-step管理，POPCustomAnimation使创建自定义动画和过渡变得更加容易。更多详情请查看header。
##### 属性
属性由POPAnimatableProperty类指定动画。在这个例子中，我们创建一个spring动画，明确地设置与-[CALayer bounds]一致的动画属性：

```
POPSpringAnimation *anim = [POPSpringAnimation animation]; 
anim.property = [POPAnimatableProperty propertyWithName:kPOPLayerBounds]; 
```

这个框架提供了很多常见的层以及额外的视图动画属性。你可以通过创建类的新实例来动画一个自定义属性。在这个例子中，我声明了一个自定义音量属性：

```
prop = [POPAnimatableProperty propertyWithName:@"com.foo.radio.vo 
lume" initializer:^(POPMutableAnimatableProperty *prop) { 
  // read value 
  prop.readBlock = ^(id obj, CGFloat values[]) { 
    values[0] = [obj volume]; 
  }; 
  // write value 
  prop.writeBlock = ^(id obj, const CGFloat values[]) { 
    [obj setVolume:values[0]]; 
  }; 
  // dynamics threshold 
  prop.threshold = 0.01; 
}]; 
anim.property = prop; 
```

关于动画属性的完整列表以及声明自定义属性的更多信息，请看POPAnimatableProperty.h。
##### 调试
这里有一些关于调试的技巧。Pop遵循模拟器的Toggle Slow Animations设置。试着用它放慢动画，将能更容易查看交互。
考虑给你的动画命名。当你通过日志查看动画或者调试动画时，这将使你更容易识别它们。

```
anim.name = @“springOpen”; 
```

每个动画都有一个关联的tracer，它以一种快速高效的方式允许你记录所有的动画相关事件，还允许你在动画完成后对其进行查询和分析。下边的例子展示启动和配置tracer，以记录所有动画事件。

```
POPAnimationTracer *tracer = anim.tracer; 
tracer.shouldLogAndResetOnCompletion = YES; 
[tracer start]; 
```

更多详情，请查看POPAnimationTracer.h。
假设已经安装了CocoaPods，这将在单元测试目标中包含必要的OCMock依赖。
##### 相关资源
[Core Animation Programming Guide](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/CoreAnimation_guide/Introduction/Introduction.html)
[Tapity Tutorial – Getting Started with Pop](http://tapity.com/tutorial-getting-started-with-pop/)
[Tweaks – Easily adjust parameters for iOS apps in development](https://github.com/facebook/tweaks)
[Rebound – Springs Animations for Android](http://facebook.github.io/rebound/)