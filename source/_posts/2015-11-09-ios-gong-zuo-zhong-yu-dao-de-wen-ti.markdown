---
layout: post
title: "iOS - 工作中遇到的问题"
date: 2015-11-09 16:44:05 +0800
comments: true
categories: 
---

在做iOS项目的过程中，到了了一些值得记录下来的问题，以便回顾和帮助他人，跳过他人跌过的坑。
<!--more-->

### 1. UITableViewCell 获取当前Cell的方法

获取cell 的方法一般有两种：
 
* `cellForRowAtIndexPath:`

	利用 tableView 根据indexPath来回调方法，但是这种方法有个值得注意的地方，就是当要获取的cell不可见或者下标超出数组范围的时候，就返回nil；参照文档

	```	
	- (nullable __kindof UITableViewCell *)cellForRowAtIndexPath:(NSIndexPath *)indexPath;   // returns nil if cell is not visible or index path is out of range
	```

* `tableView: cellForRowAtIndexPath:`
	
	利用tableView 的代理方法，使用视图控制器来调用，这种调用的时候，也有点需要注意的地方，就是有可能会重绘Cell。

### 2. 获取Cell上textfield数据的方法

好了，下面有个场景，在一个 tableView 上放置很多自定义 Cell ，cell 上又都有 textfield 输入框，页面最下面有保存按钮，现在问题来了（暂时不关心为什么有这样的需求，为什么使用tableView来显示），当用户在输入框中输入完成之后，滚动到下方，然后，上方cell上textfield的数据被清空了，很显然是Cell被重用了，那解决方法是什么呢。

后来想保存一个Cell数组，每次从这个cell数组中来取值，这时，需要用到上面提到的取Cell 的方法了，这种场景下应该使用哪种方法呢，使用第一种应该没有问题的，但是，有自定义cell需要用户选择值，比如复选按钮，单选按钮，从一个列表中选取项，选择时间等，这就要刷新tableView了，这时，问题又来了，我们需要临时保存用户操作的每一个选项值，最关键的是保存textfield上的值。
使用textfield的代理方法:

```
- (void)textFieldDidEndEditing:(UITextField *)textField
{
    UCCustomerEditCell *cell = (UCCustomerEditCell *)[[textField superview] superview];
    NSInteger row = [self.tableView indexPathForCell:cell].row;
    // 根据下标来保存textField的输入值
}
```
这种方法还是会有问题，在iOS7 和 iOS8、9上都是不一样的，就是因为cell里面的继承层级关系我们不是确定的，导致我们取到的不是我们想要的cell，更不要说cell的下标了，使用tag值也是不可取的，有种方法可以解决这个问题：

```
- (void)textFieldDidEndEditing:(UITextField *)textField
{
    UIView *view = textField;
    while (view != nil && ![view isKindOfClass:[UITableViewCell class]]) {
        view = [view superview];
    }   
    UCCustomerEditCell *cell = (UCCustomerEditCell *)view;
    NSInteger row = [self.tableView indexPathForCell:cell].row;
}
```
这种方法就是一次遍历父类视图，不清楚cell里面的视图关系，但是一直向上遍历父类view，肯定可以找到cell的。
接下来就是保存每个cell中textfield的值，刷新tableView时给重新赋值，这样就解决了这个问题。

欢迎进入我的博客[http://swplzj.github.io](http://swplzj.github.io)