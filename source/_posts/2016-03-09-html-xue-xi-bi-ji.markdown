---
layout: post
title: "HTML 学习笔记"
date: 2016-03-09 13:54:31 +0800
comments: true
categories: 
---
最新项目提交到 AppStore，等待审核过程中，有空学习点 HTML 知识，顺便做了一下笔记，其实之前也学习过网页设计，但是没有系统的学习，现在，我只想说：I am back... 要重新系统的学习一遍，学以致用，再对自己的 Blog 完美升级，期待中...
<!--more-->

### HTML 属性

属性为 HTML 元素提供附加信息。
属性总是以名称/值对的形式出现，比如：name="value"。
属性总是在 HTML 元素的开始标签中规定。

##### lang 属性

文档语言可以在 <html> 标签中来声明，语言声明使用 `lang` 属性。
声明语言对屏幕阅读器（供盲人使用的软件）和搜索引擎都是很重要的。


	<!DOCTYPE html>
	<html lang="en-US">
	<body>
	
	<h1>My First Heading</h1>
	<p>My first paragraph.</p>
	
	</body>
	</html>

#####  标题属性

HTML 中段落用 `<p>`标签定义。
可以给段落添加 `title` 属性，当鼠标放到段落上时，就会出现标题。

	<p title="About W3Schools">
	W3Schools is a web developer's site.
	It provides tutorials and references covering
	many aspects of web programming,
	including HTML, CSS, JavaScript, XML, SQL, PHP, ASP, etc.
	</p>

##### 超链接属性

HTML 链接用 `<a>` 来定义，链接地址可以在 `href` 属性中具体说明。

	<a href="http://www.baidu.com">这是一个链接</a>

##### 尺寸属性

图片可以使用 `<img>` 标签来定义。
在图片标签中提供资源的文件名称（src）和图片的尺寸（width 和 height）等属性。

	<img src="demo.jpg" width="100" height="200">

##### alt属性

当HTML中图片没有被显示的时候，可以使用 `alt` 属性来设置可供替换的文本。

##### align对齐方式

	<h1 align="center">This is a heading 1</h1>

##### bgcolor背景颜色

	<body bgcolor="yellow">

##### border 表格边框

`HTML 提示：`使用小写属性，始终为属性值加引号。

属性值应该始终被包括在引号内。双引号是最常用的，不过使用单引号也没有问题。
在某些个别的情况下，比如属性值本身就含有双引号，那么您必须使用单引号，

	name='Say "HelloWorld", Tom'

### HTML 标题

##### 水平线

`<hr />` 标签在 HTML 页面中创建水平线。
hr 元素可用于分隔内容。


标题很重要。

请确保将 HTML heading 标签只用于标题。不要仅仅是为了产生粗体或大号的文本而使用标题。

搜索引擎使用标题为您的网页的结构和内容编制索引。

因为用户可以通过标题来快速浏览您的网页，所以用标题来呈现文档结构是很重要的。
应该将 h1 用作主标题（最重要的），其后是 h2（次重要的），再其次是 h3，以此类推。

##### 注释

	<!-- This is a comment -->

### HTML 段落

	<p>This is a paragraph</p>

`注释`：浏览器会自动地在段落的前后添加空行。（<p> 是块级元素）

`提示`：使用空的段落标记 <p></p> 去插入一个空行是个坏习惯。用 <br /> 标签代替它！（但是不要用 <br /> 标签去创建列表。）

`提示`：通过结束标签来关闭 HTML 是一种经得起未来考验的 HTML 编写方法。清楚地标记某个元素在何处开始，并在何处结束，不论对您还是对浏览器来说，都会使代码更容易理解。

##### HTML 折行
	
	<p>This is<br />a para<br />graph with line breaks</p>

`<br> 还是 <br />`

您也许发现 `<br>` 与 `<br />` 很相似。
在 XHTML、XML 以及未来的 HTML 版本中，不允许使用没有结束标签（闭合标签）的 HTML 元素。
即使 `<br>` 在所有浏览器中的显示都没有问题，使用 `<br />` 也是更长远的保障。

### HTML 格式化

#### 文本格式化

	<html>
	
	<body>
	
	<b>This text is bold</b>
	<br />
	<b>这是一个加粗的文本 </b>
	<br />
	
	<strong>This text is strong</strong>
	
	<br />
	
	<strong>这是一个加重语气的文本</strong>
	<br />
	
	<big>This text is big</big>
	
	<br />
	
	<big>这是一个big字体</big>
	<br />
	
	<em>This text is emphasized</em>
	
	<br />
	
	<em>这是一个强调的文本</em>
	<br />
	
	<i>This text is italic</i>
	
	<br />
	
	<i>这是斜体文本</i>
	
	<br />
	
	<small>This text is small</small>
	
	<br />
	
	<small>这是small 字体</small>
	
	<br />
	
	This text contains
	<sub>subscript</sub>
	
	
	<sub>这是下标</sub>
	
	<br />
	
	This text contains
	<sup>superscript</sup>
	<sup>这是上标</sup>
	
	<br />
	
	3<sup>2</sup>
	<br />
	
	<b><big>3</big></b><sub>万</sub>
	</body>
	</html>

#### 预格式文本

`<pre>`预格式标签，保留了空格和换行

<pre>这是
预格式文本。
它保留了 空格 和
换行</pre>

<p>pre 标签很适合计算机代码显示</p>

	<pre>
	for i = 1 to 10
		print i
	next i
	</pre>

#### 计算机输出标签

	<code> Computer code </code>
	<br />
	<kbd>Keyboard input</kbd>
	<br />
	<tt>Teletype text</tt>
	<br />
	<samp>Sample text</samp>
	<br />
	<var>Computer variable</var>
	<br />

#### 地址标签

	<address>
	Written by <a href="mailto:swplzj@163.com">Leo</a>
	<br />
	Visit me at:
	<br />
	<a href="http://swplzj.github.io">swplzj.github.io</a>
	<br />
	ShiLiPu, ChaoYang, Beijing
	<br />
	China
	</address>
	
#### 缩写和首字母缩写

	<abbr title="etcetera">etc.定义缩写</abbr>
	
	<br />
	<acronym title="World Wide Web">WWW（定义首字母缩写）</acronym>
	<p>在某些浏览器中，当您把鼠标移至缩略词语上时，title 可用于展示表达的完整版本。</p>
	
	<p>仅对于 IE 5 中的 acronym 元素有效。</p>
	
	<p>对于 Netscape 6.2 中的 abbr 和 acronym 元素都有效。</p>
	
#### 文本方向

	<p>
	如果你的浏览器支持 bi-directional override (bdo), 下一行会从右向左输出<rtl>
	</p>
	<bdo dir="rtl">从右向左读</bdo>

#### 块引用

	这是长的引用：
	<blockquote>	
	这是长的引用。这是长的引用。这是长的引用。这是长的引用。这是长的引用。这是长的引用。这是长的引用。这是长的引用。
	</blockquote>
	
	这是短的引用。
	<q>
	这是短的引用。
	</q>


cite 引用

	<cite>	这是引用</cite>

	<dfn>定义一个定义项目</dfn>
	
#### 删除字效果和插入字效果

	<p>	
	一打有<del>二十</del><ins>十二</ins>件
	</p>

### 样式

如何使用样式

	1. 外部样式表；
	当样式需要被应用到很多页面的时候，外部样式表将是理想的选择。使用外部样式表，你就可以通过更改一个文件来改变整个站点的外观。
	2. 内部样式表；
	当单个文件需要特别样式时，就可以使用内部样式表。你可以在 head 部分通过 <style> 标签定义内部样式表。
	3. 内联样式；	
	当特殊的样式需要应用到个别元素时，就可以使用内联样式。 使用内联样式的方法是在相关的标签中使用样式属性。样式属性可以包含任何 CSS 属性
#### 使用添加到 <head> 部分的样式信息对 HTML 进行格式化。

	<html>	
	
	<head>
	<style type="text/css">
	h1 {color: red}
	p {color: blue}
	</style>
	</head>
	
	<body>
	<h1>标题</h1>
	<p>段落</p>
	</body>
	
	</html>
	
#### 	使用样式属性做一个没有下划线的链接。

	<a href="http://swplzj.github.io" style="text-decoration: none" >这是我的网址(不带下划线的链接)</a>	

#### 使用 <link> 标签链接到一个外部样式表。

		<link rel="stylesheet" type="text/css" href="csstest1.css">


### HTML 链接

#### 将图像作为链接

	<a href="http://swplzj.github.io">
 	<img src="2.gif" title="Leo"> </img>
 	</a>	
 	
有两种使用 `<a>` 标签的方式：

	1. 通过使用 href 属性 - 创建指向另一个文档的链接
	2. 通过使用 name 属性 - 创建文档内的书签 	
	
##### target属性

使用 Target 属性，你可以定义被链接的文档在何处显示。

	<a href="http://swplzj.github.io" target="_blank">Leo's Blog”</a>
	
##### name属性

name 属性规定锚（anchor）的名称。
	
<a href="#tips01">锚点定义</a>	

	<a name="label">锚（显示在页面上的文本）</a>
	
`提示`：锚的名称可以是任何你喜欢的名字。您可以使用 id 属性来替代 name 属性，命名锚同样有效。
	
	
	<a href="http://www.w3school.com.cn/html/html_links.asp#tips">有用的提示</a>

在上面的代码中，我们将 # 符号和锚名称添加到 URL 的末端，就可以直接链接到 tips 这个命名锚了。

	
### 图像

#### 插入图像

`<img>` 是空标签，意思是说，它只包含属性，并且没有闭合标签。

##### 替换文本属性（Alt）

	<img src="boat.gif" alt="Big Boat">

#### 从不同的位置插入图片	
	
	<p>这是插入图片<img src="1.png">的段落</p>
	<br />
	<p>这是上对齐<img src="1.png" align="top">插入图片</p>
	<br />
	<p>这是居中对齐的<img src="1.png" align="center">插入图片</p>
	<br />
	<p>这是下对齐的<img src="1.png" align="bottom">插入图片</p>
	
#### 浮动图像	
	
	
	<p>这是下对齐的<img src="1.png" align="right">插入图片</p>			
#### 把图像转换为图像映射

	<p>请点击图片中的星球，把它放大</p>
			<img src="image/eg_planets.jpg" border="0" usemap="#planetmap" alt="Planets" />
			<map name="planetmap" id="planetmap">

			<area shape="circle" coords="180,139,14" href="image/venus.html" target="_blank" alt="Venus" />	
			<area shape="circle" coords="129,161,10" href="image/" />
			<area shape="rect" coords="0,0,110,260" href="image/sun.html" target="_blank" alt="Sun" />
			</map>
	
### 表格

表格由 `<table>` 标签来定义。每个表格均有若干行（由 `<tr>` 标签定义），每行被分割为若干单元格（由 `<td>` 标签定义）。字母 td 指表格数据（table data），即数据单元格的内容。数据单元格可以包含文本、图片、列表、段落、表单、水平线、表格等等。

##### 表格和边框属性
	
	<table border="1">
	<tr>
	<td>Row 1, cell 1</td>
	<td>Row 1, cell 2</td>
	</tr>
	</table>
	
##### 表格的表头
表格的表头使用 `<th>` 标签进行定义。
大多数浏览器会把表头显示为粗体居中的文本：

	
##### 表格中的空单元格	
	
`注意`：这个空的单元格的边框没有被显示出来。为了避免这种情况，在空单元格中添加一个空格占位符，就可以将边框显示出来。

	<table border="1">
	<tr>
	<td>row 1, cell 1</td>
	<td>row 1, cell 2</td>
	</tr>
	<tr>
	<td>&nbsp;</td>
	<td>row 2, cell 2</td>
	</tr>
	</table>
	
##### Cell padding 和 Cell spacing 的区别
	
使用 Cell padding 来创建单元格内容与其边框之间的空白。	
使用 Cell spacing 增加单元格之间的距离。	
	
##### caption 标签

caption 元素定义表格标题。
caption 标签必须紧随 table 标签之后。您只能对每个表格定义一个标题。通常这个标题会被居中于表格之上。

	<table border="1">
	  <caption>Monthly savings</caption>
	  <tr>
	    <th>Month</th>
	    <th>Savings</th>
	  </tr>
	  <tr>
	    <td>January</td>
	    <td>$100</td>
	  </tr>
	</table>
	
	
##### thead 元素应该与 tbody 和 tfoot 元素结合起来使用。

<thead> 标签定义表格的表头。该标签用于组合 HTML 表格的表头内容。

tbody 元素用于对 HTML 表格中的主体内容进行分组，而 tfoot 元素用于对 HTML 表格中的表注（页脚）内容进行分组。
	
	
	
	<table border="1">
	  <thead>
	    <tr>
	      <th>Month</th>
	      <th>Savings</th>
	    </tr>
	  </thead>
	
	  <tfoot>
	    <tr>
	      <td>Sum</td>
	      <td>$180</td>
	    </tr>
	  </tfoot>
	
	  <tbody>
	    <tr>
	      <td>January</td>
	      <td>$100</td>
	    </tr>
	    <tr>
	      <td>February</td>
	      <td>$80</td>
	    </tr>
	  </tbody>
	</table>	
	
	
##### col 标签	
	

	<table width="100%" border="1">
	  <col align="left" />
	  <col align="left" />
	  <col align="right" />
	  <tr>
	    <th>ISBN</th>
	    <th>Title</th>
	    <th>Price</th>
	  </tr>
	  <tr>
	    <td>3476896</td>
	    <td>My first HTML</td>
	    <td>$53</td>
	  </tr>
	</table>

##### colgroup标签
	
	<table width="100%" border="1">
	  <colgroup span="2" align="left"></colgroup>
	  <colgroup align="right" style="color:#0000FF;"></colgroup>
	  <tr>
	    <th>ISBN</th>
	    <th>Title</th>
	    <th>Price</th>
	  </tr>
	  <tr>
	    <td>3476896</td>
	    <td>My first HTML</td>
	    <td>$53</td>
	  </tr>
	</table>
	
	
### 列表

#### 定义列表
自定义列表不仅仅是一列项目，而是项目及其注释的组合。
自定义列表以 `<dl>` （“definition list”）标签开始。每个自定义列表项以 `<dt>`(“definition title”，定义标题) 开始。每个自定义列表项的定义以 `<dd>`(“definition description”，定义描述) 开始。


	<dl>
	<dt>Coffee</dt>
	<dd>Black hot drink</dd>
	<dt>Milk</dt>
	<dd>White cold drink</dd>
	</dl>	
	
### 块

可以通过 `<div>` 和 `<span>` 将 HTML 元素组合起来。

块级元素在浏览器显示时，通常会以新行来开始（和结束）。
##### HTML 内联元素
内联元素在显示时通常不会以新行开始。
例子：`<b>`, `<td>`, `<a>`, `<img>`
##### HTML `<div>` 元素
HTML `<div>` 元素是块级元素，它是可用于组合其他 HTML 元素的容器。DIV（division）	`<div>` 元素的另一个常见的用途是文档布局。它取代了使用表格定义布局的老式方法。使用 `<table>` 元素进行文档布局不是表格的正确用法。`<table>` 元素的作用是显示表格化的数据。

##### HTML `<span>` 元素
HTML `<span>` 元素是内联元素，可用作文本的容器。
`<span>` 元素可用于为部分文本设置样式属性。
### 布局

提示：即使可以使用 HTML 表格来创建漂亮的布局，但设计表格的目的是呈现表格化数据 - 表格不是布局工具！

### 表单

#### 文本域 (Text field)	


#### 密码域

```
<form>
	用户名：
	<input type="text" name="username"></input>
	<br />

	密码：
	<input type="password"></input>
	<br />

	性别：
	<input type="radio" name="sex" checked="checked" value="male">男</input>
	<input type="radio" name="sex" value="female">女</input>
	<br />

	<fieldset>
		<legend>健康信息</legend>
		身高：<input type="text"></input>
		体重：<input type="text"></input>
	</fieldset>
	<br />

	兴趣：
	<input type="checkbox" value="sing">喜欢唱歌</input>
	
	<input type="checkbox" value="dance">喜欢跳舞</input>
	<br />

	拥有车辆：
	<select name="cars">
		<optgroup label="豪车">
		<option value="audi">Audi</option>
		<option value="bezi">Bezi</option>	
		</optgroup>
		<optgroup label="好车">
		<option value="bmw" selected="selected">BMW</option>
		<option value="volvo">volvo</option>	
		</optgroup>
		
	</select>

	<br />

	自我评价：
	<br />
	<textarea rows="10" cols="30">写下对自己最公正的评价...</textarea>

	<br />
	<input type="submit" value="提交"></input>

	<hr />

	<form action="MAILTO:swplzj@163.com" method="post" enctype="text/plain">
		请填写相关信息，发送邮件
		<br />
		姓名：
		<br />
		<input type="text">
		<br />
		电子邮箱：
		<br />
		<input type="text">
		<br />
		内容：
		<br />
		<input type="text">
		<br />
		<input type="submit" value="发送">
		<input type="reset" value="重置">
	</form>
</form>
```

### 框架

#### 垂直框架
	
```
	<html>

<frameset cols="25%,50%,25%">

  <frame src="/example/html/frame_a.html">
  <frame src="/example/html/frame_b.html">
  <frame src="/example/html/frame_c.html">

</frameset>

</html>
```
#### 水平框架 & noframes
```
<html>

<frameset rows="25%,50%,25%">

  <frame src="/example/html/frame_a.html">
  <frame src="/example/html/frame_b.html">
  <frame src="/example/html/frame_c.html">

<noframes>
<body>您的浏览器无法处理框架！</body>
</noframes>
</frameset>

</html>
```
#### 框架结构标签（frameset）
框架结构标签（frameset）定义如何将窗口分割为框架
每个 frameset 定义了一系列行或列
rows/columns 的值规定了每行或每列占据屏幕的面积

`重要提示：`
不能将 `<body></body>` 标签与 `<frameset></frameset>` 标签同时使用！不过，假如你添加包含一段文本的 `<noframes>` 标签，就必须将这段文字嵌套于 `<body></body>` 标签内。（在下面的第一个实例中，可以查看它是如何实现的。）

#### 混合框架

```
<html>

<frameset rows="50%,50%">

<frame src="/example/html/frame_a.html">

<frameset cols="25%,75%">
<frame src="/example/html/frame_b.html">
<frame src="/example/html/frame_c.html">
</frameset>

</frameset>

</html>
```
```
<frameset rows="25%, *, 50%">
			<frame src="div.html" noresize="noresize">
			<frame src="table.html">
			<frameset cols="25%, 75%">
				<frame src="div.html">
				<frame src="table.html">
			</frameset>
		</frameset>
```

### 内联框架 iFrame

iframe 用于在网页内显示网页。
##### 添加 iframe 的语法

	<iframe src="URL"></iframe>

URL 指向隔离页面的位置。

	<iframe src="http://www.baidu.com" width="200" height="200"></iframe>

##### 使用 iframe 作为链接的目标
iframe 可用作链接的目标（target）。

`注意：`链接的 target 属性必须引用 iframe 的 name 属性：

### 背景
#### bgcolor

```
<body bgcolor="#000000">
<body bgcolor="rgb(0,0,0)">
<body bgcolor="black">
```
#### 背景（Background）
背景属性将背景设置为图像。属性值为图像的URL。如果图像尺寸小于浏览器窗口，那么图像将在整个浏览器窗口进行复制。

```
<body background="clouds.gif">
<body background="http://www.w3school.com.cn/clouds.gif">
```

## HTML 高级教程
----------

### 文档类型
`<!DOCTYPE>` 声明帮助浏览器正确地显示网页。
Web 世界中存在许多不同的文档。只有了解文档的类型，浏览器才能正确地显示文档。
HTML 也有多个不同的版本，只有完全明白页面中使用的确切 HTML 版本，浏览器才能完全正确地显示出 HTML 页面。
`<!DOCTYPE>` 不是 HTML 标签。它为浏览器提供一项信息（声明），即 HTML 是用什么版本编写的。

### HTML 头部
`<head>` 元素是所有头部元素的容器。`<head>` 内的元素可包含脚本，指示浏览器在何处可以找到样式表，提供元信息，等等。
以下标签都可以添加到 head 部分：`<title>`、`<base>`、`<link>`、`<meta>`、`<script>` 以及 `<style>`。
#### 文档的标题
`<title>` 标题定义文档的标题。
title 元素在所有 HTML/XHTML 文档中都是必需的。
title 元素能够：

* 定义浏览器工具栏中的标题
* 提供页面被添加到收藏夹时显示的标题
* 显示在搜索引擎结果中的页面标题

#### 所有链接一个目标
`<base>` 标签为页面上的所有链接规定默认地址或默认目标（target）：

```
<head>
<base href="http://www.w3school.com.cn/images/" />
<base target="_blank" />
</head>
```
#### link 元素
`<link>` 标签定义文档与外部资源之间的关系。
`<link>` 标签最常用于连接样式表：

```
<head>
<link rel="stylesheet" type="text/css" href="mystyle.css" />
</head>
```

#### style 元素

`<style>` 标签用于为 HTML 文档定义样式信息。
您可以在 style 元素内规定 HTML 元素在浏览器中呈现的样式：

```
<head>
<style type="text/css">
body {background-color:yellow}
p {color:blue}
</style>
</head>
```

#### 文档描述
使用 `<meta>` 元素来描述文档。
元数据（metadata）是关于数据的信息。
`<meta>` 标签提供关于 HTML 文档的元数据。元数据不会显示在页面上，但是对于机器是可读的。

典型的情况是，meta 元素被用于规定页面的描述、关键词、文档的作者、最后修改时间以及其他元数据。

`<meta>` 标签始终位于 head 元素中。

元数据可用于浏览器（如何显示内容或重新加载页面），搜索引擎（关键词），或其他 web 服务。

##### 针对搜索引擎的关键词

一些搜索引擎会利用 meta 元素的 name 和 content 属性来索引您的页面。
下面的 meta 元素定义页面的描述：

	<meta name="description" content="Free Web tutorials on HTML, CSS, XML" />


#### 文档关键词
使用 `<meta>` 元素来定义文档的关键词。
	
	<meta name="keywords" content="HTML, CSS, XML" />
	
name 和 content 属性的作用是描述页面的内容。

#### 重定向用户
如何把用户重定向到新的网址。

```	
<head>
<meta http-equiv="Content-Type" content="text/html; charset=gb2312" />
<meta http-equiv="Refresh" content="5;url=http://www.w3school.com.cn" />
</head>
```
#### `<script>` 元素
`<script>` 标签用于定义客户端脚本，比如 JavaScript。

### 脚本

#### 使用 `<noscript>` 标签
如何应对不支持脚本或禁用脚本的浏览器。


	<body>
	<script type="text/javascript">
	document.write("Hello World!")
	</script>
	<noscript>Sorry, your browser does not support JavaScript!</noscript>
	<p>不支持 JavaScript 的浏览器将显示 noscript 元素中的文本。</p>
	</body>


<a name="tips01">锚点</a>	