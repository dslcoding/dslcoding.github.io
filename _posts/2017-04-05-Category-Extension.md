---
layout: post
title:  "使用类别(Category)和扩展(Extension)"
date:   2017-04-01 10:59:21 +0700
categories: jekyll update
---
# Category-Extension

------


# 1.类别
### 先建立一个类别项目，我这里就直接给VC加一个类别。名字是NSLog。记得选Category，而且Category是有.m文件的。
![](http://ac-l1vX02tC.clouddn.com/b76a58836532443c945d.png)
![](http://ac-l1vX02tC.clouddn.com/4171593997bcac5e30eb.png)
# 1.先测试：
## 1.1添加方法
```Objective-C
//先在.h文件中添加一个方法
- (void)printStr:(NSString*)str;
```
```Objective-C
@implementationViewController (NSLog)

//在.m中实现

- (void)printStr:(NSString*)str

{

NSLog(@"====print==%@",str);

}

@end
```
```Objective-C
//在VC中调用类别的方法,运行

//[self printStr:@"我叫董诗磊,我就是一码农"];

//成功输出
```
## 1.2添加属性
```Objective-C
@interfaceViewController (NSLog)

//在没有使用runtim建立关联引用建立的字符串

@property(nonatomic,copy)NSString* dsl;

////先添加一个方法

//- (void)printStr:(NSString *)str;

@end
```
```Objective-C
//在VC中做赋值操作，，，，，然后成功崩溃，

self.dsl=@"我叫董诗磊,我就是一码农";
```
崩溃的原因
![](http://ac-l1vX02tC.clouddn.com/8364ed8677d3c03fa6af.png)
不识别set方法。这里就可以说明是可以添加属性，但是不会自动生成get,set方法。不过这里可以使用runtime建立关联。
如果有这方面的需求可以用runtime具体的下面
```Objective-C
导入头文件#import <objc/runtime.h>
```
```Objective-C
.h

@interfaceViewController (NSLog)

//声明属性

@property(nonatomic,copy)NSString* dsl;

@end

```
```Objective-C
.m

static NSString*dslKEY =@"dslKEY";

@implementationViewController (NSLog)

- (void)printStr:(NSString*)str

{

NSLog(@"====print==%@",str);

}
```
```Objective-C
.m

- (void)setDsl:(NSString*)dsl

{

//四个参数原对象，关联时的用来标记的是哪个属性key(可能要添加多个属性)

objc_setAssociatedObject(self, &dslKEY, dsl,OBJC_ASSOCIATION_COPY);

}
- (NSString*)dsl

{

returnobjc_getAssociatedObject(self,&dslKEY);

}
//在VC中测试输出字符串字符串

self.dsl=@"我叫董诗磊,我就是一码农";

NSLog(@"====use runtime ===%@",self.dsl);

成功输出
```
以上都是Category添加属性和方法
我的理解是：

# 1.1类别就是扩展类可以添加方法。不能添加成员变量。

### 1.2如果添加@property的话编译是没有问题。但是运行的时候就会报错的。也可以用runtime来关联引用实现添加属性。

### 1.3如果类别的方法和原类的方法名重复的话，优先调用类别的方法。原类的犯法会忽略。尽量别这样

### 1.4继承和类别

### 1.4.1关于继承和类别我的理解是如果这个类有很多子类的时候，只需要扩展这个类不影响到原有的代码继承较好，要是紧紧扩展方法的话，分类更好。
### 1.4.2类别主要是针对系统提供的一些类，系统本身是不提供继承去扩展方法的。继承子类继承父类，而类别不能扩展属性(runtime可以)，方法也不能重复，一旦重复。就会替换原有的方法。没了
--------------------------------------------------
# 2.扩展

# 扩展是类别的一种特例。也是匿名类，可以声明属性和方法，声明的方法要实现

### 2.1先测试给VC加Extension
![](http://ac-l1vX02tC.clouddn.com/8fe3dce41b2b974be9fd.png)
![](http://ac-l1vX02tC.clouddn.com/55f2357045a9e0e3a59b.png)
### 实现方法1.就是上面的那一种，建立一个Extension.h文件然后声明
```Objective-C
#import"ViewController.h"

@interfaceViewController ()

@property(nonatomic,copy) NSString * vcExten;

- (void)test;

@end
```
```Objective-C
- (void)viewDidLoad {

[self test];

//扩展在ViewDidLoad里面实现并调试

self.vcExten=@"exten";

NSLog(@"====use exten ==%@",self.vcExten);

}

- (void)test

{

NSLog(@"extension===");

}
```
### 实现方法2.就是上面的那一种，建立一个Extension.h文件然后声明
```Objective-C
#import"ViewController.h"

#import"ViewController+NSLog.h"

#import"ViewController_VCExten.h"

这个@interface ......@end本身就是扩展

@interfaceViewController()

@end

@implementationViewController

- (void)viewDidLoad {

[superviewDidLoad];



}
```