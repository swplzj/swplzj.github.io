---
layout: post
title: "iOS - UIWebView 动态载入/删除/更新外部 JavaScript/CSS 文件"
date: 2015-03-12 19:59:54 +0800
comments: true
categories: 
---
##### 动态载入 JavaScript/CSS 文件 
传统加载外部 `JavaScript(*.js)` 或者 `CSS(*.css)` 文件的方法是直接在 `<head>` 标签里面进行添加: 

```
	<head> 
	<script type="text/javascript" src="myscript.js"></script> 
	<link rel="stylesheet" type="text/css" href="main.css" /> 
	</head> 
```

<!--more-->

这些文件用这种方式会同步加载到当前这个页面。 

##### 用动态的方式载入 JavaScript/CSS 文件： 

用 DOM createElement 方法创建一个 “script” 或者 ”link” 元素 
设置相应的属性 
使用 appendChild 方法, 把创建的元素插入到 head 标签的末尾 

``` 
function loadjscssfile(filename, filetype){ 
//如果文件类型为 .js ,则创建 script 标签，并设置相应属性 
if (filetype=="js"){ 
var fileref=document.createElement('script'); 
fileref.setAttribute("type","text/javascript"); 
fileref.setAttribute("src", filename); 
} 
//如果文件类型为 .css ,则创建 script 标签，并设置相应属性 
else if (filetype=="css"){ 
var fileref=document.createElement("link"); 
fileref.setAttribute("rel", "stylesheet"); 
fileref.setAttribute("type", "text/css"); 
fileref.setAttribute("href", filename); 
} 
if (typeof fileref!="undefined") 
document.getElementsByTagName("head")[0].appendChild(fileref); 
} 
//动态添加一个.js 文件 
loadjscssfile("myscript.js", "js"); 
//像添加.js文件一样，动态添加一个.php文件 
loadjscssfile("javascript.php", "js"); 
//动态一个.css文件 
loadjscssfile("mystyle.css", "css"); 
```

##### 为了防止多次载入同一个js/css文件， 添加以下判断（这只是粗略检测） 

```
//临时载入的文件名 
var filesadded=""; 
function checkloadjscssfile(filename, filetype){ 
if (filesadded.indexOf("["+filename+"]")==-1){ 
loadjscssfile(filename, filetype); 
//把 [filename] 存入 filesadded 
filesadded+="["+filename+"]"; 
} 
else{ 
alert("file already added!"); 
} 
//第一次载入 
checkloadjscssfile("myscript.js", "js"); 
//重复载入同一个文件， 失败 
checkloadjscssfile("myscript.js", "js"); 
```

##### 动态删除 JavaScript/Csss 文件 
注意：ie6/7 下动态删除样式时有bug. 2种解决方案：1.样式表里不要有import的样式表 2.把link的type属性设置为空值， 然后再修改 href 的地， 或者直接设置href为空， 最后再把type值设置成”text/css” 强制让ie解释新的样式表。 

取得相应的 DOM 元素 
根据 文件名&文件类型 定位元素 
用 DOM removeChild 删除一个 “script” 或者 ”link” 元素 

```
function removejscssfile(filename, filetype){ 
//判断文件类型 
var targetelement=(filetype=="js")? "script" : (filetype=="css")? "link" : "none"; 
//判断文件名 
var targetattr=(filetype=="js")? "src" : (filetype=="css")? "href" : "none"; 
var allsuspects=document.getElementsByTagName(targetelement); 
//遍历元素， 并删除匹配的元素 
for (var i=allsuspects.length; i>=0; i--){ 
if (allsuspects[i] && allsuspects[i].getAttribute(targetattr)!=null && allsuspects[i].getAttribute(targetattr).indexOf(filename)!=-1) 
allsuspects[i].parentNode.removeChild(allsuspects[i]); 
} 
} 
removejscssfile("somescript.js", "js"); 
removejscssfile("somestyle.css", "css"); 
```

##### 动态更新 JavaScript/Csss 文件 
使用 createElement 创建 要更新的 JavaScript/Css 元素 
查找要被替换的元素 
用 replaceChild 替换元素 

``` 
function createjscssfile(filename, filetype){ 
if (filetype=="js"){ //if filename is a external JavaScript file 
var fileref=document.createElement('script') 
fileref.setAttribute("type","text/javascript") 
fileref.setAttribute("src", filename) 
} 
else if (filetype=="css"){ //if filename is an external CSS file 
var fileref=document.createElement("link") 
fileref.setAttribute("rel", "stylesheet") 
fileref.setAttribute("type", "text/css") 
fileref.setAttribute("href", filename) 
} 
return fileref 
} 

function replacejscssfile(oldfilename, newfilename, filetype){ 
var targetelement=(filetype=="js")? "script" : (filetype=="css")? "link" : "none"; 
var targetattr=(filetype=="js")? "src" : (filetype=="css")? "href" : "none"; 
var allsuspects=document.getElementsByTagName(targetelement); 
for (var i=allsuspects.length; i>=0; i--){ 
if (allsuspects[i] && allsuspects[i].getAttribute(targetattr)!=null && allsuspects[i].getAttribute(targetattr).indexOf(oldfilename)!=-1){ 
var newelement=createjscssfile(newfilename, filetype); 
allsuspects[i].parentNode.replaceChild(newelement, allsuspects[i]); 
} 
} 
} 
//用 "newscript.js" 替换 "oldscript.js" 
replacejscssfile("oldscript.js", "newscript.js", "js")； 
//用 "newscript.css" 替换 "oldscript.css" 
replacejscssfile("oldstyle.css", "newscript.css","css"); 
```
