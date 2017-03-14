
[![License MIT](https://img.shields.io/badge/license-MIT-green.svg?style=flat)](https://github.com/waynezxcv/LWAsyncDisplayView/blob/master/LICENSE)&nbsp;



# Gallop v0.6.1

## 关于 Gallop


Gallop是使用异步绘制的富文本、图文混排框架。它封装了CoreText的富文本功能和常用的图片处理功能。
只需要使用框架中的LWTextStorage、LWImageStorage模型来构建文本和图片界面，Gallop将使应用获得优秀的性能。
也可以使用Gallop来解析HTML页面，并自定义加工，将HTML网页解析成iOS原生界面。




## 架构

![](https://github.com/waynezxcv/Gallop/raw/master/pics/architecture.png)

## 特性


* 构建复杂的富文本界面应用，能获得一个优秀的滚动性能。
* 在文本中添加本地、网络图片、UIView及其子类。
* 对文本添加点击、长按事件。
* 绘制文本边框、绘制空心字、设置垂直方向Alignment等。
* 便捷地解析文本中的表情、http(s)链接、@用户、#主题#、电话号码。
* 快捷实现对网络、本地图片圆角和模糊处理等，能对处理过后的图片直接提供缓存，无需重复处理。
* 支持GIF显示


** 安装要求

* iOS 8.0及以上版本

## 依赖

* 网络图片加载、缓存部分依赖于[SDWebImage](https://github.com/rs/SDWebImage) 'SDWebImage', '~>4.0'
* HTML解析依赖libxml2库


## 安装

1. 在XCode的Build Phases-> Link Binary With Libraries中添加libxml2.tbd库
2. 在XCode的Build Setting->Header Search Paths中添加‘/usr/include/libxml2’
3. 安装[SDWebImage](https://github.com/rs/SDWebImage)
4. 将Gallop文件夹下的.h及.m文件添加到你的工程当中
5. #import "Gallop.h"



## 更新说明

v0.6.0

* 依赖于SDWebImage 4.0


v0.5.1

* "- (id)initWithCallbackQueue:(dispatch_queue_t)callbackQueue" -> "- (LWTransaction *)initWithCallbackQueue:(dispatch_queue_t)callbackQueue"
* "+ (id)mainTransactionGroup;" -> "+ (LWTransactionGroup *)mainTransactionGroup;"
* 因与FMDB中的"commit:"方法冲突，修改了这两个方法的返回类型。


v0.3.7
* 修复了contentMode设置无效的问题。


v0.3.6

* 可以通过maxNumberOfLines来设置文本的行数限制。
* 可以通过VericalAlignment来设置文本垂直方向对齐方式。


v0.3.5
* LWImageStorage现在可以对图片进行模糊处理了。
本地图片时，将在子线程进行模糊处理；当网络图片时，将在子线程进行模糊处理并直接缓存模糊的版本。
无需多次重复处理。

v0.3.4
* 支持CoreData来缓存布局模型


v0.3.3
* 更改了集成方式，解决了与SDWebImage部分文件冲突的问题。

v0.3.2
* 现在，设置了圆角半径的网络图片将额外缓存一份，解决了内存消耗过大的问题。

v0.3.1
* 解析HTML渲染生成原生iOS页面时，图片可以按照原图比例自适应高度了。

v0.3.0 
* 增加了解析HTML渲染生成原生iOS页面的功能。

v0.2.5
* 对图片加载进行了优化。

v0.2.4
* 增加了TransactionGroup，LWTransaction，CALayer+LWTransaction。

v0.2.3 
* 文字添加了描边绘制模式。

v0.2.2 
* 现在，LWAsyncDisplayView内部将自动维护一个复用池，可以为LWStorage设置一个NSString*类型的Identifier，
来复用内部的相关UIView,简化API。
* 修复了对文字添加链接重叠而发生冲突的bug.


## API 使用介绍

1.使用LWTextStorage在文本中插入图片、添加点击事件

```objc
//创建LWAsyncDisplayView对象
LWAsyncDisplayView* view = [[LWAsyncDisplayView alloc] initWithFrame:CGRectMake(0.0f,64.0,SCREEN_WIDTH,SCREEN_HEIGHT - 64.0f)];
//设置代理
view.delegate = self;
[self.view addSubview:view];

//创建LWTextStorage对象
LWTextStorage* ts = [[LWTextStorage alloc] init];
ts.frame = CGRectMake(20, 50.0f,SCREEN_WIDTH - 40.0f, ts.suggestSize.height);
ts.text = @"Gallop支持图文混排,可以在文字中插入本地图片→和网络图片→UIView的子类→.给指定位置文字添加链接.快来试试吧。";
ts.font = [UIFont fontWithName:@"Heiti SC" size:16.0f];

UIImage* image = [UIImage imageNamed:@"pic.jpeg"];
UISwitch* switchView = [[UISwitch alloc] initWithFrame:CGRectMake(0, 0, 60, 30)];

//在文字中插入本地图片
[ts lw_replaceTextWithImage:image
contentMode:UIViewContentModeScaleAspectFill
imageSize:CGSizeMake(40, 40)
alignment:LWTextAttachAlignmentTop
range:NSMakeRange(26, 0)];

//在文字中插入网络图片
[ts lw_replaceTextWithImageURL:[NSURL URLWithString:@"http://joymepic.joyme.com/article/uploads/20163/81460101559518330.jpeg?imageView2/1"]
contentMode:UIViewContentModeScaleAspectFill
imageSize:CGSizeMake(80, 40)
alignment:LWTextAttachAlignmentTop
range:NSMakeRange(33, 0)];
//在文字中插入UIView的子类
[ts lw_replaceTextWithView:switchView
contentMode:UIViewContentModeScaleAspectFill
size:switchView.frame.size
alignment:LWTextAttachAlignmentTop
range:NSMakeRange(44, 0)];

//给某位置的文字添加点击事件
[ts lw_addLinkWithData:@"链接 ：）"
range:NSMakeRange(53,4)
linkColor:[UIColor blueColor]
highLightColor:RGB(0, 0, 0, 0.15)];

//给整段文字添加点击事件
[ts lw_addLinkForWholeTextStorageWithData:@"整段文字"
highLightColor:RGB(0, 0, 0, 0.15)];

//给文本添加长按事件
[ts lw_addLongPressActionWithData:@"longPress"
highLightColor:RGB(0, 0, 0, 0.25f)];


//创建LWLayout对象
LWLayout* layout = [[LWLayout alloc] init];
//将LWTextStorage对象添加到LWLayout对象中
[layout addStorage:ts];
//将LWLayout对象赋值给LWAsyncDisplayView对象
view.layout = layout;

//给文字添加点击事件后，若触发事件，会在这个代理方法中收到回调
- (void)lwAsyncDisplayView:(LWAsyncDisplayView *)asyncDisplayView
didCilickedTextStorage:(LWTextStorage *)textStorage
linkdata:(id)data {
if ([data isKindOfClass:[NSString class]]) {
[LWAlertView shoWithMessage:data];
}
}


```

2.LWTextStorage的更多用法

```objc
//用属性字符串创建LWTextStorage
NSMutableAttributedString* as1 = [[NSMutableAttributedString alloc] initWithString:@"世界对着它的爱人，把它浩翰的面具揭下了。它变小了，小如一首歌，小如一回永恒的接吻。"];
[as1 setTextBackgroundColor:[UIColor orangeColor] range:NSMakeRange(0, 9)];
[as1 setTextColor:[UIColor whiteColor] range:NSMakeRange(0, 9)];
[as1 setTextColor:[UIColor blackColor] range:NSMakeRange(9, as1.length - 9)];
[as1 setUnderlineStyle:NSUnderlineStyleDouble underlineColor:[UIColor blueColor]
range:NSMakeRange(9, as1.length - 9)];

LWTextStorage* ts1 = [LWTextStorage lw_textStrageWithText:as1
frame:CGRectMake(20.0f,
ts.bottom + 20.0f,
SCREEN_WIDTH - 40.0f,
CGFLOAT_MAX)];
ts1.font = [UIFont fontWithName:@"Heiti SC" size:18.0f];
ts1.linespacing = 3.0f;


//空心字和外框
LWTextStorage* ts2 = [[LWTextStorage alloc] initWithFrame:CGRectMake(20.0f,
ts1.bottom + 20.0f,
SCREEN_WIDTH - 40.0f,
CGFLOAT_MAX)];
ts2.text = @"The world puts off its mask of vastness to its lover.It becomes small as one song, as one kiss of the eternal.";
ts2.textDrawMode = LWTextDrawModeStroke;
ts2.strokeColor = [UIColor redColor];
ts2.font = [UIFont fontWithName:@"Heiti SC" size:18.0f];
ts2.linespacing = 5.0f;
ts2.textBoundingStrokeColor = [UIColor grayColor];


//在一个LWTextStorage对象后拼接一个LWTextStorage对象
LWTextStorage* ts3 = [[LWTextStorage alloc] init];
ts3.text = @"^_^ 我是那个尾巴~";
ts3.textColor = [UIColor redColor];
ts3.font = [UIFont systemFontOfSize:20];
[ts2 lw_appendTextStorage:ts3];


//将图片装换成属性字符串拼接到LWTextStorage对象后
UIImage* image = [UIImage imageNamed:@"pic.jpeg"];
NSMutableAttributedString* as2 = [NSMutableAttributedString
lw_textAttachmentStringWithContent:image
contentMode:UIViewContentModeScaleAspectFill
ascent:50.0f
descent:0.0f
width:50.0f];
LWTextStorage* ts4 = [LWTextStorage lw_textStrageWithText:as2 frame:CGRectZero];
[ts2 lw_appendTextStorage:ts4];


```

3.LWImageStorage的使用方法

```objc
//普通的加载网络图片
LWImageStorage* is1 = [[LWImageStorage alloc] init];
is1.frame = CGRectMake(SCREEN_WIDTH/2 - 50.0f, ts.bottom + 10.0f, 100.0f, 100.0f);
is1.clipsToBounds = YES;
is1.contents = [NSURL URLWithString:@"http://img.club.pchome.net/kdsarticle/2013/11small/21/fd548da909d64a988da20fa0ec124ef3_1000x750.jpg"];


//设置圆角半径和模糊效果
LWImageStorage* is2 = [[LWImageStorage alloc] init];
is2.frame = CGRectMake(SCREEN_WIDTH/2 - 50.0f, is1.bottom + 10.0f, 100.0f, 100.0f);
is2.contents = [NSURL URLWithString:@"http://img.club.pchome.net/kdsarticle/2013/11small/21/fd548da909d64a988da20fa0ec124ef3_1000x750.jpg"];
is2.cornerRadius = 50.0f;
is2.cornerBorderWidth = 10.0f;
is2.cornerBorderColor = [UIColor orangeColor];
is2.isBlur = YES;

```

4.使用Gallop来进行HTML解析

```objc
//创建LWHTMLDisplayView
LWHTMLDisplayView* htmlView = [[LWHTMLDisplayView alloc] initWithFrame:self.view.bounds];
htmlView.parentVC = self;
htmlView.displayDelegate = self;
[self.view addSubview:htmlView];

// 获取LWStorageBuilder
LWStorageBuilder* builder = htmlView.storageBuilder;

// 创建LWLayout
LWLayout* layout = [[LWLayout alloc] init];

//创建LWHTMLTextConfig
LWHTMLTextConfig* contentConfig = [[LWHTMLTextConfig alloc] init];
contentConfig.font = [UIFont fontWithName:@"Heiti SC" size:15.0f];
contentConfig.textColor = RGB(50, 50, 50, 1);
contentConfig.linkColor = RGB(232, 104, 96,1.0f);
contentConfig.linkHighlightColor = RGB(0, 0, 0, 0.35f);


//通过XPath解析HTML并生成LWStorage,标签名对应的LWHTMLTextConfig以字典的Key-Value格式传入最后一个参数
[builder createLWStorageWithXPath:@"//div[@class='content']/p"
edgeInsets:UIEdgeInsetsMake([layout suggestHeightWithBottomMargin:10.0f], 10.0f, 10.0, 10.0f)
configDictionary:@{@"p":contentConfig,
@"strong":strongConfig,
@"em":strongConfig}];

//获取生成的LWStorage实例数组
NSArray* storages = builder.storages;

//添加到LWLayout实例
[layout addStorages:storages];

//给LWHTMLDisplayView对象并赋值
htmlView.layout = layout;

```


* **更加详细的内容，请看各个头文件和Demo，有详细的注释**

## License

* Gallop is available under the MIT license. See the LICENSE file for more info.


*** 



# Gallop v0.6.1



## About 


Gallop is a powerful rich text framework that support Asynchronous display.
It encapsulates CoreText's rich text functions and commonly used image processing capabilities.
just need use LWTextStorage object instead of UILabel object and use LWImageStorage object instead of UIImageView object,Gallop will make sure your app scroll smoothly.
You can also use Gallop to parse HTML pages and customize machining to parse HTML pages into iOS native pages.




## Architecture

![](https://github.com/waynezxcv/Gallop/raw/master/pics/architecture.png)


## Features

* use Gallop Building complex rich text interface application, can get a great experience.
* you can easy to insert local images,web images or UIView object in the text.
* easily add click and long press events to the text.
* easily draw text frame bouding, drawing the hollow words, sets the text vertical Alignment property, etc
* easy to parse the expression in the text, like http(s) link, @ user, # theme #, phone number.
* fast to sets the image corner radius property and blur processing, can be processed directly after the image to provide a cache, without repeated processing, improve performance.
* support GIF.


## Requirements

* iOS 8.0 and later


## Dependency

* web image download and cache depends on [SDWebImage](https://github.com/rs/SDWebImage) 'SDWebImage', '~> 4.0'.
* HTML parsing depends on the libxml2 library.

## Installation

1. Add the libxml2.tbd library to XCode's Build Phases-> Link Binary With Libraries.
2. Add '/ usr / include / libxml2' to XCode's Build Setting-> Header Search Paths.
3. Install [SDWebImage](https://github.com/rs/SDWebImage).
4. Add the .h and .m files under the Gallop folder to your project.
5. #import "Gallop.h".



## Modifications

v0.6.0

* depend on SDWebImage 4.0
* fixed bugs.

v0.5.1

* Use "- (id)initWithCallbackQueue:(dispatch_queue_t)callbackQueue" istead of "- (LWTransaction *)initWithCallbackQueue:(dispatch_queue_t)callbackQueue".
* Use "+ (id)mainTransactionGroup;" instead of "+ (LWTransactionGroup *)mainTransactionGroup;".
* modifies the return value type ,because it's conflict with FMDB


v0.3.7
* Fixed an issue where the LWImageStorage object "contentMode" property setting was invalid.


v0.3.6

* You can set the number of lines of text by property "maxNumberOfLines" of LWTextStorage object.
* You can use the LWTextStorage property "VericalAlignment" to set the text vertical alignment.


v0.3.5
* LWImageStorage add blur property.


v0.3.4
* Support cache layout model by CoreData.


v0.3.3
* Changed the integration, to solve the problem with the SDWebImage part of the file conflict.

v0.3.2
* Now, set the corner radius of the web image will be an additional cache, to solve the problem of excessive memory consumption.

v0.3.1
* Parsing HTML rendering to generate native iOS webpage, the image can be adjusted according to the original image height.

v0.3.0 
* Added the ability to parse HTML rendering to generate native iOS pages.

v0.2.5
* Optimized for image loading.

v0.2.4
* add classed TransactionGroup，LWTransaction，CALayer+LWTransaction.

v0.2.2 
* Now, LWAsyncDisplayView internal will automatically maintain a reuse pool, you can set  LWStorage a Identifier property,To reuse the internal associated UIView object.
* Fixed a bug that caused the link to overlap with the text.


## API Quickstart

1.Use LWTextStorage object to insert a image in the text and add a click event.


```objc

//create a LWAsyncDisplayView object
LWAsyncDisplayView* view = [[LWAsyncDisplayView alloc] initWithFrame:CGRectMake(0.0f,64.0,SCREEN_WIDTH,SCREEN_HEIGHT - 64.0f)];
view.delegate = self;
[self.view addSubview:view];

//create a LWTextStorage object
LWTextStorage* ts = [[LWTextStorage alloc] init];
ts.frame = CGRectMake(20, 50.0f,SCREEN_WIDTH - 40.0f, ts.suggestSize.height);
ts.text = @"Gallop支持图文混排,可以在文字中插入本地图片→和网络图片→UIView的子类→.给指定位置文字添加链接.快来试试吧。";
ts.font = [UIFont fontWithName:@"Heiti SC" size:16.0f];
UIImage* image = [UIImage imageNamed:@"pic.jpeg"];
UISwitch* switchView = [[UISwitch alloc] initWithFrame:CGRectMake(0, 0, 60, 30)];

//insert a local image in the text
[ts lw_replaceTextWithImage:image
contentMode:UIViewContentModeScaleAspectFill
imageSize:CGSizeMake(40, 40)
alignment:LWTextAttachAlignmentTop
range:NSMakeRange(26, 0)];

//insert a web image in the text
[ts lw_replaceTextWithImageURL:[NSURL URLWithString:@"http://joymepic.joyme.com/article/uploads/20163/81460101559518330.jpeg?imageView2/1"]
contentMode:UIViewContentModeScaleAspectFill
imageSize:CGSizeMake(80, 40)
alignment:LWTextAttachAlignmentTop
range:NSMakeRange(33, 0)];


//inser a UIView object in the text
[ts lw_replaceTextWithView:switchView
contentMode:UIViewContentModeScaleAspectFill
size:switchView.frame.size
alignment:LWTextAttachAlignmentTop
range:NSMakeRange(44, 0)];

//add a click event in the text
[ts lw_addLinkWithData:@"链接 ：）"
range:NSMakeRange(53,4)
linkColor:[UIColor blueColor]
highLightColor:RGB(0, 0, 0, 0.15)];

[ts lw_addLinkForWholeTextStorageWithData:@"整段文字"
highLightColor:RGB(0, 0, 0, 0.15)];

//add a long press event in the text

[ts lw_addLongPressActionWithData:@"longPress"
highLightColor:RGB(0, 0, 0, 0.25f)];


//create a LWLayout object
LWLayout* layout = [[LWLayout alloc] init];
[layout addStorage:ts];

//set LWLayout object to the LWAsyncDisplayView object
view.layout = layout;



//click and long press event call back
- (void)lwAsyncDisplayView:(LWAsyncDisplayView *)asyncDisplayView
didCilickedTextStorage:(LWTextStorage *)textStorage
linkdata:(id)data {
if ([data isKindOfClass:[NSString class]]) {
[LWAlertView shoWithMessage:data];
}
}


```


2.More usage about LWTextStorage

```objc

//create a LWTextStorage object with NSMutableAttributedString object

NSMutableAttributedString* as1 = [[NSMutableAttributedString alloc] initWithString:@"世界对着它的爱人，把它浩翰的面具揭下了。它变小了，小如一首歌，小如一回永恒的接吻。"];
[as1 setTextBackgroundColor:[UIColor orangeColor] range:NSMakeRange(0, 9)];
[as1 setTextColor:[UIColor whiteColor] range:NSMakeRange(0, 9)];
[as1 setTextColor:[UIColor blackColor] range:NSMakeRange(9, as1.length - 9)];
[as1 setUnderlineStyle:NSUnderlineStyleDouble underlineColor:[UIColor blueColor]
range:NSMakeRange(9, as1.length - 9)];

LWTextStorage* ts1 = [LWTextStorage lw_textStrageWithText:as1
frame:CGRectMake(20.0f,
ts.bottom + 20.0f,
SCREEN_WIDTH - 40.0f,
CGFLOAT_MAX)];
ts1.font = [UIFont fontWithName:@"Heiti SC" size:18.0f];
ts1.linespacing = 3.0f;


LWTextStorage* ts2 = [[LWTextStorage alloc] initWithFrame:CGRectMake(20.0f,
ts1.bottom + 20.0f,
SCREEN_WIDTH - 40.0f,
CGFLOAT_MAX)];
ts2.text = @"The world puts off its mask of vastness to its lover.It becomes small as one song, as one kiss of the eternal.";
ts2.textDrawMode = LWTextDrawModeStroke;
ts2.strokeColor = [UIColor redColor];
ts2.font = [UIFont fontWithName:@"Heiti SC" size:18.0f];
ts2.linespacing = 5.0f;
ts2.textBoundingStrokeColor = [UIColor grayColor];


//append a LWTextStorage object after another one

LWTextStorage* ts3 = [[LWTextStorage alloc] init];
ts3.text = @"^_^ 我是那个尾巴~";
ts3.textColor = [UIColor redColor];
ts3.font = [UIFont systemFontOfSize:20];
[ts2 lw_appendTextStorage:ts3];


UIImage* image = [UIImage imageNamed:@"pic.jpeg"];
NSMutableAttributedString* as2 = [NSMutableAttributedString
lw_textAttachmentStringWithContent:image
contentMode:UIViewContentModeScaleAspectFill
ascent:50.0f
descent:0.0f
width:50.0f];
LWTextStorage* ts4 = [LWTextStorage lw_textStrageWithText:as2 frame:CGRectZero];
[ts2 lw_appendTextStorage:ts4];


```

3.Usage of LWImageStorage

```objc

//down load web image with LWImageStorage object

LWImageStorage* is1 = [[LWImageStorage alloc] init];
is1.frame = CGRectMake(SCREEN_WIDTH/2 - 50.0f, ts.bottom + 10.0f, 100.0f, 100.0f);
is1.clipsToBounds = YES;
is1.contents = [NSURL URLWithString:@"http://img.club.pchome.net/kdsarticle/2013/11small/21/fd548da909d64a988da20fa0ec124ef3_1000x750.jpg"];


//set corner radius and blur property

LWImageStorage* is2 = [[LWImageStorage alloc] init];
is2.frame = CGRectMake(SCREEN_WIDTH/2 - 50.0f, is1.bottom + 10.0f, 100.0f, 100.0f);
is2.contents = [NSURL URLWithString:@"http://img.club.pchome.net/kdsarticle/2013/11small/21/fd548da909d64a988da20fa0ec124ef3_1000x750.jpg"];
is2.cornerRadius = 50.0f;
is2.cornerBorderWidth = 10.0f;
is2.cornerBorderColor = [UIColor orangeColor];
is2.isBlur = YES;

```

4.Use Gallop to parsing HTML

```objc

//create a LWHTMLDisplayView object

LWHTMLDisplayView* htmlView = [[LWHTMLDisplayView alloc] initWithFrame:self.view.bounds];
htmlView.parentVC = self;
htmlView.displayDelegate = self;
[self.view addSubview:htmlView];

// get LWStorageBuilder object
LWStorageBuilder* builder = htmlView.storageBuilder;

// create a LWLayout object
LWLayout* layout = [[LWLayout alloc] init];

//create a LWHTMLTextConfig object
LWHTMLTextConfig* contentConfig = [[LWHTMLTextConfig alloc] init];
contentConfig.font = [UIFont fontWithName:@"Heiti SC" size:15.0f];
contentConfig.textColor = RGB(50, 50, 50, 1);
contentConfig.linkColor = RGB(232, 104, 96,1.0f);
contentConfig.linkHighlightColor = RGB(0, 0, 0, 0.35f);


//create LWStorage object with XPath

[builder createLWStorageWithXPath:@"//div[@class='content']/p"
edgeInsets:UIEdgeInsetsMake([layout suggestHeightWithBottomMargin:10.0f], 10.0f, 10.0, 10.0f)
configDictionary:@{@"p":contentConfig,
@"strong":strongConfig,
@"em":strongConfig}];

NSArray* storages = builder.storages;
[layout addStorages:storages];

htmlView.layout = layout;


```



## License

* Gallop is available under the MIT license. See the LICENSE file for more info.





*** 





