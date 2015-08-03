---
layout: post
title: "Mac 计算代码的总行数"
date: 2013-11-19 21:41:37 +0800
comments: true
categories: 
---

计算代码的行数

在终端上的命令，作用：找到项目的根目录，可以计算出代码的行数

```
find . -name "*.m" -or -name "*.h" |xargs grep -v "^$"|wc -l
grep -v "^$"是去掉空行
find . "(" -name "*.m" -or -name "*.strings" -or -name "*.h" ")" -print | xargs wc -l
```

赶快试一试吧，看看你的代码量是多少！