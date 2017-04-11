---
layout: post
title:  "改变Alpha"
date:   2017-04-01 10:59:21 +0700
categories: jekyll update
---
# 改变父控件Alpha子控件不变

------

# 我经常用的一种方法

```Objective-C
UIColor *blockColor = [UIColor blackColor];
    
self.backgroundColor = [blockColor colorWithAlphaComponent:0.75];
```


