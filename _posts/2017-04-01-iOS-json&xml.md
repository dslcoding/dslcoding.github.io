ß---
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
### 时间长啦。我都忘了。所以花了点时间准备一下，下面是demo
### 1.资源文件详见demo，或者找一些json文件直接转成xml。自行搜索。
### 2.demo
### 2.1
```Objective-C
//首先建立一个model类

@interface DSLModel : NSObject

@property (nonatomic ,copy) NSString *proID;

@property (nonatomic ,copy) NSString *proName;

@property (nonatomic ,copy) NSString *pinyin;

@property (nonatomic ,copy) NSString *hits;

@end

```
### 2.2 Viewcontrol操作
```Objective-C
- (void)xml{

    //声明一个数组，用来存放解析的数据
    self.dataArray = [[NSMutableArray alloc]init];

    NSString * path = [[NSBundle mainBundle]pathForResource:@"DSL.xml" ofType:nil];


    NSXMLParser * xmlParser = [[NSXMLParser alloc]initWithData:[NSData dataWithContentsOfFile:path]];

    xmlParser.delegate = self;

    [xmlParser parse];


}


#pragma mark NSXMLParserDelegate
//文档读区开始
- (void)parserDidStartDocument:(NSXMLParser *)parser
{

    NSLog(@"parse start");


}

//文档读取结束
- (void)parserDidEndDocument:(NSXMLParser *)parser
{
    NSLog(@"parser end ===%@",self.dataArray);

    [self.dataArray enumerateObjectsUsingBlock:^(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {

        DSLModel *model = (DSLModel*)obj;

        NSLog(@"====%@===%@==%@",model.proID,model.pinyin,model.proName);


    }];

}

//解析标签开始
- (void)parser:(NSXMLParser *)parser didStartElement:(NSString *)elementName namespaceURI:(NSString *)namespaceURI qualifiedName:(NSString *)qName attributes:(NSDictionary<NSString *,NSString *> *)attributeDict{

    self.startTagFlag = elementName;

    if ([elementName isEqualToString:@"result"]) {

        self.model = [[DSLModel alloc]init];

        [self.dataArray addObject:self.model];

    }



}

//获取标签的内容
- (void)parser:(NSXMLParser *)parser foundCharacters:(NSString *)string{

    string = [string stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceAndNewlineCharacterSet]];

    if ([self.startTagFlag isEqualToString:@"proID"]) {

        [self.model setProID:string];
    }

    if ([self.startTagFlag isEqualToString:@"proName"]) {

        [self.model setProName:string];

    }


    if ([self.startTagFlag isEqualToString:@"pinyin"]) {

        [self.model setPinyin:string];
    }

}

//解析标签结束
- (void)parser:(NSXMLParser *)parser didEndElement:(NSString *)elementName namespaceURI:(NSString *)namespaceURI qualifiedName:(NSString *)qName
{

    self.startTagFlag = nil;


}
```
[相关demo](https://github.com/dslcoding/JsonXml)

没有啦，完啦。
