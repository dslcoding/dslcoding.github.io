---
layout: post
title:  "Json&Xml"
date:   2017-04-01 10:59:21 +0700
categories: jekyll update
---
# Json&Xml

------

## 由于公司的项目不涉及大量的数据解析。所以解析数据的机会就越来越少，常说熟能生巧。我这都快生了。今天做个总结，也算是个复习吧！
## 首先说明本次的json解析不实用第三方。json解析的第三方有JsonKit，SBJosn，AFNetworking，YY系列等等有很多。根据个人的编码习惯，自己选择。我的习惯是不使用第三方。
## 不就是解析个数据嘛。
## 动动小手，啥都有。
## 废话不多说直接上代码。
## json解析
### 解析json数据 用苹果官方给出的NSJSONSerialization（记得是iOS5的） 解析方式是性能最优越的，复杂程度还可以。
```Objective-C
    NSURLSessionDataTask *tast = [[NSURLSession sharedSession]dataTaskWithURL:[NSURL URLWithString:@"https://api.douban.com/v2/movie/subject/25881786"] completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {
        
        
        
        //最外层是个字典直接用字典接收。如果是数组就用数组
        NSDictionary * dic = [NSJSONSerialization JSONObjectWithData:data options:NSJSONReadingMutableContainers error:nil];
        
         NSLog(@"=====%@",[dic objectForKey:@"alt"]);
         //成功输出=====https://movie.douban.com/subject/25881786/===具体的自己代码测试啊
        
        
    }];
    
    [tast resume];

```
### 生成json字符串
```Objective-C
 NSDictionary * dic = @{
                           @"large":@"https://img3.doubanio.com/view/movie_poster_cover/lpst/public/p2276855500.jpg",
                           @"medium":@"https://img3.doubanio.com/view/movie_poster_cover/spst/public/p2276855500.jpg"
                           };
    
    NSData * data = [NSJSONSerialization dataWithJSONObject:dic options:NSJSONWritingPrettyPrinted error:nil];
    
    NSLog(@"===%@",[[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

```
## xml解析
### 这个xml解析说实话。我都已经把他给忘了，因为从我工作第一天到现在没有遇见过xml解析。不知道现在还有没有公司在使用。不管有没有公司在使用，今天是做记录的，都要记录的。
首先对iOS的xml的概念做些了解，iOSxml解析主要有两种方式，SAX和DOM解析。SAX解析是标签读取，逐行解析数据，采用协议的方式获取数据。DOM解析就是把整个文档读入，将整个文档生成一个树结构，有节点，子节点等，然后对节点进行解析。
### 这里不做演示。如果记不得啦，自行搜索

没有啦，完啦。






























































