---
layout: post
title: "iOS UIAutomation 自动化测试"
date: 2016-08-05 17:19:00 +0800
comments: true
categories: 
---

{% img center /2016_08_05_uiautomation_topic.png %}

### 介绍

- 为什么创建自动化测试用例？
- 什么是 UI 自动化测试
- 怎样对一个应用进行自动化测试
- 自动化测试的优点及问题
- 录制自动化测试脚本
- 总结

<!--more-->

## 为什么创建自动化测试用例？

- 在你睡觉的时候就能找到 bug
- 可重复的进行回归测试用例

## 什么是 UIAutomation 自动化测试

UIAutomation的功能测试代码是用Javascript编写的。UIAutomation和Accessibility有着直接的关系，你将用到通过标签和值的访问性来获得UI元素，同时完成相应的交互操作。

自动化测试代码可以让你能够快速地跟踪你程序中的回归和性能方面的问题，这样你就不用担心你新增的功能会影响到你之前已经完成开发的程序了。

## 怎样对一个应用进行自动化测试

这里先看一下苹果官方给出的一些编写测试用例用到的脚本知识。

### 控制层级

- Target application
    - Main window
        - View
            - Element
                - Child element

这是整个的层级关系，必须根据这个层级才能准确地获取一个元素，进而对特定元素进行修改、触发事件等。

```
// Target application
UIATarget.localTarget().frontMostApp()

// Main window
UIATarget.localTarget().frontMostApp().mainWindow ()

// View
UIATarget.localTarget().frontMostApp().mainWindow ().tableViews()[0]

// Element
UIATarget.localTarget().frontMostApp().mainWindow ().tableViews()[0].cells()[0]

// Child element

 UIATarget.localTarget().frontMostApp().mainWindow().tableViews()[0].cells()[0].elements()[“Chocolate Cake”]
```
### 按钮点击


	 UIATarget.localTarget().frontMostApp().navigationBar().buttons()["Add"].tap();

### 文本输入

```
 var name = “Turtle Pie”;

 UIATarget.localTarget().frontMostApp().mainWindow().textFields()[0].setValue(name);
```

### 标签切换

```
 var tabBar = UIATarget.localTarget().frontMostApp().tabBar();
 var selectedTabName = tabBar.selectedButton().name();
 if (selectedTabName != "Unit Conversion") {

  tabBar.buttons()["Unit Conversion"].tap();
}
```

### 视图滚动

```
 UIATarget.localTarget().frontMostApp().mainWindow().tableViews()[0].scrollToElementWithPredicate("name beginswith 'Turtle Pie'");
```

### 校验

```
var cell = UIATarget.localTarget().frontMostApp().mainWindow().tableViews()[0].cells().firstWithPredicate("name beginswith 'Turtle Pie'")
if (cell.isValid())
{
    UIALogger.logPass(testName);
} else {
    UIALogger.logFail(testName);
}
```

### 日志


#### 开始和结束测试

```
var testName = "My first test";
UIALogger.logStart(testName);
...
// test code
...
UIALogger.logPass(testName);
```


#### 测试进行中输出日志

```
var testName = "My first test";
UIALogger.logStart(testName);
...
UIALogger.logMessage("Logging about my test");
...
UIALogger.logPass(testName);
```


### 截取屏幕

```
var testName = "My first test";
UIALogger.logStart(testName);
...
UIALogger.logMessage("Logging about my test");
UIATarget.localTarget().captureScreenWithName(“screenshot1”); // specify a name
for the screenshot
...
UIALogger.logPass(testName);
```




### 处理不期望出现的提示框

```
UIATarget.onAlert = function onAlert(alert) {  var title = alert.name();
  UIALogger.logWarning("Alert with title '" + title + "' encountered!");


    if (title == "Duplicate Recipe Name") {
        alert.buttons()["Continue"].tap();

        return true; // bypass default handler
    }


  return false; // use default handler }
```

### 多任务

- 设置特定时间来禁用应用
- 自动恢复应用

```
UIALogger.logMessage("Deactivating app");
UIATarget.localTarget().deactivateAppForDuration(10);
UIALogger.logMessage("Resuming test after deactivation");
```

### 设备方向

```
var target = UIATarget.localTarget();
var app = target.frontMostApp();
// set landscape left
target.setDeviceOrientation(UIA_DEVICE_ORIENTATION_LANDSCAPELEFT);
UIALogger.logMessage("Current orientation is " + app.interfaceOrientation());


// portrait
target.setDeviceOrientation(UIA_DEVICE_ORIENTATION_PORTRAIT);
UIALogger.logMessage("Current orientation is " + app.interfaceOrientation());
```

### 点击交互高级用法

- 单点
- 捏合
- 拖拽和滑动

```
// Taps
UIATarget.localTarget().tap({x:100, y:200});
UIATarget.localTarget().doubleTap({x:100, y:200});
UIATarget.localTarget().twoFingerTap({x:100, y:200});

// Pinches
UIATarget.localTarget().pinchOpenFromToForDuration({x:20, y:200}, {x:300, y:200},
2);
UIATarget.localTarget().pinchCloseFromToForDuration({x:20, y:200}, {x:300, y:
200}, 2);

// Drag and Flick
UIATarget.localTarget().dragFromToForDuration({x:160, y:200}, {x:160, y:400}, 1);
UIATarget.localTarget().flickFromTo({x:160, y:200}, {x:160, y:400});
```


### 超时处理

```
// Wait for elements to appear
UIATarget.localTarget().frontMostApp().navigationBar().buttons()["Add"];

// Five second default timeout
// Custom timeouts
UIATarget.localTarget().pushTimeout(2);
...

UIATarget.localTarget().popTimeout(); 
```

### 运行 UIAutomation 测试用例
#### 使用iOS模拟器测试

1. 打开项目工程；
2. 确保你的项目可以使用模拟器来运行项目；
3. 启动Instruments(Product > Profile)或从 Xcode 菜单中打开；

	{% img center /2016_08_05_uiautomation_open.png 从菜单中打开 %}
	
4. 选择左边的 iniOS Simulator，然后再选择Automation模板，然后点击“Choose”；

	{% img center /2016_08_05_uiautomation_create.png 选择模版 %}
	
5. Instruments就已经启动好后，然后直接开始录制了。这里先停止录制；

6. 在左边的Scripts窗口，点击“Add > Create”创建新的脚本；

7. 在脚本编辑器里，输入下面的代码：

	```
	var target = UIATarget.localTarget();
	var app = target.frontMostApp(); 
	var window = app.mainWindow(); 
	target.logElementTree();
	```
8. 重新运行这段脚本，脚本跑起来后，你可以在日志打完后停止它。

#### 使用iOS设备测试

这里说的是使用开发证书编译打包，直接在 iOS 设备上运行好了程序，然后使用 Instruments 进行测试。

1. 通过USB接口连接上你的iPhone；
2. 选择设备及目标测试应用；

	{% img center /2016_08_05_device_select.png 选择设备及目标测试应用 %}

3. 编写测试用例，并运行；

	{% img center /2016_08_05_run_script.png 编写脚本并运行 %}


#### 对 iOS 项目进行 Monkey 测试

1. [下载 Monkey测试脚本](http://7xkrxl.com1.z0.glb.clouddn.com/iOSMonkeyTest.rtf)
2. 在左边的Scripts窗口，点击“Add > Import”导入 Monkey 测试脚本；

	{%img center /2016_08_05_run_monkey_script.png Monkey 测试脚本 %}
3. 运行脚本。

你就可以休息了，看着各种页面之间的跳转测试，而且每个测试环节还有截屏图片。	
##  自动化测试的优点及问题
通过编写自动化测试，了解到自动化测试的优点和存在的问题。

 优点：

 - 可以节省测试人力成本；
 - 减少人为操作失误；
 
 缺点：
 
 - 编写测试脚本耗费时间和精力； 
 - 编写脚本获取元素之前必须清楚层级关系；
 - 出现对话框或对于网络请求相关页面不好处理，需要人工处理；
 

## 录制自动化测试脚本


除了刚才说到的手动编写测试脚本，你还可以直接在设备上或者模拟器上录制脚本，然后替换掉原来的。
操作步骤：

1. 启动Instruments；

2. 创建一个新的脚本；

3. 选择脚本编辑器；

4. 在脚本编辑器的底端，你是否看到了一个红色的按钮？点击它！

5. 现在，你可以操作你的应用程序；你将看到录制的交互操作出现在脚本窗口（甚至旋转事件）。点击方形按钮来停止录制。

`注意:`

当遇到问题时，加上“UIATarget.delay(1);”

当你在编写脚本的时候，你总是在与时间，动画打交道。UIAutomation有很多方式去获取控件元素，然后等待它们变为可用状态，即使有时候它们还没有显示出来，但根据这篇文档里提到的，最好的建议是：

当遇到问题时，加上UIATarget.delay(1);!



## 总结

自动化测试还是值得尝试的，毕竟避免重复的工作是我们的追求，做自动化测试前期要投入大量的时间成本，但一旦这个流程搭建好只好，也许你会发现，这种工作方式为你节省不少时间。


参考文献：

 [WWDC 2010 - Session 306 - Automating User Interface Testing with Instruments](http://developer.apple.com/devcenter/download.action?path=/wwdc_2010/wwdc_2010_video_assets__pdfs/306__automating_user_interface_testing_with_instruments.pdf)