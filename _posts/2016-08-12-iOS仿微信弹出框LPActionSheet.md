---
layout:     post
title:      iOS仿微信弹出框LPActionSheet.md
excerpt:    ""
date:       2016-08-12 22:00:00
author:     "Liu Peng"
header-img: "assets/iOS常用工具/iOS仿微信弹出框LPActionSheet-bg.jpg"
tags:
    - iOS常用工具
---

`LPActionSheet`是一款轻量级的仿微信弹出框

### 安装

##### 从CocoaPods安装

CocoaPods可以优雅地管理第三方库(具体使用方式我在另一篇文章有详细介绍,这里不再赘述)

- 第一步: 在Podfile文件中写入如下代码

```objc
pod 'LPActionSheet', '~> 1.0'
```

- 第二步: 将文件安装进项目中

```objc
pod install
```

##### 从Github上手动添加

- 第一步: 到 [这里](https://github.com/wenxiangjiang/LPActionSheet) 下载代码文件,将LPActionSheet/LPActionSheet文件夹拖到项目中
- 第二步: 将头文件加入项目中 #import "LPActionSheet.h"

### 如何使用LPActionSheet

你可以像这样实例化一个对象 (该方法为指定初始化器,其他初始化方法都会调用该方法,NS_DESIGNATED_INITIALIZER)
```objc
- (instancetype)initWithTitle:(NSString *)title
            cancelButtonTitle:(NSString *)cancelButtonTitle
       destructiveButtonTitle:(NSString *)destructiveButtonTitle
            otherButtonTitles:(NSArray *)otherButtonTitles
                      handler:(LPActionSheetBlock)actionSheetBlock NS_DESIGNATED_INITIALIZER;
```

你可以像这样快速返回一个实例化对象

```objc
+ (instancetype)actionSheetWithTitle:(NSString *)title
                   cancelButtonTitle:(NSString *)cancelButtonTitle
              destructiveButtonTitle:(NSString *)destructiveButtonTitle
                   otherButtonTitles:(NSArray *)otherButtonTitles
                             handler:(LPActionSheetBlock)actionSheetBlock;
```

使用如下方法弹出该视图

```objc
- (void)show;
```

终极解决方案 (推荐)

```objc
+ (void)showActionSheetWithTitle:(NSString *)title
               cancelButtonTitle:(NSString *)cancelButtonTitle
          destructiveButtonTitle:(NSString *)destructiveButtonTitle
               otherButtonTitles:(NSArray *)otherButtonTitles
                         handler:(LPActionSheetBlock)actionSheetBlock;
```

### Demo

```objc
[LPActionSheet showActionSheetWithTitle:@"This is a title, you can show some prompt here"
                      cancelButtonTitle:@"Cancel"
                 destructiveButtonTitle:@"Destructive"
                      otherButtonTitles:@[@"First choice", @"Second choice", @"Third choice"]
                                handler:^(LPActionSheet *actionSheet, NSInteger index) {
        NSLog(@"%ld", index);
}];
```

竖屏

![shuping]({{ site.baseurl }}/assets/iOS常用工具/LPActionSheet.png)

横屏

![hengping]({{ site.baseurl }}/assets/iOS常用工具/LPActionSheet_Landscape.png)

### 后续更新

该框架后续会在Github上维护更新,欢迎关注

https://github.com/wenxiangjiang/LPActionSheet