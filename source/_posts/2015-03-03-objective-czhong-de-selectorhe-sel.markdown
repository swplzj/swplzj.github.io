---
layout: post
title: "Objective-C中的Selector和SEL"
date: 2015-01-07 10:32:05 +0800
comments: true
categories: 
---
#### Objective-C中Selector基本概念和操作

简而言之，你可以理解 @selector()就是取类方法的编号,他的行为基本可以等同C语言的中函数指针,只不过C语言中，可以把函数名直接赋给一个函数指针，而Objective-C的类不能直接应用函数指针，这样只能做一个@selector语法来取.

<!--more-->   

它的结果是一个SEL类型。这个类型本质是类方法的编号(函数地址)?因此我们有如下代码。
##### 一、取得selector值.

C函数指针

```
int add(int val)  
{  
  return val+1;  
}  
int (* c_func)(int val); //定义一个函数指针变量  
_func = add ;  //把函数addr地址直接赋给c_func 
```
Objective-C的选择器,

```
@interface foo  
-(int)add:int val;  
@end  
 
SEL class_func ; //定义一个类方法指针  
class_func = @selector(add:int); 
```
`注意:`

1. @selector是查找当前类的方法，而[object @selector(方法名:方法参数..) ] ;是取object对应类的相庆方法;
2. 查找类方法时，除了方法名,方法参数也查询条件之一.
3. 可以用字符串来找方法 SEL　变量名　=　NSSelectorFromString(方法名字的字符串);
4. 可以运行中用SEL变量反向查出方法名字字符串
	
NSString　*变量名　=　NSStringFromSelector(SEL参数); 

##### 二、执行selector值.

取得相庆值后，怎么处理SEL值呢，这一点仍然与函数指针一样，就是执行它
   
函数指针执行，（以下有几种等效形式）

* *c_func(10);  
* c_func(10); 
* SEL变量的执行.用performSelecor方法来执行. 
* [对象　performSelector:SEL变量　withObject:参数1　withObject:参数2]; 

##### 三.selector的应用场合
selector本质是跟C的回调函数一样。主要用于两个对象之间进行松耦合的通讯.这种方法很多开发环境用到。比如GTK，Delphi.基本上整个Cocoa库之间对象，控制之间通讯都是在这个基础构建的。


####  SEL

在Objective-C中，SEL是选择器（selector）的一个类型。选择器就是指向方法的一个指针，读者可以简单理解为程序运行到这里就会执行指定的方法，可以这样定义一个选择器：

```
SEL action = [button action]; 
```
我们这样使用一个选择器，下面的选择器都叫做action：

```
[Foo action]  
[Bar action] 
```
在Target-Action 模式（Cocoa 程序中的一种常用模式）中：Target 指定了一个类，Action指定一个方法。在一个对象上设置Action就是通过选择器完成的：

```
-（void）setTarget:（id）target;  
-（void）setAction:（SEL）action; 
```

下述语句设置了一个button对象上的Action为“@selector（start:）”，即它调用start方法：

```
[button setAction:@selector（start:）]; 
```

如果你的方法上有两个参数，比如：

```
-（void）setName:（NSString *）name age:（int）age; 
```

那么，你的选择器应该这样书写：

```
SEL sel = @selector（setName:age:）; 
```

如果方法不存在的话，调用该方法的应用可能会异常中止。所以，需要使用respondsToSelector 方法来判断该对象是否存在对应的方法，使用performSelector:withObject:方法来调用选择器：

```
SEL sel = @selector (start:) ; // 指定action  
if ([obj respondsToSelector:sel]) { //判断该对象是否有相应的方法  
[obj performSelector:sel withObject:self]; //调用选择器方法  
} 
```

下面来看一个应用选择器的实例。

```
#import <Foundation/Foundation.h> 
@interface ClassA : NSObject {  
}  
- (void) print;  
@end  
@implementation ClassA  
- (void) print{  
NSLog (@"I'm ClassA.") ;  
}  
@end  
int main (int argc, const char * argv[]) {  
NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];  
SEL sel = @selector (print) ;  
ClassA *classA = [[ClassA alloc]init];  
[classA performSelector:sel withObject:nil]; //调用选择器指定的方法  
[pool drain];  
return 0;  
} 
```
【程序结果】

```
I'm ClassA. 
```

下面解释一下这段代码，读者有可能看不明白，因为到目前为止并没有讲述类相关的知识。读者只需要了解上述例子中选择器的用法即可，关于类的知识，会在后面的章节详细阐述。

代码首先创建了一个名字叫ClassA 的类，它只包含一个方法print。在随后的实现文件中，我们实现了这个方法：

```
- (void) print{  
NSLog (@"I'm ClassA.") ;  
} 
```

读者不难看出，这个方法仅仅是打印到控制台上一句话。在接下来的main方法中，定义了一个选择器sel，它指向的是一个名叫print 的方法。我们并不知道这个方法是哪个类的，因为具体的信息是在运行期间系统自动帮我们判断的。

```
SEL sel = @selector (print) ; 
```

随后构建了一个对象（读者不用拘泥于语法，我们会在后面的章节详细阐述），并调用这个对象performSelector:withObject:的方法。

```
ClassA *classA = [[ClassA alloc]init];  
[classA performSelector:sel withObject:nil]; 
```

这时候，系统就会自动调用classA对象的print方法，最终得到程序运行结果。