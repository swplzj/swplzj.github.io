---
layout: post
title: "在iOS上绘制自然的签名"
date: 2014-02-22 21:02:25 +0800
comments: true
categories: 
---

这里有一篇很棒的文章写如何在Android上获取流畅的签名：[Smoother Signatures](http://corner.squareup.com/2012/07/smoother-signatures.html)，但是我没有找到一篇是写在iOS上如何实现。那么，究竟怎么做才能在iOS设备上获取用户的签名呢？

<!--more-->

虽然我没有找到任何关于获取签名的文章，但是在App store上已经有了实现得很好的app。  [Paper by 53](http://fiftythree.com/paper) 是一个画画的iPad应用程序，它拥有漂亮并且灵敏的画笔，这也是我所要追求的用户体验。

代码可以从这里得到： [SignatureDemo](https://www.github.com/jharwig/SignatureDemo)


##### 连点成线

![id1](http://img.my.csdn.net/uploads/201304/25/1366858755_4942.png)


最简单得办法是，依次获取触摸点并且用直线把它们连起来。
在UIView子类的初始化方法中创建path和用于捕获触摸事件的gesture recongnizer .

```
// Create a path to connect lines
path = [UIBezierPath bezierPath];

// Capture touches
UIPanGestureRecognizer *pan = [[UIPanGestureRecognizer alloc] initWithTarget:self action:@selector(pan:)];
pan.maximumNumberOfTouches = pan.minimumNumberOfTouches = 1;
[self addGestureRecognizer:pan];
```

将捕获到的pan事件location数据依次加入到贝塞尔path中，连点成线。

```
- (void)pan:(UIPanGestureRecognizer *)pan {
    CGPoint currentPoint = [pan locationInView:self];

    if (pan.state == UIGestureRecognizerStateBegan) {
        [path moveToPoint:currentPoint];
    } else if (pan.state == UIGestureRecognizerStateChanged)
        [path addLineToPoint:currentPoint];

    [self setNeedsDisplay];
}
```


##### 画出轨迹


```
- (void)drawRect:(CGRect)rect
{
    [[UIColor blackColor] setStroke];
    [path stroke];
}
```

![id2](http://img.my.csdn.net/uploads/201304/25/1366859967_1064.png "image")

用这种方法画个字母J就暴露出一些问题了。
当签名速度较慢时，iOS可以捕获到足够的touch位置信息，让连接起来的直线看起来不那么明显。但是当手指移动速度很快时就有麻烦了。

在2012苹果开发者大会中介绍的 Building Advanced Gesture Recognizers 提到，可以用数学来解决这个问题。

##### 二次贝塞尔曲线

我们需要用二次贝塞尔曲线去连接那些触摸点，而并非用直线，可以参考上面给出的苹果开发者大会视频（大约在42：15处）。连接二次贝塞尔曲线时，应把触摸点当作控制点，而取中点为对应的起点和终点。

添加二次贝塞尔曲线到之前的代码中，需要用到上一次的touch信息，所以我们增加一个实例变量来存储它。

```
CGPoint previousPoint;
```

写一个计算2点中点的方法

```
static CGPoint midpoint(CGPoint p0, CGPoint p1) {
    return (CGPoint) {
        (p0.x + p1.x) / 2.0,
        (p0.y + p1.y) / 2.0
    };
}
```


更新手势处理，用二次贝塞尔曲线替换掉之前的直接连接处理

```
- (void)pan:(UIPanGestureRecognizer *)pan {
    CGPoint currentPoint = [pan locationInView:self];
    CGPoint midPoint = midpoint(previousPoint, currentPoint);

    if (pan.state == UIGestureRecognizerStateBegan) {
        [path moveToPoint:currentPoint];
    } else if (pan.state == UIGestureRecognizerStateChanged) {
        [path addQuadCurveToPoint:midPoint controlPoint:previousPoint];
    }

    previousPoint = currentPoint;

    [self setNeedsDisplay];
}
```

![id3](http://img.my.csdn.net/uploads/201304/25/1366862268_4928.png)

没有写很多代码，我们就看到了很大的改观。棱角不见了，但是作为签名似乎有点乏味。每一处曲线都是等宽的，和用一只真正的钢笔签出来的签名效果相违背。


##### 可变的笔刷宽度

笔刷的宽度应该基于签名的速度而变化，这样的签名看起来才自然。UIPanGestureRecognizer 有一个 velocityInView 方法可以返回当前触摸点的速度。

为了画出变化的宽度，我改用OpenGL ES 曲面细分将笔刷转换成三角序列（OpenGL支持画线，但是iOS不支持绘制平滑的可变宽度的线条）。二次贝塞尔曲线点需要重新计算，但是这超出了这篇文章的讨论范畴，具体可以查看代码：github 

用相邻的2个二次贝塞尔曲线点来说明一下，可以计算得到此两点差值表示的向量的垂直向量，并且设定其长度为当前厚度值的1/2（译者注：下图大括号部分包含2份1/2厚度值长度，故恰好为当前厚度），采用GL_TRIANGLE_STRIP的方式绘制三角序列，因此需要2个二次贝塞尔曲线点来确定一个含有2个三角形的矩形段。


![id4](http://img.my.csdn.net/uploads/201304/25/1366864689_3366.png)

这个例子，就是用二次贝塞尔曲线和速度控制笔刷厚度的方法画出来的签名，自然多了吧。

![id5](http://img.my.csdn.net/uploads/201304/25/1366864895_3168.png)