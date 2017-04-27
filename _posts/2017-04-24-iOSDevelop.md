---
layout: post
title:  "2017-04-24 iOS小知识小记"
date:   2017-04-24
categories: jekyll update
---



##  NSNotification

1.iOS8及以前，NSNotificationCenter持有的是观察者的unsafe_unretained指针（可能是为了兼容老版本），这样，在观察者回收的时候未removeOberser，而后再进行post操作，则会向一段被回收的区域发送消息，所以出现野指针crash。而iOS9以后，unsafe_unretained改成了weak指针，即使dealloc的时候未removeOberser，再进行post操作，则会向nil发送消息，所以没有任何问题。

2.postNotification:总是会卡住当前线程，待observer执行（如不特殊处理selector也会在postNotification:所在线程执行）结束之后才会继续往下执行。所以是同步的。

3.postNotification:必须是在主线程。所以不是在主线程的时候，有两种方式可以解决。这里用的是block的方式

```

[[NSNotificationCenter defaultCenter] addObserverForName:@"dsl" object:nil queue:[NSOperationQueue mainQueue] usingBlock:^(NSNotification * _Nonnull note) {
        NSLog(@"current thread %@ 刷新UI", [NSThread currentThread]);
        // 刷新UI ...
    }];

```
[苹果文档](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Notifications/Introduction/introNotifications.html#//apple_ref/doc/uid/10000043-SW1)

## iOS枚举
1.NS_OPTIONS

```
typedef NS_OPTIONS(NSUInteger, UISwipeGestureRecognizerDirection) {
    UISwipeGestureRecognizerDirectionNone = 0,  //值为0
    UISwipeGestureRecognizerDirectionRight = 1 << 0,  //值为2的0次方
    UISwipeGestureRecognizerDirectionLeft = 1 << 1,  //值为2的1次方
    UISwipeGestureRecognizerDirectionUp = 1 << 2,  //值为2的2次方
    UISwipeGestureRecognizerDirectionDown = 1 << 3  //值为2的3次方
};
```
形式typedef NS_OPTIONS(NSUInteger,opttions ) {

}官方推荐的。NSUInteger这个枚举值是固定的，opttions这个是枚举类型。可以自定义的。

2.NS_ENUM
```
typedef NS_ENUM(NSInteger, NSWritingDirection) {
    NSWritingDirectionNatural = -1,  //值为-1    
    NSWritingDirectionLeftToRight = 0,  //值为0
    NSWritingDirectionRightToLeft = 1  //值为1       
};
```

形式typedef NS_ENUM(NSInteger,opttions ) {

}官方推荐的。NSInteger这个枚举值是固定的，opttions这个是枚举类型。可以自定义的。

3.总结两者的不同：1.枚举值是不一样的一个是NSUInteger，一个是NSInteger
             2.如果在一个需要枚举值的地方需要多个枚举值，那么就需要用NS_OPTIONS，比如：
```
JPUSHRegisterEntity * entity = [[JPUSHRegisterEntity alloc] init];
entity.types = JPAuthorizationOptionAlert|JPAuthorizationOptionBadge|JPAuthorizationOptionSound;
```
否则就要用NS_ENUM 只能存在一个枚举值。换句话说就是多选就是options(options本来就有选择的意思)，要是单选的就用ENUM。没了。

## NSInteger 和 NSUInteger

NSInteger 是根据系统的位数来判断的是32位就是int，64是long，

NSUInteger 是一个非零的整数

## weak和assign有什么区别？
1.week主要是修饰对象类型，是弱引用比如修饰delegate，对象释放之后就会被置nil
2.assig是修饰基本的数据类型。

## iOS推送和JPush

1.iOS推送
![](http://ac-l1vX02tC.clouddn.com/d5e0b242795d613c6a0b.png)
- 1.APP向苹果服务器获取token
- 2.苹果服务器把token发送给APP
- 3.APP再把token发送自己的服务器
- 4.APPServer把推送的消息和token一起发送给苹果的服务器
- 5.苹果的服务器在消息推送给APP
- 流程就是这个样子的

2.JPush
![](http://ac-l1vx02tc.clouddn.com/3e9566b834ebbe0f229e.png)
这个图片是我根据极光推送文档中找的，这里就是总结一下。加深对极光推送的理解。
- 1.这条线路走的是苹果的服务器，有JPush推送给苹果的服务器，再有苹果的服务器推送给APP
- 2.这条线路走的是应用内消息，这个其实就是长链接。当APP启动的时候，APP中的JPushSDK与与JPush服务器保持长链接，然后直接推送给APP

- [JPushSDK](https://docs.jiguang.cn/jpush/client/iOS/ios_sdk/)
- [「转载」《微言（官方版）》iOS源码分享--极光推送实践篇 - 分享 - 极光社区](http://ac-l1vx02tc.clouddn.com/f7074b1795b79fb747e9.pdf)
- [详解极光推送的 4 种消息形式—— iOS 篇 - 分享 - 极光社区](http://ac-l1vX02tC.clouddn.com/78b69ececf24ba1ca5d6.pdf)


## SDK和API

- API是一种应用程序与操作系统交互操作的接口规范。
- SDK是应用程序的一种开发包，当然里面会将API进行封装，以方便应用程序的开发。

## SQL中常用的语句

- 1.检查是否存在某条数据(这个问题曾经苦恼我了很久，现在我解决了)
- 解决方法：
```
 NSInteger count = [db intForQuery:@"SELECT COUNT(*) FROM DSLCY WHERE name = ?",deptId];
 ```
- 2.去除重复的内容
```
delete from DSL_ZI where id not in (select min(id) from DSL_ZI group by name )
```
- 3.sql把一个表某字段复制到另一个表

```
update zi SET logogriph = (select mimian from zimi where zi.name = zimi.name)

```
