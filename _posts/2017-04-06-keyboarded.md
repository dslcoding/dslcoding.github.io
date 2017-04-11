---
layout: post
title:  "关于Keyboard Extension"
date:   2017-04-01 10:59:21 +0700
categories: jekyll update
---
# 关于Keyboard Extension

------

写了好几天的keyboarded extension， 说实话这个Extension 有点恶心。我感觉bug挺多的，也不能这样说，可能不经常出现的bug都让我遇到了。下面开始说我遇到到了那些问题，以及是怎么做的。

声明：本文章不做文档翻译，想看翻译自行查询.(网上的都是翻译不解决实际问题。我已经。。。。) [键盘扩展翻译](http://www.jianshu.com/p/987dfa9f3baf)
### 1 代码分享。hostApp 与containApp代码分享和TodayExtortion 是一样的。这里不做多说 [TodayExtortion](https://dslcoding.github.io/Today-Extension/)

### 2数据共享。hostApp 与containApp代码分享和TodayExtortion 是一样的。不做多说 [TodayExtortion](https://dslcoding.github.io/Today-Extension/)

### 3申请ID。hostApp 与containApp代码分享和TodayExtortion 是一样的。不做多说[TodayExtortion](https://dslcoding.github.io/Today-Extension/)

以上都是共同点，毕竟都是扩展。

下面我认为是重点(因为我是吃过亏的)：

### 1.关于在扩展中使用cocoapods，这个会者不难，难者恶心。
首先先看stackoverflow[Extension cocoapods](http://stackoverflow.com/questions/37280077/error-with-cocoapods-link-with-after-update-to-1-0-0)这个是我在扩展中使用cocoapads出现问题在stackoverflow中找到的目前我找的最佳答案。如果谁知道更好的方法，给我也说说。按照这个上面的说的直接就可以用在hostApp与containApp分别导入相应库就可以了。
![](http://ac-l1vX02tC.clouddn.com/5adbe79572ebbc69b073.png)
主意：提示框如果要是使用第三方的话 ，(算了还是别用第三放了，因为第三方的有sharedApplication，在扩展中获取不到sharedApplication对象)，一定主意。当你按照上面的内容 pod install 成功的时候，你会发现在项目中的hostApp 与containApp中使用的一些库的头文件报错，意思是找不到相应的文件，遇到这种情况，别着急。因为不添加这个use_frameworks!，cocoapads是用static libraries 管理代码的，如果是使的话，cocoapads则是用framework的形式管理代码。所以你必须分别到hostApp 与containApp 中的Target中的BuildPhases 中的 Linked Framework and Libraries 添加相应的第三库，运行一下，顺利通过。这个cocoapds就到此为止。
### 2.关于键盘的高度
我测试过，讯飞的键盘的高度大概在280左右。于是我也是280.但是我发现一个问题。我的手机是iOS9，但是按照官方文档的自定义高度的方法，不起作用，我在iOS10就可以。于是我🈶️去stackoverflow转了一下，找到了这个。就顺利解决了[自定义键盘高度](http://stackoverflow.com/questions/36145450/incorrect-keyboard-extension-height-after-rotation)。我写在viewdidload里面。就是加载不出来。哎。。。
### 3.切换输入法
官方文档明确表示，没有给用户切换下个输入法的第三方键盘是不会过审核的。大家主意安全。我一开始就用的是这个方法，
```Objective-C
- (void)handleInputModeListFromView:(nonnull UIView *)view withEvent:(nonnull UIEvent *)event NS_AVAILABLE_IOS(10_0);
```
 在iOS10上面用的挺爽的。到iOS9上怎么测试就是行。后来看看那个只是针对iOS10 的，尴尬。用这个
 ```Objective-C
 //iOS9 和iOS10都是可以的。可以试试
  - (void)advanceToNextInputMode;
 ```
### 4.推荐一些第三方的。
keyboard [Extension第三方](https://github.com/polishedcode/tasty-imitation-keyboard)。这个自定义键盘其实也不太难。他是一个控制器 UIInputViewController继承自UIViewController，说白就是一个控制器，除了不能获取sharedApplication对象，别的还可以。还有就是坐标的问题，要主意一下。每个人都有获取坐标的习惯不一样，比如我是这样的self.view.frame.size.with。这个在扩展中是不能获取到的。主意。

### 5.记得把RequestsOpenAccess设置为YES。

### 6.至今未解决的问题。
请求帮助 我的需求是扩展键盘上放了一个uicollectview。每个cell上都加载一个图片。获取图片的步骤：从网络上请求，转化data在转成base64，生成base64字符串。存到数据库。显示的时候，将base64字符串转成data 在转成image；每个图片大概有1Mgif动图吧。不知掉哪个环节出现问题。以至于总是报如下的两个错误。(后来我用工具检测发现内存蹦了600M，用的SDWebimage加载的)。后来用了各种的方法解决也没解决成功就放弃了。

### 6.1遇见的插件中断和服务中断。下面是服务中断的错误信息

2017-03-24 21:37:45.820834 ExpressionBaby[3672:934876] viewServiceDidTerminateWithError:: Error Domain=_UIViewServiceInterfaceErrorDomain Code=3 "(null)" UserInfo={Message=Service Connection Interrupted} 2017-03-24 21:37:48.709876 ExpressionBaby[3672:934876] [App] if we're in the real pre-commit handler we can't actually add any new fences due to CA restriction 2017-03-24 21:37:49.222492 ExpressionBaby[3672:934876] viewServiceDidTerminateWithError:: Error Domain=_UIViewServiceInterfaceErrorDomain Code=3 "(null)" UserInfo={Message=Service Connection Interrupted} 2017-03-24 21:37:51.231664 ExpressionBaby[3672:934876] viewServiceDidTerminateWithError:: Error Domain=_UIViewServiceInterfaceErrorDomain Code=3 "(null)" UserInfo={Message=Service Connection Interrupted} 2017-03-24 21:37:53.225378 ExpressionBaby[3672:934876] [App] if we're in the real pre-commit handler we can't actually add any new fences due to CA restriction 2017-03-24 21:37:54.500931 ExpressionBaby[3672:934876] [App] if we're in the real pre-commit handler we can't actually add any new fences due to CA restriction 2017-03-24 21:37:55.016598 ExpressionBaby[3672:934876] viewServiceDidTerminateWithError:: Error Domain=_UIViewServiceInterfaceErrorDomain Code=3 "(null)" UserInfo={Message=Service Connection Interrupted}
### 6.1 上架补充，上架的时候一定要有数字和英文键盘,另一个是协议必须要有的，![](http://ac-l1vX02tC.clouddn.com/7b86d88e2a3da8847506.png)
协议内容就是关于用户的隐私什么的,大概内容就是要保护用户的隐私.(必须要写的)

我叫董诗磊我就是一码农。


