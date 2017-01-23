---
layout: post
title: "CSS 学习笔记"
date: 2016-03-11 11:29:14 +0800
comments: true
categories: 
---
### CSS 简介

CSS 指层叠样式表 (Cascading Style Sheets)
<!--more-->
#### 多重样式将层叠为一个
样式表允许以多种方式规定样式信息。样式可以规定在单个的 HTML 元素中，在 HTML 页的头元素中，或在一个外部的 CSS 文件中。甚至可以在同一个 HTML 文档内部引用多个外部样式表。
#### 层叠次序
当同一个 HTML 元素被不止一个样式定义时，会使用哪个样式呢？
一般而言，所有的样式会根据下面的规则层叠于一个新的虚拟样式表中，其中数字 4 拥有最高的优先权。

1. 浏览器缺省设置
2. 外部样式表
3. 内部样式表（位于 <head> 标签内部）
4. 内联样式（在 HTML 元素内部）

因此，内联样式（在 HTML 元素内部）拥有最高的优先权，这意味着它将优先于以下的样式声明：`<head>` 标签中的样式声明，外部样式表中的样式声明，或者浏览器中的样式声明（缺省值）。

### 基础语法

CSS 规则由两个主要的部分构成：选择器，以及一条或多条声明。
	
	selector {declaration1; declaration2; ... declarationN }

选择器通常是您需要改变样式的 HTML 元素。

每条声明由一个属性和一个值组成。

属性（property）是您希望设置的样式属性（style attribute）。每个属性有一个值。属性和值被冒号分开。

	selector {property: value}

#### 值的不同写法和单位
除了英文单词 red，我们还可以使用十六进制的颜色值 #ff0000：

	p { color: #ff0000; }
	
为了节约字节，我们可以使用 CSS 的缩写形式：
	
	p { color: #f00; }
	
我们还可以通过两种方法使用 RGB 值：

	p { color: rgb(255,0,0); }
	p { color: rgb(100%,0%,0%); }
`注意:`当使用 RGB 百分比时，即使当值为 0 时也要写百分比符号。

#### 记得写引号
`提示：`如果值为若干单词，则要给值加引号：

	p {font-family: "sans serif";}	
	
#### 多重声明：

`提示：`如果要定义不止一个声明，则需要用分号将每个声明分开。

多重声明时，应该在每行只描述一个属性，这样可以增强样式定义的可读性，就像这样：

```
p {
  text-align: center;
  color: black;
  font-family: arial;
}	
```
#### 空格和大小写
大多数样式表包含不止一条规则，而大多数规则包含不止一个声明。多重声明和空格的使用使得样式表更容易被编辑：

```
body {
  color: #000;
  background: #fff;
  margin: 0;
  padding: 0;
  font-family: Georgia, Palatino, serif;
  }
```

### 高级语法

#### 选择器的分组
你可以对选择器进行分组，这样，被分组的选择器就可以分享相同的声明。用逗号将需要分组的选择器分开。

```
h1,h2,h3,h4,h5,h6 {
  color: green;
  }
```
#### 继承及其问题
根据 CSS，子元素从父元素继承属性。

### 派生选择器

通过依据元素在其位置的上下文关系来定义样式，你可以使标记更加简洁。

```
li strong {
    font-style: italic;
    font-weight: normal;
  }
```
只有 li 元素中的 strong 元素的样式为斜体字，无需为 strong 元素定义特别的 class 或 id，代码更加简洁。

### id 选择器
id 选择器可以为标有特定 id 的 HTML 元素指定特定的样式。

id 选择器以 "#" 来定义。

`注意：`id 属性只能在每个 HTML 文档中出现一次.

在现代布局中，id 选择器常常用于建立派生选择器。

```
#sidebar p {
	font-style: italic;
	text-align: right;
	margin-top: 0.5em;
	}
```
#### 一个选择器，多种用法
即使被标注为 sidebar 的元素只能在文档中出现一次，这个 id 选择器作为派生选择器也可以被使用很多次：


### 类选择器

`注意：`类名的第一个字符不能使用数字！

在 CSS 中，类选择器以一个点号显示：

	.center {text-align: center}

和 id 一样，class 也可被用作派生选择器：

```
.fancy td {
	color: #f60;
	background: #666;
	}
```

元素也可以基于它们的类而被选择：

```
td.fancy {
	color: #f60;
	background: #666;
	}
```	

### 属性选择器
对带有指定属性的 HTML 元素设置样式。

可以为拥有指定属性的 HTML 元素设置样式，而不仅限于 class 和 id 属性。


下面的例子为带有 title 属性的所有元素设置样式：

```
[title]
{
color:red;
}
```
#### 子选择器
选择器子选择器，即大于符号(>),用于选择指定标签元素的第一代子元素。如右侧代码编辑器中的代码：

	.food>li{border:1px solid red;}

#### 包含(后代)选择器

即加入空格,用于选择指定标签元素下的后辈元素。如右侧代码编辑器中的代码：

	.first  span{color:red;}
	
#### 自选择器和包含选择器的区别

请注意这个选择器与子选择器的区别，子选择器（child selector）仅是指它的直接后代，或者你可以理解为作用于子元素的第一代后代。而后代选择器是作用于所有子后代元素。后代选择器通过空格来进行选择，而子选择器是通过“>”进行选择。	
`总结：`>作用于元素的第一代后代，空格作用于元素的所有后代。

#### 通用选择器

通用选择器是功能最强大的选择器，它使用一个（*）号指定，它的作用是匹配html中所有标签元素，如下使用下面代码使用html中任意标签元素字体颜色全部设置为红色：

	* {color:red;}
	
#### 伪类选择符
伪类选择符，它允许给html不存在的标签（标签的某种状态）设置样式，比如说我们给html中一个标签元素的鼠标滑过的状态来设置字体颜色：

	a:hover{color:red;}	
	
#### 分组选择符
当你想为html中多个标签元素设置同一个样式时，可以使用分组选择符（，），如下代码为右侧代码编辑器中的h1、span标签同时设置字体颜色为红色：

	h1,span{color:red;}
	
#### 权值规则

标签的权值为1，类选择符的权值为10，ID选择符的权值最高为100。例如下面的代码：

```
p{color:red;} /*权值为1*/
p span{color:green;} /*权值为1+1=2*/
.warning{color:white;} /*权值为10*/
p span.warning{color:purple;} /*权值为1+1+10=12*/
#footer .note p{color:yellow;} /*权值为100+10+1=111*/	
```
	
#### 属性和值选择器 - 多个值
下面的例子为包含指定值的 title 属性的所有元素设置样式。适用于由空格分隔的属性值：

	[title~=hello] { color:red; }
	
下面的例子为带有包含指定值的 lang 属性的所有元素设置样式。适用于由连字符分隔的属性值：

	[lang|=en] { color:red; }
	
#### 设置表单的样式

属性选择器在为不带有 class 或 id 的表单设置样式时特别有用：

```
input[type="text"]
{
  width:150px;
  display:block;
  margin-bottom:10px;
  background-color:yellow;
  font-family: Verdana, Arial;
}

input[type="button"]
{
  width:120px;
  margin-left:35px;
  display:block;
  font-family: Verdana, Arial;
}
```

为带有 target 属性的 `<a>` 元素设置样式：

```
a[target]
{ 
background-color:yellow;
}
```

为 target="_blank" 的 `<a>` 元素设置样式：

```
a[target=_blank]
{ 
background-color:yellow;
}
```

选择 titile 属性包含单词 "flower" 的元素，并设置其样式：

```
[title~=flower]
{ 
background-color:yellow;
}
```

[attribute|=value]	用于选取带有以指定值开头的属性值的元素，该值必须是整个单词。
选择 lang 属性值以 "en" 开头的元素，并设置其样式：

```
[lang|=en]
{ 
background-color:yellow;
}
```

[attribute^=value] 选择器匹配属性值以指定值开头的每个元素。

设置 class 属性值以 "test" 开头的所有 div 元素的背景色：

```			
div[class^="test"]
{
background:#ffff00;
}
```

设置 class 属性值以 "test" 结尾的所有 div 元素的背景色：

```
div[class$="test"]
{
background:#ffff00;
}
```		
设置 class 属性值包含 "test" 的所有 div 元素的背景色：

```
div[class*="test"]
{
background:#ffff00;
}
```

### 如何插入样式表
####  外部样式表
当样式需要应用于很多页面时，外部样式表将是理想的选择。在使用外部样式表的情况下，你可以通过改变一个文件来改变整个站点的外观。每个页面使用 `<link>` 标签链接到样式表。`<link>` 标签在（文档的）头部：

```
<head>
<link rel="stylesheet" type="text/css" href="mystyle.css" />
</head>
```

#### 内部样式表
当单个文档需要特殊的样式时，就应该使用内部样式表。你可以使用 `<style>` 标签在文档头部定义内部样式表，就像这样:

```
<head>
<style type="text/css">
  hr {color: sienna;}
  p {margin-left: 20px;}
  body {background-image: url("images/back40.gif");}
</style>
</head>
```

#### 内联样式
由于要将表现和内容混杂在一起，内联样式会损失掉样式表的许多优势。请慎用这种方法，例如当样式仅需要在一个元素上应用一次时。

要使用内联样式，你需要在相关的标签内使用样式（style）属性。Style 属性可以包含任何 CSS 属性。本例展示如何改变段落的颜色和左外边距：

	<p style="color: sienna; margin-left: 20px">
	This is a paragraph
	</p>

### 背景

CSS 允许应用纯色作为背景，也允许使用背景图像创建相当复杂的效果。
CSS 在这方面的能力远远在 HTML 之上。


### 格式化排版

#### 段落排版--缩进

	p{text-indent:2em;}

注意：2em的意思就是文字的2倍大小。

#### 段落排版--行间距（行高）
	
	p{line-height:1.5em;}

#### 段落排版--中文字间距、字母间距
	
	h1{
	    letter-spacing:50px;
	}
	h1{
	    word-spacing:50px;
	}
	
#### 段落排版--对齐	
	
	h1{
	    text-align:center;
	}
	
### 盒模型
	
代码就是将内联元素a转换为块状元素，从而使a元素具有块状元素特点。

	a{display:block;}	

#### 块级元素特点：

1. 每个块级元素都从新的一行开始，并且其后的元素也另起一行。（真霸道，一个块级元素独占一行）
2. 元素的高度、宽度、行高以及顶和底边距都可设置。
3. 元素宽度在不设置的情况下，是它本身父容器的100%（和父元素的宽度一致），除非设定一个宽度。	

#### 内联元素特点：

1. 和其他元素都在一行上；
2. 元素的高度、宽度及顶部和底部边距不可设置；
3. 元素的宽度就是它包含的文字或图片的宽度，不可改变。

### 盒模型

#### 边框

盒子模型的边框就是围绕着内容及补白的线，这条线你可以设置它的粗细、样式和颜色(边框三个属性)。

如下面代码为 div 来设置边框粗细为 2px、样式为实心的、颜色为红色的边框：

	div{
	    border:2px  solid  red;
	}
	
上面是 border 代码的缩写形式，可以分开写：

	div{
	    border-width:2px;
	    border-style:solid;
	    border-color:red;
	}

border-style（边框样式）常见样式有：

dashed（虚线）| dotted（点线）| solid（实线）。

##### 标签单独设置下边框
div{border-bottom:1px solid red;}

#### 宽度和高度

因此一个元素实际宽度（盒子的宽度）=左边界+左边框+左填充+内容宽度+右填充+右边框+右边界。

#### 填充
元素内容与边框之间是可以设置距离的，称之为“填充”。填充也可分为上、右、下、左(顺时针)。如下代码：

	div{padding:20px 10px 15px 30px;}
	
#### 边界
元素与其它元素之间的距离可以使用边界（margin）来设置。边界也是可分为上、右、下、左。如下代码：

	div{margin:20px 10px 15px 30px;}	
	
### 布局模型

CSS包含3种基本的布局模型，用英文概括为：Flow、Layer 和 Float。
在网页中，元素有三种布局模型：

1. 流动模型（Flow）
2. 浮动模型 (Float)
3. 层模型（Layer）	

#### 层模型有三种形式：

1. 绝对定位(position: absolute)
2. 相对定位(position: relative)
3. 固定定位(position: fixed)

### 盒模型代码简写
盒模型外边距(margin)、内边距(padding)和边框(border)设置上下左右四个方向的边距是按照顺时针方向设置的：上右下左
#### 通常有下面三种缩写方法:

1、如果top、right、bottom、left的值相同，如下面代码：

	margin:10px 10px 10px 10px;
	
可缩写为：

	margin:10px;
	
2、如果top和bottom值相同、left和 right的值相同，如下面代码：

	margin:10px 20px 10px 20px;
	
可缩写为：

	margin:10px 20px;
	
3、如果left和right的值相同，如下面代码：

	margin:10px 20px 30px 20px;
	
可缩写为：

	margin:10px 20px 30px;

`注意：`padding、border的缩写方法和margin是一致的。

#### 颜色值缩写

当你设置的颜色是16进制的色彩值时，如果每两位的值相同，可以缩写一半。

例子1：

	p{color:#000000;}
	
可以缩写为：

	p{color: #000;}	

#### 字体缩写	

```
body{
    font-style:italic;
    font-variant:small-caps; 
    font-weight:bold; 
    font-size:12px; 
    line-height:1.5em; 
    font-family:"宋体",sans-serif;
}
```

这么多行的代码其实可以缩写为一句：

```
body{
    font:italic  small-caps  bold  12px/1.5em  "宋体",sans-serif;
}
```

### 单位值

#### 颜色值

1、英文命令颜色

	p{color:red;}
	
2、RGB颜色

这个与 photoshop 中的 RGB 颜色是一致的，由 R(red)、G(green)、B(blue) 三种颜色的比例来配色。

	p{color:rgb(133,45,200);}
	
每一项的值可以是 0~255 之间的整数，也可以是 0%~100% 的百分数。如：

	p{color:rgb(20%,33%,25%);}
	
3、十六进制颜色

这种颜色设置方法是现在比较普遍使用的方法，其原理其实也是 RGB 设置，但是其每一项的值由 0-255 变成了十六进制 00-ff。

	p{color:#00ffff;}

{%img center /2016_03_10_color_sheet.gif %}	

#### 长度值
1、像素
像素为什么是相对单位呢？因为像素指的是显示器上的小点。

2、em
就是本元素给定字体的 font-size 值，如果元素的 font-size 为 14px ，那么 1em = 14px；如果 font-size 为 18px，那么 1em = 18px。如下代码：

	p{font-size:12px;text-indent:2em;}
	
3、百分比

p{font-size:12px;line-height:130%}
设置行高（行间距）为字体的130%（12 * 1.3 = 15.6px）。


### 小技巧

#### 居中设置

如果被设置元素为文本、图片等行内元素时，水平居中是通过给父元素设置 text-align:center 来实现的。如下代码：
html代码：

	
	<body>
	  <div class="txtCenter">我是文本，哈哈，我想要在父容器中水平居中显示。</div>
	</body>

css代码：
	
	<style>
	  div.txtCenter{
	    text-align:center;
	  }
	</style>


#### 水平居中设置-定宽块状元素
满足定宽和块状两个条件的元素是可以通过设置“左右margin”值为“auto”来实现居中的。我们来看个例子就是设置 div 这个块状元素水平居中：

html代码：

	<body>
	  <div>我是定宽块状元素，哈哈，我要水平居中显示。</div>
	</body>

css代码：

	<style>
	div{
	    border:1px solid red;/*为了显示居中效果明显为 div 设置了边框*/
	    
	    width:500px;/*定宽*/
	    margin:20px auto;/* margin-left 与 margin-right 设置为 auto */
	}
	
	</style>
	
#### 水平居中总结-不定宽块状元素方法
不定宽度的块状元素有三种方法居中（这三种方法目前使用的都比多）：

1. 加入 table 标签
2. 设置 display;inline 方法
3. 设置 position:relative 和 left:50%;	

#### 垂直居中-父元素高度确定的单行文本
父元素高度确定的单行文本的竖直居中的方法是通过设置父元素的 height 和 line-height 高度一致来实现的

#### 垂直居中-父元素高度确定的多行文本

父元素高度确定的多行文本、图片、块状元素的竖直居中的方法：

使用插入 table (包括tbody、tr、td)标签，同时设置 vertical-align：middle。

#### 隐性改变display类型

### 后记

简单的学习了一遍 CSS， 还需要大量练习，继续努力吧，只有不敢想，没有做不到。