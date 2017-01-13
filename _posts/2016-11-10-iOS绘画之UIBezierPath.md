---
layout:     post
title:      iOS绘画之UIBezierPath
excerpt:    UIBezierPath用于定义一个由直线/曲线组合而成的路径, 并且可以在自定义视图中渲染该路径.
date:       2016-11-10 17:00:00
author:     "Liu Peng"
header-img: "assets/iOS绘画技术/iOS绘画之UIBezierPath.jpg"
tags:
    - iOS绘画技术
---

### 概述

UIBezierPath用于定义一个由直线/曲线组合而成的路径, 并且可以在自定义视图中渲染该路径. 在使用的过程中, 我们只需要先指定好路径的结构, 比如一条直线、一条贝塞尔曲线、一个矩形、一个椭圆、一个圆弧等, 然后使用系统为我们提供的方法将构建好的路径渲染出来即可

UIBezierPath位于UIKit库中, 是针对Core Graphics库中的CGPathRef的封装, 其已经可以帮助我们实现一般需求, 下面让我们一起学习一下UIBezierPath的使用方法

> 注: 使用UIBezierPath绘画的代码写在自定义视图的drawRect:方法中

```objc
- (void)drawRect:(CGRect)rect
{
    // Drawing code
}
```

### 关于初始化

##### + (instancetype)bezierPath

初始化一个UIBezierPath对象

```objc
UIBezierPath *path = [UIBezierPath bezierPath];
```

##### + (instancetype)bezierPathWithRect:(CGRect)rect

以"矩形路径"初始化一个UIBezierPath对象

```objc
// 参数一: 矩形范围
UIBezierPath *path = [UIBezierPath bezierPathWithRect:CGRectMake(20, 20, 200, 100)];
```

##### + (instancetype)bezierPathWithOvalInRect:(CGRect)rect

以"内切于一个矩形的椭圆路径"初始化一个UIBezierPath对象

```objc
// 参数一: 矩形范围
UIBezierPath *path = [UIBezierPath bezierPathWithOvalInRect:CGRectMake(20, 20, 200, 100)];
```

##### + (instancetype)bezierPathWithRoundedRect:(CGRect)rect cornerRadius:(CGFloat)cornerRadius

以"圆角矩形路径"初始化一个UIBezierPath对象

```objc
// 参数一: 矩形范围 
// 参数二: 圆角半径, 如果半径大于矩形短边的一半, 则按照一半处理
UIBezierPath *path = [UIBezierPath bezierPathWithRoundedRect:CGRectMake(20, 20, 200, 100) cornerRadius:30];
```

##### + (instancetype)bezierPathWithRoundedRect:(CGRect)rect byRoundingCorners:(UIRectCorner)corners cornerRadii:(CGSize)cornerRadii

以"可选圆弧位置的圆角矩形路径"初始化一个UIBezierPath对象

```objc
// 参数一: 矩形范围
// 参数二: 圆弧位置, 可以多选
// 参数三: 圆弧半径, 如果半径大于矩形短边的一半, 则按照一半处理, CGSize以小的为准(自己试的, 文档没说)
UIBezierPath *path = [UIBezierPath bezierPathWithRoundedRect:CGRectMake(20, 20, 200, 100) byRoundingCorners:UIRectCornerAllCorners cornerRadii:CGSizeMake(30, 50)];
```

##### + (instancetype)bezierPathWithArcCenter:(CGPoint)center radius:(CGFloat)radius startAngle:(CGFloat)startAngle endAngle:(CGFloat)endAngle clockwise:(BOOL)clockwise

以"圆弧路径"初始化一个UIBezierPath对象

```objc
// 参数一: 圆弧圆心
// 参数二: 圆弧半径
// 参数三: 开始弧度
// 参数四: 结束弧度
// 参数五: 是否为顺时针
UIBezierPath *path = [UIBezierPath bezierPathWithArcCenter:CGPointMake(100, 100) radius:50 startAngle:0 endAngle:M_PI clockwise:YES];
```

![坐标系统.jpeg]({{ site.url }}/assets/iOS绘画技术/坐标系统.jpeg)

### 关于构造一个路径

##### - (void)moveToPoint:(CGPoint)point

将当前点移动到指定的位置

```objc
UIBezierPath *path = [UIBezierPath bezierPath];
// 参数一: 指定的位置
[path moveToPoint:CGPointMake(20, 20)];
```

##### - (void)addLineToPoint:(CGPoint)point

在路径中增加一条直线

```objc
UIBezierPath *path = [UIBezierPath bezierPath];
[path moveToPoint:CGPointMake(20, 20)];
// 参数一: 直线的终点位置
[path addLineToPoint:CGPointMake(100, 100)];
```

##### - (void)addArcWithCenter:(CGPoint)center radius:(CGFloat)radius startAngle:(CGFloat)startAngle endAngle:(CGFloat)endAngle clockwise:(BOOL)clockwise

在路径中增加一条圆弧

```objc
UIBezierPath *path = [UIBezierPath bezierPath];
[path moveToPoint:CGPointMake(20, 20)];
[path addLineToPoint:CGPointMake(100, 20)];
// 参数一: 圆弧圆心
// 参数二: 圆弧半径
// 参数三: 开始弧度
// 参数四: 结束弧度
// 参数五: 是否为顺时针
[path addArcWithCenter:CGPointMake(60, 20) radius:40 startAngle:0 endAngle:M_PI clockwise:YES];
```

##### - (void)addQuadCurveToPoint:(CGPoint)endPoint controlPoint:(CGPoint)controlPoint

在路径中增加一条二次贝塞尔曲线

```objc
UIBezierPath *path = [UIBezierPath bezierPath];
[path moveToPoint:CGPointMake(20, 20)];
// 参数一: 曲线的终点位置
// 参数二: 控制点
[path addQuadCurveToPoint:CGPointMake(100, 20) controlPoint:CGPointMake(100, 60)];
```

![二次贝塞尔曲线.jpeg]({{ site.url }}/assets/iOS绘画技术/二次贝塞尔曲线.jpeg)

##### - (void)addCurveToPoint:(CGPoint)endPoint controlPoint1:(CGPoint)controlPoint1 controlPoint2:(CGPoint)controlPoint2

在路径中增加一条三次贝塞尔曲线

```objc
UIBezierPath *path = [UIBezierPath bezierPath];
[path moveToPoint:CGPointMake(20, 20)];
// 参数一: 曲线的终点位置
// 参数二: 第一控制点
// 参数三: 第二控制点
[path addCurveToPoint:CGPointMake(100, 20) controlPoint1:CGPointMake(30, 60) controlPoint2:CGPointMake(100, 100)];
```

![三次贝塞尔曲线.jpeg]({{ site.url }}/assets/iOS绘画技术/三次贝塞尔曲线.jpeg)

### 关于路径信息

##### @property(readonly,getter=isEmpty) BOOL empty

是否路径信息为空, 即使通过moveToPoint:移动到指定的位置也算不为空

```objc
UIBezierPath *path = [UIBezierPath bezierPath];
BOOL isEmpty = path.empty;
```

##### @property(nonatomic,readonly) CGRect bounds

可以封闭所有路径点的最小矩形范围, 包括多次贝塞尔曲线的控制点在内

```objc
UIBezierPath *path = [UIBezierPath bezierPath];
CGRect bounds = path.bounds;
```

##### @property(nonatomic,readonly) CGPoint currentPoint

路径当前所在点

```objc
UIBezierPath *path = [UIBezierPath bezierPath];
CGPoint currentPoint = path.currentPoint;
```

##### - (BOOL)containsPoint:(CGPoint)point

是否包含指定点

```objc
UIBezierPath *path = [UIBezierPath bezierPath];
BOOL isContainsPoint = [path containsPoint:CGPointMake(20, 30)];
```

### 关于路径操作

##### - (void)closePath

使用一条直线闭合路径的起点和终点, 该方法同时也会更新当前点到新直线的终点(即路径的起点)

```objc
UIBezierPath *path = [UIBezierPath bezierPath];
[path moveToPoint:CGPointMake(20, 20)];
[path addLineToPoint:CGPointMake(100, 100)];
[path addLineToPoint:CGPointMake(100, 200)];
[path closePath];
```

##### - (void)removeAllPoints

移除路径中所有的点

```objc
UIBezierPath *path = [UIBezierPath bezierPath];
[path removeAllPoints];
```

##### - (void)appendPath:(UIBezierPath *)bezierPath

路径中增加一个已有路径

```objc
UIBezierPath *path = [UIBezierPath bezierPath];
UIBezierPath *path2 = [UIBezierPath bezierPath];
[path appendPath:path2];
```

##### - (UIBezierPath *)bezierPathByReversingPath

返回一个翻转已有路径的新路径

```objc
UIBezierPath *path = [UIBezierPath bezierPath];
UIBezierPath *path2 = [path bezierPathByReversingPath];
```

### 关于绘图

##### - (void)fill

利用当前绘画属性填充路径封闭范围, 该方法在绘画之前会自动将开放子路径封闭, 填充部分不包含路径本身, 所以对于线宽较大的路径, 填充部分会跟部分路径重合

```objc
UIBezierPath *path = [UIBezierPath bezierPath];
// do something....
[path fill];
```

##### - (void)fillWithBlendMode:(CGBlendMode)blendMode alpha:(CGFloat)alpha

利用指定模式填充路径封闭范围, 该方法在绘画之前会自动将开放子路径封闭, 填充部分不包含路径本身, 所以对于线宽较大的路径, 填充部分会跟部分路径重合

```objc
UIBezierPath *path = [UIBezierPath bezierPath];
// do something....
[path fillWithBlendMode:kCGBlendModeNormal alpha:0.8];
```

##### - (void)stroke

利用当前绘画属性沿着路径画线

```objc
UIBezierPath *path = [UIBezierPath bezierPath];
// do something....
[path stroke];
```

##### - (void)strokeWithBlendMode:(CGBlendMode)blendMode alpha:(CGFloat)alpha

利用指定模式沿着路径画线

```objc
UIBezierPath *path = [UIBezierPath bezierPath];
// do something....
[path strokeWithBlendMode:kCGBlendModeNormal alpha:0.8];
```

### 关于绘画属性

##### @property(nonatomic) CGFloat lineWidth

路径的线宽

```objc
UIBezierPath *path = [UIBezierPath bezierPath];
path.lineWidth = 10;
```

##### - (void)setFill

路径的填充颜色

```objc
[[UIColor orangeColor] setFill];
```

##### - (void)setStroke

路径的画线颜色

```objc
[[UIColor orangeColor] setStroke];
```

##### @property(nonatomic) CGLineCap lineCapStyle

路径的终点形状, 该属性适用于开放路径的起点和终点, 默认为kCGLineCapButt(方形结束, 结束位置正好为精确位置), 其他可选项为kCGLineCapRound(圆形结束, 结束位置超过精确位置半个线宽)和kCGLineCapSquare(方形结束, 结束位置超过精确位置半个线宽)

```objc
UIBezierPath *path = [UIBezierPath bezierPath];
path.lineCapStyle = kCGLineCapButt;
```

##### @property(nonatomic) CGLineJoin lineJoinStyle

路径的连接点形状, 默认为kCGLineJoinMiter(全部连接), 其他可选项为kCGLineJoinRound(圆形连接)和kCGLineJoinBevel(斜角连接)

```objc
UIBezierPath *path = [UIBezierPath bezierPath];
path.lineJoinStyle = kCGLineJoinMiter;
```

### 关于二维形变

##### - (void)applyTransform:(CGAffineTransform)transform

对路径中的所有点进行二维形变, 该变化立即生效, 且为永久性改变所有点

```objc
UIBezierPath *path = [UIBezierPath bezierPath];
[path applyTransform:CGAffineTransformMakeTranslation(20, 20)];
```

### 示例

以"今日头条下拉刷新效果"为例, 练习一下绘画方式

```objc
CGAffineTransform transform = CGAffineTransformMakeTranslation((self.bounds.size.width-50*5)/2, 100);
transform = CGAffineTransformScale(transform, 5, 5);

UIBezierPath *path1 = [UIBezierPath bezierPathWithRoundedRect:CGRectMake(0, 0, 50, 50) cornerRadius:10];
[path1 applyTransform:transform];
[[UIColor colorWithRed:198/255.0f green:198/255.0f blue:198/255.0f alpha:1.0f] setStroke];
[path1 stroke];

UIBezierPath *path2 = [UIBezierPath bezierPathWithRect:CGRectMake(7, 10, 14, 12)];
[path2 applyTransform:transform];
[[UIColor colorWithRed:224/255.0f green:224/255.0f blue:224/255.0f alpha:1.0f] setFill];
[path2 fill];

UIBezierPath *path3 = [UIBezierPath bezierPathWithRect:CGRectMake(7, 10, 14, 12)];
[path3 applyTransform:transform];
[[UIColor colorWithRed:189/255.0f green:189/255.0f blue:189/255.0f alpha:1.0f] setStroke];
[path3 stroke];

UIBezierPath *path11 = [UIBezierPath bezierPath];
[path11 moveToPoint:CGPointMake(28, 10)];
[path11 addLineToPoint:CGPointMake(43, 10)];
[path11 applyTransform:transform];
[[UIColor colorWithRed:153/255.0f green:153/255.0f blue:153/255.0f alpha:1.0f] setStroke];
[path11 stroke];

UIBezierPath *path12 = [UIBezierPath bezierPath];
[path12 moveToPoint:CGPointMake(28, 16)];
[path12 addLineToPoint:CGPointMake(43, 16)];
[path12 applyTransform:transform];
[path12 stroke];

UIBezierPath *path13 = [UIBezierPath bezierPath];
[path13 moveToPoint:CGPointMake(28, 22)];
[path13 addLineToPoint:CGPointMake(43, 22)];
[path13 applyTransform:transform];
[path13 stroke];

UIBezierPath *path21 = [UIBezierPath bezierPath];
[path21 moveToPoint:CGPointMake(7, 28)];
[path21 addLineToPoint:CGPointMake(43, 28)];
[path21 applyTransform:transform];
[path21 stroke];

UIBezierPath *path22 = [UIBezierPath bezierPath];
[path22 moveToPoint:CGPointMake(7, 34)];
[path22 addLineToPoint:CGPointMake(43, 34)];
[path22 applyTransform:transform];
[path22 stroke];

UIBezierPath *path23 = [UIBezierPath bezierPath];
[path23 moveToPoint:CGPointMake(7, 40)];
[path23 addLineToPoint:CGPointMake(43, 40)];
[path23 applyTransform:transform];
[path23 stroke];
```

![今日头条示例.png]({{ site.url }}/assets/iOS绘画技术/今日头条示例.png)

