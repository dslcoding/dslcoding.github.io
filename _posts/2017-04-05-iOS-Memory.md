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


没有啦，完啦

