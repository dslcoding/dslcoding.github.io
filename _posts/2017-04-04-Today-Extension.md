---
layout: post
title:  "关于Today Extension"
date:   2016-09-02 10:59:21 +0700
categories: jekyll update
---
# 关于Today Extension

 第一次写Extension功能，下面是我的一些步骤。这是对[iOS App Extension Programming Guide](https://developer.apple.com/library/content/documentation/General/Conceptual/ExtensibilityPG/)的链接。
 
效果图
 ![1.效果图](http://ac-l1vX02tC.clouddn.com/96a2456ba7b86754dd5f.gif)
 最近写了一个app需要用到app扩展(App Extension)，本次主要讲的是TodayExtension这个扩展，[关于Toady的官方介绍自己先看](https://developer.apple.com/library/content/documentation/General/Conceptual/ExtensibilityPG/Today.html)，废话不多说直接写代码，既然写了那就从申请appID开始：
# 1.先新建一个项目
### 2.申请ID：
![](http://ac-l1vX02tC.clouddn.com/7f3efb74e794561cdf4d.png)
![](http://ac-l1vX02tC.clouddn.com/80b8867cba66a1929a0f.png)
然后点击注册完成；
### 3.申请App Groups
![](http://ac-l1vX02tC.clouddn.com/1cb58b390dbb5deb4c54.png)
![](http://ac-l1vX02tC.clouddn.com/7275b2a6a7bdb69170c3.png)
然后点击继续按钮，在点击注册，就会出现以下界面
![](http://ac-l1vX02tC.clouddn.com/ee405194982e3324bdaf.png)
去iOSAppIDS这个界面找到Name为：BlobBlobWidget的ID，点击编辑
然后
![](http://ac-l1vX02tC.clouddn.com/76f3046f289180ed7307.png)
![](http://ac-l1vX02tC.clouddn.com/caad348a05a49cd01ab5.png)
点击assign就可以了
### 4.找到ID发现下面都变绿说明配置成功了，如果不是的话，估计你得重复在来一次
![](http://ac-l1vX02tC.clouddn.com/c75d1f73f2e4dce09b9c.png)
# 2.开始代码项目中的配置
### 2.1新建扩展
![](http://ac-l1vX02tC.clouddn.com/cb99dbd24f51d209b728.png)
### 2.2先配置General
![](http://ac-l1vX02tC.clouddn.com/70afb2a17787624f1467.png)
### 2.3在配置Capabilities
![](http://ac-l1vX02tC.clouddn.com/b9e3044909cad2ef2d2a.png)
好了以上是宿主app也就是主app配置完成。
# 6.下面配置TodayExtension
### 6.1配置General
![](http://ac-l1vX02tC.clouddn.com/34945dd01ed65a48043d.png)
### 6.2配置Capabilities
![](http://ac-l1vX02tC.clouddn.com/90631f921812fc0525ee.png)
以上都配置完了运行以下，正常了就是正常的，不正常就是不正常了，顺便自己看看哪里出的问题，bug在哪里自己心里最清楚，别人都是爱莫能助啊。
# 7.关于调试
在调试的时候xcode会弹出一个调试框框，让你选择一个主APP。主APP就是要选择你建立的APP；不知道看懂没有。
# 8数据的共享

### 8.1通过用NSUserDefaults共享数据

```Objective-C
存储：

#pragma mark存储data

- (void)saveData:(UITextField*)TF{

NSUserDefaults* defaults = [[NSUserDefaultsalloc]initWithSuiteName:@"group.DSLBlobWidget"];

[defaultssetObject:TF.textforKey:@"DSL"];

[defaultssynchronize];

}
获取：

#pragma mark从app那里加载数据以便达到数据共享的目的

- (NSString*)getDatafromMainAPP{

NSUserDefaults*defaults = [[NSUserDefaultsalloc]initWithSuiteName:@"group.DSLBlobWidget"];

return[defaultsobjectForKey:@"DSL"];

}
```
### 8.2通过NSFileManger存图片
```Objective-C
//copy image to Library

- (void)copyImageToLibrary{

NSString * path = [[NSBundle mainBundle]pathForResource:@"DSL.png" ofType:nil];

NSFileManager * manger = [NSFileManager defaultManager];

NSString * documentPath = [NSHomeDirectory() stringByAppendingPathComponent:@"/Library/Caches/DSL.png"];

NSLog(@"%@",documentPath);

NSError * error = nil;

if (path != nil) {

if (![manger fileExistsAtPath:documentPath]) {

[manger copyItemAtPath:path toPath:documentPath error:&error];

if (error) {

NSLog(@"==copy error==%@",[error

localizedDescription]);

}

}

}else{

NSLog(@"sourcePath is nil");

}

[self copyImage];

}

#pragma mark从app那里加载数据以便达到数据共享的目的（NSFileManger 存图片）

- (void)copyImage{

NSError * error = nil;

NSURL * contentURL = [[NSFileManager defaultManager]containerURLForSecurityApplicationGroupIdentifier:@"group.DSLBlobWidget"];

NSString * newPath = [contentURL URLByAppendingPathComponent:@"/Library/Caches/DSL.png"].path;

[[NSFileManager

defaultManager]copyItemAtPath:[NSHomeDirectory()

stringByAppendingPathComponent:@"/Library/Caches/DSL.png"]

toPath:newPath error:&error];

if (error) {

NSLog(@"copy error==%@",[error localizedDescription]);

}

}
```
# 9.代码的共享

### 9.1第一步：和创建APP Extension一样，New->Target，选择Cocoa Touch Framework来创建framework

### 9.2第二部：起名字，我的起的名字是DSLWidgetFramework

### 9.3
![](http://ac-l1vX02tC.clouddn.com/02a4f537451a8f98f257.png)
# 10.在APP Extension中不能使用的API
### 10.1 Access a sharedApplication object, and so cannot use any of the methods on that object
不能获取sharedApplication对象

### 10.2Use any API marked in header files with theNS_EXTENSION_UNAVAILABLEmacro, or similar unavailability macro, or any API in an unavailable framework，For example, in iOS 8.0, the HealthKit framework and EventKit UI framework are unavailable to app extensions.

不能使用API的标志性头文件 和 theNS_EXTENSION_UNAVAILABLE 宏定义，还有一些不能用的框架API,例如HealthKit framework and EventKit UI framework。

### 10.3Access the camera or microphone on an iOS device (an iMessage app, unlike other app extensions, does have access to these resources, as long as it correctly configures theNSCameraUsageDescriptionandNSMicrophoneUsageDescriptionInfo.plistkeys)

不能获取麦克风和照相机的权限(iMessage 不同于其它的应用扩展，只要配置相关的应用权限就可以直接使用了)

### 10.4Perform long-running background tasks

不能长期的在后台运行

### 10.5Receive data using AirDrop

不能使用AirDrop接收相关的数据

以上个人翻译，建议[查看官方文档](https://developer.apple.com/library/content/documentation/General/Conceptual/ExtensibilityPG/ExtensionOverview.html#//apple_ref/doc/uid/TP40014214-CH2-SW6)
# 11 .TodayExtension的进入设置的快捷方式
```Objective-C
//打开Wi-Fi

[self.extensionContextopenURL:[NSURLURLWithString:@"Prefs:root=WIFI"]completionHandler:^(BOOLsuccess) {

}];
//打开蜂窝网络

[self.extensionContextopenURL:[NSURLURLWithString:@"Prefs:root=MOBILE_DATA_SETTINGS_ID"]completionHandler:^(BOOLsuccess) {

}];
```
[更多方式请点击这里,请尽量按照以上方式避免被拒绝.](https://github.com/cyanzhong/app-tutorials/blob/master/prefs.md)
# 12.与主APP的交互
![](http://ac-l1vX02tC.clouddn.com/9b23ab7bdf2069aa01df.png)
在扩展中的代码：
```Objective-C
- (void)OpenAcstion{

[self.extensionContextopenURL:[NSURLURLWithString:@"DSLTodayWidget://SecondVC"]completionHandler:^(BOOLsuccess) {

}];

}
```
在主APP中的代码：
AppDelegate
```Objective-C
- (BOOL)application:(UIApplication*)app openURL:(NSURL*)url options:(NSDictionary *)options

{

NSLog(@"=====%@",url.scheme);

NSLog(@"===%@===%@",url.host,url.absoluteString);

if([url.absoluteStringhasPrefix:@"DSLTodayWidget"]) {

if([url.hostisEqualToString:@"SecondVC"]) {

//判断是否是直接跳入到添加页面

//self.window.rootViewController = [[SecondVC alloc]init];

UIViewController*rootNav = (UIViewController*)self.window.rootViewController;

[rootNavpresentViewController:[[SecondVCalloc]init]animated:YEScompletion:nil];

}

}

returnYES;

}
```
# 13.折叠与展开

### 13.1首先折叠与展开只有iOS10才有，所以先要判断系统的版本
```Objective-C
if ([[UIDevice currentDevice].systemVersion integerValue]>=10) {

self.extensionContext.widgetLargestAvailableDisplayMode = NCWidgetDisplayModeExpanded;

}
```
然后实现协议，点击展开或者是折叠会触发这个协议就是NCWidgetProviding协议
```Objective-C
- (void)widgetActiveDisplayModeDidChange:(NCWidgetDisplayMode)activeDisplayMode withMaximumSize:(CGSize)maxSize

{

//    NCWidgetDisplayModeCompact, // Fixed height

//    NCWidgetDisplayModeExpanded,

if (activeDisplayMode == NCWidgetDisplayModeCompact ) {

//高度最低为110

self.preferredContentSize = CGSizeMake([UIScreen mainScreen].bounds.size.width ,110);

}else{

//因为机子型号不一样所以最大搞多可能不一样，这里设置最大

self.preferredContentSize = CGSizeMake([UIScreen mainScreen].bounds.size.width ,MAXFLOAT);

}

}
```
除了上面的协议外还有一个协议就是更新扩展UI界面的协议

```Objective-C
该协议的具体用法，我没有探究，需要更新这个界面的时候就在这方法里面写

- (void)widgetPerformUpdateWithCompletionHandler:(void (^)(NCUpdateResult))completionHandler {

// Perform any setup necessary in order to update the view.

// If an error is encountered, use NCUpdateResultFailed

// If there's no update required, use NCUpdateResultNoData

// If there's an update, use NCUpdateResultNewData

completionHandler(NCUpdateResultNewData);

}
```
# 14.关于使用StoryBoard或者是代码

### 14.1使用StryBoard如果使用自己定义的类的StoryBoard的话，需要修改info文件

### 14.2![](http://ac-l1vX02tC.clouddn.com/c08223452b889dbf901b.png)
使用这个方法不要忘记在todayViewController的ViewDidLoad中设置preferredContentSize属性调整大小。
14关于上架，上架的时候扩展app一定是单独的appID，且命名时比如：主appID是com.DSLWidget那么扩展appID是com.DSLWidget.widExten这样尽量按照这个格式来，不要问我为什么之前上架踩过的坑

本次关于Today Extension之旅结束。随后补充。有错请立即通知我，谢谢。

[dd](_posts/2017-04-03-sand-box.md)

我叫董诗磊我就是一码农。