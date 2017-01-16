---
layout:     post
title:      iOS形变之CGAffineTransform
excerpt:    ""
date:       2016-04-11 18:00:00
author:     "Liu Peng"
header-img: "assets/iOS动画技术/iOS形变之CGAffineTransform-bg.jpg"
tags:
    - iOS动画技术
---

![CGAffineTransform]({{ site.baseurl }}/assets/iOS动画技术/CGAffineTransform.png)

### CGAffineTransform介绍
##### 概述
CGAffineTransform是一个用于处理形变的类,其可以改变控件的平移、缩放、旋转等,其坐标系统采用的是二维坐标系,即向右为x轴正方向,向下为y轴正方向

在UIView中有一个transform属性便是专门用来控制形变的,其使用方法如下

##### 样例素材
在介绍UIView形变的过程中,我们会使用一个UIImageView图片为例,对各动画效果进行演示

```objc
@property (nonatomic, strong) UIImageView *demoImageView;

- (void)viewDidLoad
{
    [super viewDidLoad];
    
    self.demoImageView = [[UIImageView alloc] initWithFrame:CGRectMake(20, 20, [[UIScreen mainScreen] bounds].size.width-40, [[UIScreen mainScreen] bounds].size.height-40)];
    self.demoImageView.image = [UIImage imageNamed:@"demo"];
    [self.view addSubview:self.demoImageView];
}
```

##### 方法介绍

- CGAffineTransformMakeTranslation实现以初始位置为基准,在x轴方向上平移x单位,在y轴方向上平移y单位

```objc
// 格式
CGAffineTransformMakeTranslation(CGFloat tx, CGFloat ty)
// 样例
self.demoImageView.transform = CGAffineTransformMakeTranslation(100, 100);
```

> 注: 当tx为正值时,会向x轴正方向平移,反之,则向x轴负方向平移;当ty为正值时,会向y轴正方向平移,反之,则向y轴负方向平移

- CGAffineTransformMakeScale实现以初始位置为基准,在x轴方向上缩放x倍,在y轴方向上缩放y倍

```objc
// 格式
CGAffineTransformMakeScale(CGFloat sx, CGFloat sy)
// 样例
self.demoImageView.transform = CGAffineTransformMakeScale(2, 0.5);
```

> 注: 当sx为正值时,会在x轴方向上缩放x倍,反之,则在缩放的基础上沿着竖直线翻转;当sy为正值时,会在y轴方向上缩放y倍,反之,则在缩放的基础上沿着水平线翻转

- CGAffineTransformMakeRotation实现以初始位置为基准,将坐标系统`逆`时针旋转angle弧度(弧度=π/180×角度,M_PI弧度代表180角度)

```objc
// 格式
CGAffineTransformMakeRotation(CGFloat angle)
// 样例
self.demoImageView.transform = CGAffineTransformMakeRotation(M_PI*0.5);
```

> 注1: 当angle为正值时,`逆`时针旋转坐标系统,反之`顺`时针旋转坐标系统

> 注2: `逆`时针旋转坐标系统的表现形式为对控件进行`顺`时针旋转

- CGAffineTransformTranslate实现以一个已经存在的形变为基准,在x轴方向上平移x单位,在y轴方向上平移y单位

```objc
// 格式
CGAffineTransformTranslate(CGAffineTransform t, CGFloat tx, CGFloat ty)
// 样例
CGAffineTransform transform = CGAffineTransformMakeTranslation(50, 50);
self.demoImageView.transform = CGAffineTransformTranslate(transform, 50, 50);
```

- CGAffineTransformScale实现以一个已经存在的形变为基准,在x轴方向上缩放x倍,在y轴方向上缩放y倍

```objc
// 格式
CGAffineTransformScale(CGAffineTransform t, CGFloat sx, CGFloat sy)
// 样例
CGAffineTransform transform = CGAffineTransformMakeScale(2, 0.5);
self.demoImageView.transform = CGAffineTransformScale(transform, 2, 1);
```

- CGAffineTransformRotate实现以一个已经存在的形变为基准,将坐标系统`逆`时针旋转angle弧度(弧度=π/180×角度,M_PI弧度代表180角度)

```objc
// 格式
CGAffineTransformRotate(CGAffineTransform t, CGFloat angle)
// 样例
CGAffineTransform transform = CGAffineTransformMakeRotation(M_PI*0.25);
self.demoImageView.transform = CGAffineTransformRotate(transform, M_PI*0.25);
```

- 特殊地,transform属性默认值为CGAffineTransformIdentity,可以在形变之后设置该值以还原到最初状态

```objc
// 样例
self.demoImageView.transform = CGAffineTransformIdentity;
```

### CGAffineTransform原理
CGAffineTransform形变是通过"仿射变换矩阵"来控制的,其中平移是矩阵相加,旋转与缩放则是矩阵相乘,为了合并矩阵运算中的加法和乘法,引入了齐次坐标的概念,它提供了用矩阵运算把二维、三维甚至高维空间中的一个点集从一个坐标系变换到另一个坐标系的有效方法.CGAffineTransform形变就是把二维形变使用一个三维矩阵来表示,其中第三列总是(0,0,1),形变通过前两列来控制,系统提供了CGAffineTransformMake结构体来控制形变

```objc
// 格式
CGAffineTransformMake(CGFloat a, CGFloat b, CGFloat c, CGFloat d, CGFloat tx, CGFloat ty)
```

该三维变换矩阵如下

![矩阵]({{ site.baseurl }}/assets/iOS动画技术/矩阵.png)

通过变换矩阵左乘向量,将空间中的一个点集从一个坐标系变换到另一个坐标系中,计算方式如下

![计算]({{ site.baseurl }}/assets/iOS动画技术/计算.png)

![结果]({{ site.baseurl }}/assets/iOS动画技术/结果.png)

由此可知,其中tx用来控制在x轴方向上的平移,ty用来控制在y轴方向上的平移;a用来控制在x轴方向上的缩放,d用来控制在y轴方向上的缩放;abcd共同控制旋转

- 平移CGAffineTransformMakeTranslation原理

```objc
self.demoImageView.transform = CGAffineTransformMakeTranslation(100, 100);
self.demoImageView.transform = CGAffineTransformMake(1, 0, 0, 1, 100, 100);
```

- 缩放CGAffineTransformMakeScale原理

```objc
self.demoImageView.transform = CGAffineTransformMakeScale(2, 0.5);
self.demoImageView.transform = CGAffineTransformMake(2, 0, 0, 0.5, 0, 0);
```

- 旋转CGAffineTransformMakeRotation原理

```objc
self.demoImageView.transform = CGAffineTransformMakeRotation(M_PI*0.5);
self.demoImageView.transform = CGAffineTransformMake(cos(M_PI * 0.5), sin(M_PI * 0.5), -sin(M_PI * 0.5), cos(M_PI * 0.5), 0, 0);
```

- 初始状态CGAffineTransformIdentity原理

```objc
self.demoImageView.transform = CGAffineTransformIdentity;
self.demoImageView.transform = CGAffineTransformMake(1, 0, 0, 1, 0, 0);
```