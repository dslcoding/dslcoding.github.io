---
layout: post
title:  "2017-05-02 iOS小知识小记二"
date:   2017-05-02
categories: jekyll update
---

## 深拷贝和浅拷贝
1. 第一种


```
NSString * str = @"ablc";

   self.DstrongStr = str;

   self.DcopyStr = str;

   NSLog(@"====str:%p,%p",str,&str);

   NSLog(@"====strong:%p,%p",_DstrongStr,&_DstrongStr);

   NSLog(@"====copy:%p,%p",_DcopyStr,&_DcopyStr);

   NSLog(@"======mutableCopy==%p",[str mutableCopy]);
   //输出的结果

  2017-05-02 10:43:47.340 NSStringCopyorStrong[65120:2234199] ====str:0x10eb06078,0x7fff510f9978
  2017-05-02 10:43:47.340 NSStringCopyorStrong[65120:2234199] ====strong:0x10eb06078,0x7fdd3460aeb8
  2017-05-02 10:43:47.340 NSStringCopyorStrong[65120:2234199] ====copy:0x10eb06078,0x7fdd3460aec0
  2017-05-02 10:43:47.341 NSStringCopyorStrong[65120:2234199] ======mutableCopy==0x60800007ea40

  //说明：源头是NSString的字符串，不管是copy和strong都不会产生新的对象，是浅拷贝。要是调用mutablecopy方法的话就会产生新的对象。

```

2. 第二种

```
NSMutableString * str = [[NSMutableString alloc]initWithString:@"我叫董诗磊"];

   self.DstrongStr = str;

   self.DcopyStr = str;

   NSLog(@"====str:%p,%p",str,&str);

   NSLog(@"====strong:%p,%p",_DstrongStr,&_DstrongStr);

   NSLog(@"====copy:%p,%p",_DcopyStr,&_DcopyStr);

   NSLog(@"======mutableCopy==%p",[str mutableCopy]);
   // 输出结果

   2017-05-02 10:54:42.122 NSStringCopyorStrong[65181:2238687] ====str:0x6080000738c0,0x7fff5f448978
  2017-05-02 10:54:42.122 NSStringCopyorStrong[65181:2238687] ====strong:0x6080000738c0,0x7fd2e4f0d6c8
  2017-05-02 10:54:42.122 NSStringCopyorStrong[65181:2238687] ====copy:0x6080000475f0,0x7fd2e4f0d6d0
  2017-05-02 10:54:42.122 NSStringCopyorStrong[65181:2238687] ======mutableCopy==0x60000006dcc0

  //说明：源头是NSMutableString，strong仅仅是指针引用，没有产生新的对象，增加引用计数，是浅拷贝，但是copy产生了新的对象，是深拷贝。要是调用mutablecopy方法的话就会产生新的对象

```

#### 总结
- 总结一下，声明一个NSString的变量如果这个变量需要和MutableString的变量同时变化的话，就需要用strong修饰，否则就用copy。如果两者的都是NSString的话，copy，strong都是一样。还有就是两者的内存管理的引用计数都会增加的。
- copy返回不可变对象，mutablecopy返回可变对象
- NSMutableArray必须用strong，copy返回不可变对象，原因：mutablecopy返回可变对象，NSMutableArray用copy的话就变成了NSArray，在调用setter方法后，是对赋值对象的深拷贝，并且拷贝的对象是copy的（不可变的），而不是mutableCopy的（可变的）。所以用copy修饰的mutableArray也被视为Array了，所以再用mutableArray的方法就会发生崩溃。而NSArray必须用copy原因：被strong修饰之后，由于只是强引用，所以副本对象数组和源对象数组只是指向同一个内存区域，这样就会造成副本对象数组会随着源对象数组的改变而改变,即便有时候你并不想让副本对象跟着改变。[博客地址](http://blog.csdn.net/ioswl/article/details/60589076)

### 僵尸对象
1. 僵尸对象就是在对象被调用的时候该对象的内存已经被释放了，指针指向了没有意义的地址控件，于是就变成了野 指针。比如exc_bad_access这中问题就是僵尸对象引起的
