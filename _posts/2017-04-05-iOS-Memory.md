---
layout: post
title:  "内存管理"
date:   2017-04-01 10:59:21 +0700
categories: jekyll update
---
# 内存管理

------
今天花啦点时间了解一些自动释放池@autoreleasepool
先说说ARC是啥。我的理解是。ARC是苹果公司在iOS5出现的。并在xcode4.2使用的。虽然没有经历过MRC的时代，但是我也做了一些了解。做个记录.
<!-- ### 在MRC时代如果用刀alloc，new，copy这些关键字。都需要使用release来释放。而autorelease是把对象放到内存池里，autoreleasepool释放的时候，该对想也跟着释放了。另外还有一个关键字是retain这个是将该对象的引用计数加1. -->
### ARC是采用引用计数的管理，基于编译器的机制来管理内存的。这样的话，开发人员不用手动添加release，autorelease。是编译器在编译的时候，在合适的地方加入release，autorelease。只要没有强指针引用就被释放了。这样就大大方便了开发人员。而且在某种程度来说也是一种优化。编译速度更快。

## 在MRC的@autoreleasepool

```Objective-C
NSAutoreleasePool *DSLpool = [[NSAutoreleasePool alloc] init];

NSString* DSLStr = [[[NSString alloc] initWithString:@"tutuge"] autorelease];
// DSLStr...

[pool release];

//DSLStr release
```
上面表达的意思是对象创建完之后顺便在发送一个autorelease的消息，然后NSAutoreleasePool结束的时候就释放了。

## 在ARC的@autoreleasepool

```Objective-C
NSArray *urls = <# An array of file URLs #>;
for (NSURL *url in urls) {

    @autoreleasepool {
        NSError *error;
        NSString *fileContents = [NSString stringWithContentsOfURL:url
                                         encoding:NSUTF8StringEncoding error:&error];
        /* Process the string, creating and autoreleasing more objects. */
    }
}
//把相关执行的demo放到@autoreleasepool{}中。因为在ARC中我们不用给对象发送autorelease对象，编译器在编译的时候帮我们添加了，是不是很方便。
```
根据[苹果的官方文档](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmAutoreleasePools.html)在以下几种情况下使用@autoreleasepool：
- 写命令行程序，没有UI界面
- 常见大量的临时对象
- 创建新的线程(非Cocoa程序创建的线程)
- 长时间在后台的任务
上面的实例就是用到了@autoreleasepool的循环，创建大量的临时对象。利用@autorelease可以及时对内存进行释放。

## MRC情况下的引用计数的增加
1. 如果定义一个属性，属性用strong，retain，copy修饰的，在使用set***或者是点语法的时候引用计数都会增加的,原因是：旧对象与新对象不是同一个对象，当一个对象换了当前它所使用的对象的时候，就要让被使用的对象release一次，方法是在set方法中，让旧对象release（就是在换对象的时候让被换掉的就对象release一次，被使用的新对象retain一次）。如果新旧对象是一样的，就要加判断如果是一样的话，就不操作，不一样的话就操作
2. alloc，new，[mutabcopy]copy,

## 内存泄漏
1. 第一种是对象没有被释放，但是不能被引用了。
2. 第二种是内存的无限增长，不能被释放。

## 内存泄漏的解决方法
1. 使用NSZombieEnabled，点击Product--> Scheme -->Edit Scheme 在Run选项的Diagnositics中设置Enable Zombie Objects。然后Close。再次运行，可能会出现一些问题提示
2. 使用instruments中的Zombies这个工具
3. 使用静态内存分析工具，Product→Analyze，速度快，但是 准确率较低
- 内存泄漏隐患提示：Potential Leak of an object allocated on line ……
- 数据赋值隐患提示：The left operand of …… is a garbage value;
- 对象引用隐患提示：Reference-Counted object is used after it is released;

## 使用内存注意的地方
1. 使用频率高且小的图片用imageNamed，又缓存
2. 大图片用initWithContentsOfFile，没有缓存，用完就消失

## 内存方面的专业术语
1. 内存泄漏，内存一直被占着，不能被释放
2. 内存益处，内存不够用
3. 内存泄漏会导致内存益处

没有啦，完啦
