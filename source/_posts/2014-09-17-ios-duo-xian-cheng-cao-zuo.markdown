---
layout: post
title: "iOS多线程操作：锁 互斥 同步"
date: 2014-10-17 23:03:45 +0800
comments: true
categories: 
---


在iOS中有几种方法来解决多线程访问同一个内存地址的互斥同步问题，本文简单介绍：

##### 方法一，@synchronized(id anObject),(最简单的方法)
会自动对参数对象加锁，保证临界区内的代码线程安全

<!--more-->

```
@synchronized(self)  
{  
     // 这段代码对其他 @synchronized(self) 都是互斥的         
     // self 指向同一个对象  
}   
``` 

##### 方法二，NSLock
NSLock对象实现了NSLocking protocol，包含几个方法：
lock，加锁
unlock，解锁
tryLock，尝试加锁，如果失败了，并不会阻塞线程，只是立即返回NO
lockBeforeDate:，在指定的date之前暂时阻塞线程（如果没有获取锁的话），如果到期还没有获取锁，则线程被唤醒，函数立即返回NO
比如：

```
NSLock *theLock = [[NSLock alloc] init];   
if ([thelock lock])   
{  
   //do something here  
   [theLock unlock];   
}   
```
##### 方法三，NSRecursiveLock，递归锁
NSRecursiveLock，多次调用不会阻塞已获取该锁的线程。

```
 NSRecursiveLock *theLock = [[NSRecursiveLock alloc] init];   
 void MyRecursiveFunction(int value)   
{   
 	[theLock lock];   
 	if (value != 0)   
	<span style=“font-size:14px;”> </span>{   
    –value;   
    MyRecursiveFunction(value);   
 }  
	 [theLock unlock];   
}   
 MyRecursiveFunction(5);  
``` 
##### 方法四，NSConditionLock，条件锁
NSConditionLock，条件锁，可以设置条件

```
//公共部分  
id condLock = [[NSConditionLock alloc] initWithCondition:NO_DATA];       
 //线程一，生产者  
 while(true) {   
        [condLock lockWhenCondition:NO_DATA];   
        //生产数据  
        [condLock unlockWithCondition:HAS_DATA];   
}     
 //线程二，消费者  
 while (true) {   
        [condLock lockWhenCondition:HAS_DATA];   
        //消费  
        [condLock unlockWithCondition:NO_DATA];   
}  
```
##### 方法五，NSDistributedLock，分布锁
NSDistributedLock，分布锁，文件方式实现，可以跨进程
用tryLock方法获取锁。
用unlock方法释放锁。
如果一个获取锁的进程在释放锁之前挂了，那么锁就一直得不到释放了，此时可以通过breakLock强行获取锁。
