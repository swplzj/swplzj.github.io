---
layout: post
title: "rake 和 zsh 冲突的解决方法"
date: 2016-08-04 14:40:14 +0800
comments: true
categories: 
---

{% img center /2016_08_04_zsh_topic.png %}

搭建 Jenkins 持续集成之后，总结一下搭建经验吧，写一篇 blog 记录一下，遇到了问题。。。。。。现在用的 shell 是 `oh-my-zsh`，发现执行 `rake new_post["newpost"]`时提示`zsh: no matches found: new_post[newpost]`，不能新建文章。转到`bash`下再执行相同命令却能成功，想到可能是`zsh`的问题。`Google`之，在`octpress`的`issues`里找到了答案：`zsh`会转义`[]`。

解决的方法有下面三种：
<!--more-->

1. alias rake="noglob rake"，noglob用来取消转义。
2. rake "new_post[title]"。
3. rake new_post\[title\]。