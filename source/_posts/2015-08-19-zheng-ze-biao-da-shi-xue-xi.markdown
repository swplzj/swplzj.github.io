---
layout: post
title: "正则表达式学习"
date: 2015-08-19 11:45:54 +0800
comments: true
categories: 
---

项目中经常使用到一些校验，比如有效手机号，身份证号码，邮箱，银行卡号码...假如说都是简单的使用位数来做校验，容易造成特殊字符输入乱码，还有可能在数据库中写入脏数据，所以使用正则表达式来进行校验匹配是很有必要的，当然，我们学习它就要知道它有哪些作用，正则表达式的用途基本有以下三种：

* 校验数据
* 查询数据
* 替换数据

学习正则表达式的最好方法是从例子开始，理解例子之后再自己对例子进行修改。下面开始学习之旅：

<!--more-->

### 语法
`.` 是元字符，匹配除了换行符以外的任意字符。

`*` 是元字符，不过它代表的不是字符，也不是位置，而是数量——它指定 `*` 前边的内容可以连续重复使用任意次以使整个表达式得到匹配。

`.*` 连在一起就意味着任意数量的不包含换行的字符。

`+`	重复一次或更多次

`?`	重复零次或一次

`{n}` 重复n次

`{n,}`	重复n次或更多次

`{n,m}`	重复n到m次

`[]` 指定一个字符范围，比如，**[aeiou]**就匹配任何一个英文元音字母，`[0-9]`代表的含意与\d就是完全一致的。
`()` 小括号来指定子表达式(也叫做分组),比如：**(\d{1,3}\.){3}\d{1,3}** 是一个简单的IP地址匹配表达式。

`\b` 是正则表达式规定的一个特殊代码（也叫它元字符，metacharacter），代表着单词的开头或结尾，也就是单词的分界处。虽然通常英文的单词是由空格，标点符号或者换行来分隔的，但是`\b`并不匹配这些单词分隔字符中的任何一个，它只匹配一个位置。比如:在一段文本中查询`Leo`，就可以这样写：**\bLeo\b** 。

`\B`	匹配不是单词开头或结束的位置

`\d` 匹配一位数字，比如匹配大陆手机号码： **1\d{10}** ，{10} 意思是重复匹配10次。

`\D`	匹配任意非数字的字符

`\s` 匹配任意的空白符，包括空格，制表符(Tab)，换行符，中文全角空格等。

`\S`	匹配任意不是空白符的字符

`\w` 匹配字母或数字或下划线或汉字等。

`\W`	匹配任意不是字母，数字，下划线，汉字的字符

`[^x]`	匹配除了x以外的任意字符

`[^aeiou]`	匹配除了aeiou这几个字母以外的任意字符

`^`	匹配字符串的开始，或者是行的开始处

`$`	匹配字符串的结束，或者是行的结束处

`|` 分枝条件指的是有几种规则，如果满足其中任意一种规则都应该当成匹配。

`\` 为转义字符，比如匹配网址：使用 `swplzj\.github\.io` 来匹配 `swplzj.github.io` 。

### 后向引用

#### 捕获
(exp):	匹配exp,并捕获文本到自动命名的组里

(?<name>exp):	匹配exp,并捕获文本到名称为name的组里，也可以写成(?'name'exp)

(?:exp):	匹配exp,不捕获匹配的文本，也不给此分组分配组号

#### 零宽断言

(?=exp):	匹配exp前面的位置

(?<=exp):	匹配exp后面的位置

(?!exp):	匹配后面跟的不是exp的位置

#### 注释

(?#comment):	这种类型的分组不对正则表达式的处理产生任何影响，用于提供注释让人阅读

### 实例

从下面文本中查询出以`[]`包含的内容，包括`[]`

	The Tasmanian tiger, also known as the Thylacine, has been announced extinct many years ago. Now many geneticists assert that they are capable of recreating a [sid=13_1] from DNA extracted from its bone specimen. \n\nTasmanian tigers belong to marsupials that have a [sid=13_2] in common. They looked like big dogs with over 15 dark stripes on the body. When chasing their preys, these animals primarily depended on [sid=13_3] to prevail. Their stomach could inflate so they could eat large amount of food each time to [sid=13_4] a period lack of food.  
	
我们发现要查询的内容是以`[`开头，以 `]`	结束的，所以首先可以肯定的是  `\[\]`，注意需要转义字符`\`，然后`[]`之前包含的可能是 数字、空格、大小写字母、符号，但是不能包含`]`,所以我们可以写出 `\[[^\]]+\]`

{% img center /2015_08_14_regular_express_id1.png /%}

再增加点难度，要检索出`[]`里面的数据，我们可以先观察规律，因为正则表达式 - Regular Express 本身就是有规律的表达式，所以现发现规律，再写出所对应的正则。

**解答**

* 以 `[` 开头，作为规律，可以写出 `(?<=\[)([^\]])+`

{% img center /2015_08_14_regular_express_id2.png /%}


* 以`]` 结束，可以写出`[^\[]+(?=\])`

{% img center /2015_08_14_regular_express_id3.png /%}

### iOS中应用

我们在开发iOS项目过程中，使用到正则表达式的主要场景有两种：

**第一** 在应用中用户输入数据时，做正则匹配校验，这类比较常见，这里就不做详述，但是给出一个比较好的建议，就是把需要校验的项，单独创建一个 XML 文件，然后再自定义标签，属性，来进行统一存放项目中所有校验项，如下：


```
<?xml version="1.0" encoding="utf-8"?>
<rules>
    <rule type="mobile" pattern="^1\d{10}$" tip="请输入正确的手机号" />
</rules>
```

上面是简单校验手机号码位数的正则表达式，这样做的好处：方便维护管理；代码更为规范。

**第二** 在使用 `UIWebView` 来加载文章类的页面时，使用正则表达式可以很好的提升用户体验。那使用正则我们可以做到那些呢？

* 使用正则表达式来加载本地 `CSS` 样式表来自定义 `HTML` 中的样式；
	
	* 替换 `CSS` 样式表，这个可以实现 `HTML` 字体大小的动态调整；
	* 可以实现类似白天、黑夜主题色的更换；
	* 可以对文本进行标注、突出等处理；

* 使用正则表达式获取 `HTML` 中的图片、视频，以便更友好的操作图片、视频资源；
	* 可以更改图片的大小展示；
	* 通过正则匹配获取到图片的属性，比如id，然后进行本地操作，如，对图片添加点击事件，点击之后，进入本地图片浏览器，在图片浏览器中，就可以对图片进行放大、缩小、查看图片备注等操作了。
* 使用正则表达式来为 `HTML` 添加元素；
	* 可以为添加文章标题、发布时间等操作

使用场景已经描述完了，但是怎么样实现呢，下面直接上代码：

**加载本地 CSS **

```
NSRegularExpression *regularexpression = [[NSRegularExpression alloc]
                                              initWithPattern:@"(?i)<\\s*link\\s*href=\"[^\"]+\\.css\""
                                              options:NSRegularExpressionCaseInsensitive
                                              error:nil];
    NSArray *matches = [regularexpression matchesInString:htmlContent options:0 range:NSMakeRange(0, [htmlContent length])];
    NSString *content = htmlContent;
    NSString *replaceStr = [NSString stringWithFormat:@"<link href=\"%@\"",cssFilePath];
    for (NSTextCheckingResult *match in matches){
        NSRange matchRange = [match range];
        content=[regularexpression stringByReplacingMatchesInString:content options:0 range:matchRange withTemplate:replaceStr];
    }
```

**获取图片资源**

```
 NSMutableArray *imageIds = [NSMutableArray array];
    NSString *str = @"(?i)<div\\s*class\\s*=\\s*\"[^\"]*img\">\\s*<img\\s*[^>]*id=\\s*\"[^>]*>";
 NSRegularExpression *regularexpression = [[NSRegularExpression alloc]
                                              initWithPattern:str
                                              options:NSRegularExpressionCaseInsensitive
                                              error:nil];
 NSArray *matches = [regularexpression matchesInString:htmlContent
                                                  options:0
                                                    range:NSMakeRange(0, [htmlContent length])];

```

**对图片进行操作**

```
 NSString *str = @"(?i)<div\\s*class=\"[^\"]*img\">\\s*<img\\s*[^>]*id=\\s*\"";

 str = [str stringByAppendingString:imageInfo.image_id];
 str = [str stringByAppendingString:@"\"\\s*src=\\s*\"[^\"]*\"[^>]*>"];

 NSString *replacejs = [NSString stringWithFormat:@"<div class=\"img\" id=\"%@\" onclick=\"location.href='picid'+this.id\" style=\"background-color:#999999;width:%fpx;height:%fpx;\" />",imageInfo.image_id,width,height];

 replacejs = [replacejs stringByAppendingString:[NSString stringWithFormat:@"<img src=\"%@\"  width=\"%fpx\" height=\"%fpx\"/>",imagePath,width,0.0]];
 
 replacejs = [replacejs stringByAppendingString:[NSString stringWithFormat:@"<img src=\"%@\" width=\"180px\" height=\"36px\" style=\"margin-left:%fpx;margin-top:%fpx\"/>",logoImagePath,(width / 2 - 90),(height/2 - 34)]];

 NSRegularExpression *regularexpression = [[NSRegularExpression alloc]
                                          initWithPattern:str
                                          options:NSRegularExpressionCaseInsensitive
                                          error:nil];
 NSArray *matches = [regularexpression matchesInString:content options:0 range:NSMakeRange(0,  [content length])];


 for (NSTextCheckingResult *match in matches){
    NSRange matchRange = [match range];
    content=[regularexpression stringByReplacingMatchesInString:content options:0 range:matchRange withTemplate:replacejs];
 }
            
```

**放大缩小页面中的文本**

```
 NSString *jsString = [[NSString alloc] initWithFormat:@"document.getElementsByTagName('body')[0].style.webkitTextSizeAdjust= '%@'",textFontScale];
    
[_webView stringByEvaluatingJavaScriptFromString:jsString];
```

**扩展 - 在本地HTML中加入JS**

```
    NSString *js = @"<script language=\"javascript\" type=\"text/javascript\">";
 //替换css文件
    js = [js stringByAppendingString:@"function replacejscssfile(oldfilename, newfilename, filetype){ var targetelement=(filetype==\"js\")? \"script\" : (filetype==\"css\")? \"link\" : \"none\"; var targetattr=(filetype==\"js\")? \"src\" : (filetype==\"css\")? \"href\" : \"none\"; var allsuspects=document.getElementsByTagName(targetelement); for (var i=allsuspects.length; i>=0; i--){ if (allsuspects[i] && allsuspects[i].getAttribute(targetattr)!=null && allsuspects[i].getAttribute(targetattr).indexOf(oldfilename)!=-1){ var newelement=createjscssfile(newfilename, filetype); allsuspects[i].parentNode.replaceChild(newelement, allsuspects[i]);} } return \"sdasds\";}"];
    
    js = [js stringByAppendingString:@"</script></head>"];
    
   NSString *content = [htmlContent stringByReplacingOccurrencesOfString:@"</head>" withString:js];
```

### 常用正则表达式


网址（URL）：	[a-zA-z]+://[^\s]*

IP地址(IP Address)：	((2[0-4]\d|25[0-5]|[01]?\d\d?)\.){3}(2[0-4]\d|25[0-5]|[01]?\d\d?)

电子邮件(Email)：	\w+([-+.]\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*

QQ号码：	[1-9]\d{4,}

HTML标记(包含内容或自闭合)：	<(.*)(.*)>.*<\/\1>|<(.*) \/>

密码(由数字/大写字母/小写字母/标点符号组成，四种都必有，8位以上)：	(?=^.{8,}$)(?=.*\d)(?=.*\W+)(?=.*[A-Z])(?=.*[a-z])(?!.*\n).*$

日期(年-月-日)：	(\d{4}|\d{2})-((1[0-2])|(0?[1-9]))-(([12][0-9])|(3[01])|(0?[1-9]))

日期(月/日/年)：	((1[0-2])|(0?[1-9]))/(([12][0-9])|(3[01])|(0?[1-9]))/(\d{4}|\d{2})

时间(小时:分钟, 24小时制)：	((1|0?)[0-9]|2[0-3]):([0-5][0-9])

汉字(字符)：	[\u4e00-\u9fa5]

中文及全角标点符号(字符)：	[\u3000-\u301e\ufe10-\ufe19\ufe30-\ufe44\ufe50-\ufe6b\uff01-\uffee]

中国大陆固定电话号码：	(\d{4}-|\d{3}-)?(\d{8}|\d{7})

中国大陆手机号码：	1\d{10}

中国大陆邮政编码：	[1-9]\d{5}

中国大陆身份证号(15位或18位)：	\d{15}(\d\d[0-9xX])?

非负整数(正整数或零)：	\d+

正整数：	[0-9]*[1-9][0-9]*

负整数：	-[0-9]*[1-9][0-9]*

整数：	-?\d+

小数：	(-?\d+)(\.\d+)?

不包含abc的单词：	\b((?!abc)\w)+\b

### 后记

整理这篇博文的过程中，当然涉及到的知识不仅仅是正则表达式，还有后面通过 webview 和 JavaScript 进行交互，这是另一方面的知识了，就不在这里介绍了。马上七夕情人节了，大家快乐。就写到这里啦，还没有准备礼物，赶紧准备，不然...

参考博文：<a href="http://deerchao.net/tutorials/regex/regex.htm">正则表达式30分钟入门教程</a>