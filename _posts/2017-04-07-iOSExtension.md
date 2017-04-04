---
layout: post
title:  "iOSExtensionProgram总结"
date:   2017-04-01 10:59:21 +0700
categories: jekyll update
---

# iOSExtensionProgram总结

------

### 1.上架的问题hostapp和containingapp的版本号必须一致，

### 2.关于Bundle identifier的话，hostapp 和 containingapp都是独立的App。所有你必须申请两个ID，

### 3.上面的第二条是申请两个Bundle identifier。这个两个Bundle identifier的名字规则是：hostApp:com.DSL，containingapp:com.DSL.todayWight(通知扩展，还有键盘扩展com.DSL.keyboard)

### 4.关于cocoapods的使用,请看我的写的一篇iOS Keyboarded Extension

### 5.别的问题遇见在说吧



