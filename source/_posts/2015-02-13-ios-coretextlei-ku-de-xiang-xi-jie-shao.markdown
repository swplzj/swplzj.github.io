---
layout: post
title: "iOS CoreText类库的详细介绍"
date: 2014-04-22 21:50:08 +0800
comments: true
categories: 
---
iOS 编程中如何使用CoreText类库

![id2](http://img.blog.csdn.net/20131104171422968?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3dwbHpq/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

<!--more-->

从框架图中我们可以看出：
CTFrame 作为一个整体的画布(Canvas)，其中由行(CTLine)组成，而每行可以分为一个或多个小方块（CTRun）。

注意：你不需要自己创建CTRun，Core Text将根据NSAttributedString的属性来自动创建CTRun。每个CTRun对象对应不同的属性，正因此，你可以自由的控制字体、颜色、字间距等等信息。
通常处理步聚：

1. 使用core text就是先有一个要显示的string，然后定义这个string每个部分的样式－>attributedString －> 生成 CTFramesetter -> 得到CTFrame -> 绘制（CTFrameDraw）
其中可以更详细的设置换行方式，对齐方式，绘制区域的大小等。

2. 绘制只是显示，点击事件就需要一个判断了。

CTFrame 包含了多个CTLine,并且可以得到各个line的其实位置与大小。判断点击处在不在某个line上。
 CTLine 又可以判断这个点(相对于ctline的坐标)处的文字范围。然后遍历这个string的所有
 NSTextCheckingResult，根据result的rang判断点击处在不在这个rang上，从而得到点击的链接与位置。


#### 首先，创建工程，添加系统库CoreText

![id1](http://img.blog.csdn.net/20131104170022796?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3dwbHpq/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


```
//  
//  CoreTextView.m  
//  CoreTextDemo  
//  
//  Created by swplzj on 13-10-30.  
//  Copyright (c) 2013年 swplzj. All rights reserved.  
//  
  
#import "CoreTextView.h"  
#import <CoreText/CoreText.h>  
#import <math.h>  
  
@implementation CoreTextView  
  
static inline double radians (double degrees)  
{  
    return degrees * M_PI / 180;  
}  
  
void ctLineAndRun(CTFrameRef frame)  
{  
    int lineIndex = 1;  
    //获取frame中的所有行  
    CFArrayRef allLines = CTFrameGetLines(frame);  
    //获取frame中第一行的元素  
    CTLineRef firstLineRef = CFArrayGetValueAtIndex(allLines, lineIndex);  
    //获得frame中所有的行数  
    int linesCount = CFArrayGetCount(allLines);  
    NSLog(@"ctLine的总数量：%d", linesCount);  
    //获取frame中的所有CTRun  
    CFArrayRef ctRun = CTLineGetGlyphRuns(firstLineRef);  
//    CTRunRef firstRun = CFArrayGetValueAtIndex(ctRun, 0);  
//    int runsNum = CTLineGetGlyphCount(ctRun);  
    NSLog(@"ctRun的数量：%ld", CFArrayGetCount(ctRun));  
    //按长度截断字符串：省略号  
    NSAttributedString *attributedStr = [[NSAttributedString alloc] initWithString:@"\u2026"];  
    CTLineRef lineToken = CTLineCreateWithAttributedString((CFAttributedStringRef) attributedStr);  
    //截断的方式  
    CTLineTruncationType lineTruncationType = kCTLineTruncationEnd;  
    CTLineRef newLine = CTLineCreateTruncatedLine(firstLineRef, 260, lineTruncationType, lineToken);  
    CGContextRef context = UIGraphicsGetCurrentContext();  
    //重置画布  
    CGContextSetTextMatrix(context, CGAffineTransformIdentity);  
    //保存Context状态  
    CGContextSaveGState(context);  
    CGMutablePathRef path = CGPathCreateMutable();      
	CGRect tmpRect = CGRectMake(40, 40, 320-80, 40);
    CGRect bounds = CGRectInset(tmpRect, 0.0f, 0.0f);
    CGPathAddRect(path, NULL, bounds);  
    CGContextAddPath(context, path);  
    CGContextSetFillColorWithColor(context, [UIColor orangeColor].CGColor);  
    CGContextFillPath(context);  
    CGContextSetTextPosition(context, 40, 40);  
    CTLineDraw(newLine, context);  
    CGPathCloseSubpath(path);  
    CGPathRelease(path);  
    //获取一行文字中，指定charIndex字符(也就是StringRange的Location)相对ctline本身x原点的偏移量（譬如第一个字符很显然是0），返回值与secondaryOffset同为一个值,另外也可以用来求一行字符所占的像素长度  
    //第二个字符index位置是在ctframe整个中的位置，譬如第二行的第一个字符的index是加上第一行所有字符后的index  
    CGFloat secondaryOffSet;  
    CGFloat primaryOffSet = CTLineGetOffsetForStringIndex(firstLineRef, CTLineGetStringRange(firstLineRef).location, &secondaryOffSet);  
    NSLog(@"第%ld个字符与本身x原点的偏移量:%f",CTLineGetStringRange(firstLineRef).location + 1,primaryOffSet);  
    //获取相对于Flush的偏移量  
    //    CGFloat penOffset = CTLineGetPenOffsetForFlush(firstLine, NSTextAlignmentLeft, 240);  
    //    NSLog(@"相对于Flush的偏移量:%f",penOffset);  
    //CTLineGetStringIndexForPosition 这个方法主要是获得某个ctline中某个位置（position）的character在整个ctframe中的index.这个方法在当有段前缩进或者首行缩进的时候，并不准确，不会跟着缩进而进行偏移，所以段前缩进多少，就需要把position的x值减去多少（譬如段前缩进是20，则要减去20个单位），所以，这个方法最好的用处就是判断一行ctline最多容纳多少的字符，只需把这个point的x位置调很大（超过ctframe path的宽度）就可以了。  
    CGPoint origins[linesCount];  
    CTFrameGetLineOrigins(frame,CFRangeMake(0, 0), origins);  
    CGPoint indexPoint = origins[lineIndex];  
    indexPoint.x -= 20;//段前缩进20,需要减去20//indexPoint.x += 300;可以求  
    CFIndex index = CTLineGetStringIndexForPosition(firstLineRef, indexPoint);  
    NSLog(@"ctframe中%@位置（position）的字符索引:%ld",NSStringFromCGPoint(indexPoint),index);  
    //CTLine在整个文本中所处的range，location是该行前面n行的字符数和，换行符也包括  
    //    CFRange range = CTLineGetStringRange(firstLine);  
    //    NSLog(@"location:%ld=====length:%ld",range.location,range.length);  
      
    //获取一行中上行高(ascent)，下行高(descent)，行距(leading),整行高为(ascent+|descent|+leading) 返回值为整行字符串长度占有的像素宽度。  
    //    CGFloat asc,des,lead;  
    //    double lineWidth = CTLineGetTypographicBounds(firstLine, &asc, &des, &lead);  
    //    NSLog(@"ascent = %f,descent = %f,leading = %f,lineWidth = %f",asc,des,lead,lineWidth);  
      
    //获取一行文字最佳可视范围（会把所有文字都包含进去）  
    //    CGRect lineRect = CTLineGetImageBounds(firstLine, context);  
    //    NSLog(@"整行的范围:%@",NSStringFromCGRect(lineRect));  
CGContextRestoreGState(context);  
  
}  
  
- (id)initWithFrame:(CGRect)frame  
{  
    self = [super initWithFrame:frame];  
    if (self) {  
        // Initialization code  
    }  
    return self;  
}  
  
// Only override drawRect: if you perform custom drawing.  
// An empty implementation adversely affects performance during animation.  
- (void)drawRect:(CGRect)rect  
{  
    NSMutableAttributedString *attributedStr = [[NSMutableAttributedString alloc] initWithString:@"北京出现严重雾霾后，网友纷纷给PM2.5取中文名字。严肃点就叫“公雾源”，高端点就叫“京尘”，霸气点就叫“尘疾思汗”，乐观点就叫“尘世美”，娱乐点就叫“尘惯吸”。但直到那五个字映入眼帘，才明白了中文的强大和词汇的无穷魅力：喂人民服雾！"];  
    [self setAttributedStringParagraphStyle:attributedStr];  
//    [self setAttributesStringAttributes:attributedStr];  
  
    CTFramesetterRef frameSetter = CTFramesetterCreateWithAttributedString((CFAttributedStringRef) attributedStr);  
    CGMutablePathRef path = CGPathCreateMutable();  
	CGRect tempRect1 = CGRectMake(20, 100, CGRectGetWidth(self.bounds)-40, 400);
    CGRect bounds = CGRectInset(tempRect1, 10.0f, 10.0f);
    CGPathAddRect(path, NULL, bounds);  
    CTFrameRef frame = CTFramesetterCreateFrame(frameSetter, CFRangeMake(0, 0), path, NULL);  
      
    CGContextRef context = UIGraphicsGetCurrentContext();  
    CGContextTranslateCTM(context, 0, self.bounds.size.height);//转换CTM  
    CGContextScaleCTM(context, 1.0, -1.0);  
    CGContextSaveGState(context);//向图形状态栈中保存context状态  
      
    //ctLine的相关操作  
    ctLineAndRun(frame);  
      
    CGContextSetTextMatrix(context, CGAffineTransformIdentity);//重置画布  
    CGContextAddPath(context, path);  
    CGContextSetFillColorWithColor(context, [UIColor yellowColor].CGColor);  
    CGContextFillPath(context);  
      
    //CTLineGetImageBounds的应用，获取一行文字的范围， 就是指把这一行文字点有的像素矩阵作为一个image图片，来得到整个矩形区域  
    CFArrayRef Lines = CTFrameGetLines(frame);  
    int lineCount = CFArrayGetCount(Lines);  
      
    CGPoint origins[lineCount];  
    CTFrameGetLineOrigins(frame, CFRangeMake(0, 0), origins);  
    NSInteger lineIndex = 0;  
      
    for (id oneLine in (NSArray *)Lines) {  
        //相对于每一行基线原点的偏移量和宽高（例如：{1.2， -2.57227}, {208.025, 19.2523}，就是相对于本身的基线原点向右偏移1.2个单位，向下偏移2.57227个单位，后面是宽高）  
        CGRect lineBounds = CTLineGetImageBounds((CTLineRef)oneLine, context);  
          
        //每一行的起始点（相对于context）加上相对于本身基线原点的偏移量  
        lineBounds.origin.x += origins[lineIndex].x + 30;  
        lineBounds.origin.y += origins[lineIndex].y + 110;  
          
        lineIndex++;  
          
        //填充  
        CGContextSetStrokeColorWithColor(context, [[UIColor redColor] CGColor]);  
        CGContextSetLineWidth(context, 1.0);  
        //设置长方形4个顶点  
        CGPoint points[] = {CGPointMake(lineBounds.origin.x, lineBounds.origin.y), CGPointMake(lineBounds.origin.x + lineBounds.size.width, lineBounds.origin.y), CGPointMake(lineBounds.origin.x + lineBounds.size.width, lineBounds.origin.y + lineBounds.size.height), CGPointMake(lineBounds.origin.x, lineBounds.origin.y + lineBounds.size.height)};  
        CGContextAddLines(context, points, 4);  
        CGContextClosePath(context);  
        CGContextStrokePath(context);  
    }  
      
    //画  
    CTFrameDraw(frame, context);  
    CGPathRelease(path);  
    CFRelease(frame);  
    CFRelease(frameSetter);  
      
    //恢复状态栈中原有的状态  
    CGContextRestoreGState(context);  
}  
  
- (void)setAttributesStringAttributes:(NSMutableAttributedString *)attributedString  
{  
    //设置字体  
    CTFontRef font = CTFontCreateWithName(CFSTR("Georgia"), 20, NULL);  
    [attributedString addAttribute:(id)kCTFontAttributeName value:(id)font range:NSMakeRange(0, 4)];  
      
    //设置字间距  
    long kernNumber = 5;  
    CFNumberRef kernNum = CFNumberCreate(kCFAllocatorDefault, kCFNumberSInt8Type, &kernNumber);  
    [attributedString addAttribute:(id)kCTKernAttributeName value:(id)kernNum range:(NSRange){0, 4}];  
      
    //设置字体颜色  
    long strokeNumber = 2;  
    CFNumberRef strokeNum = CFNumberCreate(kCFAllocatorDefault, kCFNumberSInt8Type, &strokeNumber);  
    [attributedString addAttribute:(id)kCTStrokeWidthAttributeName value:(id)strokeNum range:(NSRange){0, 4}];  
    [attributedString addAttribute:(id)kCTStrokeColorAttributeName value:(id)[UIColor redColor].CGColor range:NSMakeRange(0, 4)];  
      
    //设置下划线  
    [attributedString addAttribute:(id)kCTUnderlineStyleAttributeName value:(id)[NSNumber numberWithInt:kCTUnderlineStyleSingle] range:NSMakeRange(0, 4)];  
    [attributedString addAttribute:(id)kCTUnderlineStyleAttributeName value:(id)[UIColor redColor] range:(NSRange){0, 4}];  
      
    //多属性设置  
    UIColor *color = [UIColor redColor];  
    NSMutableDictionary *attributes = [[NSMutableDictionary alloc] initWithObjectsAndKeys:(id)font, kCTFontAttributeName, kernNum, kCTKernAttributeName, color.CGColor, kCTForegroundColorAttributeName, nil nil];  
    [attributedString setAttributes:attributes range:NSMakeRange(0, 4)];  
}  
  
- (void)setAttributedStringParagraphStyle:(NSMutableAttributedString *)attributedString  
{  
    //对齐方式  
    CTTextAlignment textAlignment = kCTLeftTextAlignment;  
    CTParagraphStyleSetting alignmentStyleSetting;  
    alignmentStyleSetting.spec = kCTParagraphStyleSpecifierAlignment;  
    alignmentStyleSetting.valueSize = sizeof(textAlignment);  
    alignmentStyleSetting.value = &textAlignment;  
      
    //首行缩进  
    CGFloat firstLineIndentSize = 55.0f;  
    CTParagraphStyleSetting firstLineIndent;  
    firstLineIndent.spec = kCTParagraphStyleSpecifierFirstLineHeadIndent;  
    firstLineIndent.value = &firstLineIndentSize;  
    firstLineIndent.valueSize = sizeof(float);  
      
    //段前缩进（文字左侧距离context最左侧的距离）  
    CGFloat headIndentSize = 20.0f;  
    CTParagraphStyleSetting headIndent;  
    headIndent.spec = kCTParagraphStyleSpecifierHeadIndent;  
    headIndent.valueSize = sizeof(float);  
    headIndent.value = &headIndentSize;  
      
    //断尾缩进(文字右侧距离context最左侧的距离)  
    CGFloat tailIndentSize = 251.0f;    //文字最左侧到文字最右侧的距离  
    CTParagraphStyleSetting tailIndent;  
    tailIndent.spec = kCTParagraphStyleSpecifierTailIndent;  
    tailIndent.value = &tailIndentSize;  
    tailIndent.valueSize = sizeof(float);  
      
    //换行模式  
    CTLineBreakMode lineBreak = kCTLineBreakByCharWrapping;  
    CTParagraphStyleSetting lineBreakMode;  
    lineBreakMode.spec = kCTParagraphStyleSpecifierLineBreakMode;  
    lineBreakMode.value = &lineBreak;  
    lineBreakMode.valueSize = sizeof(CTLineBreakMode);  
      
    //最大行高  
    CGFloat maxHeightSize = 50.0f;  //最大行高不能超过50个像素，超过按照最大像素来  
    CTParagraphStyleSetting maxHeightSetting;  
    maxHeightSetting.spec = kCTParagraphStyleSpecifierMaximumLineHeight;  
    maxHeightSetting.value = &maxHeightSize;  
    maxHeightSetting.valueSize = sizeof(float);  
      
    //多行高  
    CGFloat multipleHeight = 1.2f;  //1.2倍原来的高度  
    CTParagraphStyleSetting multipleHeightSetting;  
    multipleHeightSetting.spec = kCTParagraphStyleSpecifierLineHeightMultiple;  
    multipleHeightSetting.value = &multipleHeight;  
    multipleHeightSetting.valueSize = sizeof(float);  
      
    //最大行距  
    CGFloat maxLineSpace = 5.0f;//最大行距不能超过5像素，超过了按最大行距画图，最小行距同理，行距调整只在中间值中进行  
    CTParagraphStyleSetting maxLineSpaceSetting;  
    maxLineSpaceSetting.spec = kCTParagraphStyleSpecifierMaximumLineSpacing;  
    maxLineSpaceSetting.valueSize = sizeof(float);  
    maxLineSpaceSetting.value = &maxLineSpace;  
      
    //行距  
    CGFloat lineSpace = 25.0f;  //行距25像素  
    CTParagraphStyleSetting lineSpaceSetting;  
    lineSpaceSetting.spec = kCTParagraphStyleSpecifierLineSpacing;  
    lineSpaceSetting.value = &lineSpace;  
    lineSpaceSetting.valueSize = sizeof(float);  
      
    //段前间隔  
    CGFloat paragraghSpace = 15.0f;  
    CTParagraphStyleSetting paragraghInterval;  
    paragraghInterval.spec = kCTParagraphStyleSpecifierParagraphSpacing;  
    paragraghInterval.valueSize = sizeof(float);  
    paragraghInterval.value = ¶graghSpace;  
      
    CTParagraphStyleSetting settingsArray[] = {alignmentStyleSetting, firstLineIndent, headIndent, tailIndent, lineBreakMode, maxLineSpaceSetting, multipleHeightSetting, lineSpaceSetting, maxLineSpaceSetting, paragraghInterval};  
      
    //采用setting里面前10个的段落设置，不够10个，就按照setting拥有的最大段落设置个数来算  
    CTParagraphStyleRef paragraphStyle = CTParagraphStyleCreate(settingsArray, 10);  
    NSDictionary *paragraphStyleDic = [[NSDictionary alloc] initWithObjectsAndKeys:(id)paragraphStyle, kCTParagraphStyleAttributeName, nil nil];  
    [attributedString setAttributes:paragraphStyleDic range:(NSRange){0, [attributedString length]}];  
}  
  
- (void)ctLineAndRun  
{  
      
}  
  
@end  
```

[关于CoreText的官方API](https://developer.apple.com/library/mac/#documentation/Carbon/Reference/CoreText_Framework_Ref/_index.html)
