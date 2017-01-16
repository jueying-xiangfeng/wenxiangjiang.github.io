---
layout:     post
title:      iOS之UIView动画
excerpt:    ""
date:       2016-05-26 20:00:00
author:     "Liu Peng"
header-img: "assets/iOS动画技术/iOS之UIView动画-bg.jpg"
tags:
    - iOS动画技术
---

![UIView动画]({{ site.baseurl }}/assets/iOS动画技术/UIView动画.png)

### 概述
在AppStore中的应用越来越重视动画效果的使用,一个良好动画效果可以让两个状态之间平滑地过度,也可以利用动画吸引住用户的眼球,在UIView类中共有三个类目(Category)用于实现动画功能,分为UIViewAnimation、UIViewAnimationWithBlocks以及UIViewKeyframeAnimations,他们是Apple对核心动画(Core Animation)的封装,可以让我们不进行任何绘画等复杂操作的前提下实现大部分动画需求

##### UIView动画的用处
UIView类中提供的动画功能,允许我们实现如下动画需求

- 动态修改"可动画属性"
  - frame(位置及大小)
  - bounds(大小)
  - center(中心点位置)
  - transform(形变)
  - alpha(透明度)
  - backgroundColor(背景颜色)
  - contentStretch(可拉伸区域,从iOS6开始被废弃)
- 提供转场动画效果
  - 修改已存在视图的子视图(也可以修改自身)
  - 在视图层级中用一个视图替换另一个视图(Block动画提供)

##### 本文中样例素材

在介绍UIView动画的过程中,我们会使用一个UIImageView图片为例,对各动画效果进行演示

```objc
@property (nonatomic, strong) UIImageView *demoImageView;

- (void)viewDidLoad
{
    [super viewDidLoad];
    
    self.demoImageView = [[UIImageView alloc] initWithFrame:CGRectMake(20, 20, [[UIScreen mainScreen] bounds].size.width-40, [[UIScreen mainScreen] bounds].size.height-40)];
    self.demoImageView.image = [UIImage imageNamed:@"old"];
    [self.view addSubview:self.demoImageView];
}
```

### UIViewAnimation
Apple自iOS2开始提供了若干UIView的类方法来实现动画功能,Apple建议从iOS4系统开始尽量使用Block方式来实现动画功能,因此我们以后在开发中应该遵从Apple的建议使用Block方式来实现动画功能,但是学习本部分对未来学好Block动画有很好的铺垫作用,因此本文在这里会对系统提供的方法进行详尽地介绍

在iOS4之前,我们需要使用动画块(begin/commit animation block)来进行动画,一个完整的动画块由以下四部分组成

- 动画块开始标识
- 配置动画选择项
- 修改可动画属性
- 动画块结束标识

##### 动画块开始标识
标识动画块的开始,该方法是告诉系统将要执行一个或多个动画,如果通过setAnimationWillStartSelector:或者setAnimationDidStopSelector:方法设置了代理回调方法,便可以在回调方法中收到该方法中的两个参数animationID(动画标志)和context(上下文,一般为nil)

```objc
// 格式
+ (void)beginAnimations:(nullable NSString *)animationID context:(nullable void *)context;
// 样例
[UIView beginAnimations:@"demo" context:nil];
```

##### 动画块结束标识
标识动画块的结束,同时安排动画执行

```objc
// 格式
+ (void)commitAnimations;
// 样例
[UIView commitAnimations];
```

##### 设置动画代理对象及代理回调方法
设置动画代理对象,以便在动画开始和结束时收到系统回调

```objc
// 格式
+ (void)setAnimationDelegate:(nullable id)delegate; 
// 样例
[UIView setAnimationDelegate:self];
```

设置动画将要开始时调用代理对象的方法

```objc
// 格式
+ (void)setAnimationWillStartSelector:(nullable SEL)selector; 
// 样例
[UIView setAnimationWillStartSelector:@selector(animationWillStart:context:)];
```

> 注1: 当动画当前不可用时,该方法不会被调用
 
> 注2: 当不设置该方法时,系统默认会调用- (void)animationWillStart:(NSString *)animationID context:(void *)context

设置动画已经结束后调用代理对象的方法

```objc
// 格式
+ (void)setAnimationDidStopSelector:(nullable SEL)selector; 
// 样例
[UIView setAnimationDidStopSelector:@selector(animationDidStop:finished:context:)];
```

> 注1: 当动画当前不可用时,该方法依然会被调用

> 注2: 当不设置该方法时,系统默认会调用- (void)animationDidStop:(NSString *)animationID finished:(NSNumber *)finished context:(void *)context

##### 设置动画持续时长
设置动画持续时长,默认0.2s

```objc
// 格式
+ (void)setAnimationDuration:(NSTimeInterval)duration; 
// 样例
[UIView setAnimationDuration:0.2f];
```

> 注: 该方法必须在修改"可变化属性"之前调用

##### 设置延时动画时长
设置延时动画时长,默认0.0s

```objc
// 格式
+ (void)setAnimationDelay:(NSTimeInterval)delay; 
// 样例
[UIView setAnimationDelay:0.0f];
```

> 注: 该方法必须在修改"可变化属性"之前调用

##### 设置动画开始时间(该方法设置后整个动画即不生效,不明原因)
设置动画开始时间,默认当前时间

```objc
// 格式
+ (void)setAnimationStartDate:(NSDate *)startDate; 
// 样例
[UIView setAnimationStartDate:[NSDate date]];
```

> 注1: 该方法必须在修改"可变化属性"之前调用

> 注2: 不要在Block动画中调用该方法

> 注3: 该方法设置后整个动画即不生效,作者尝试过如下四种方式,结果都不生效

```objc
[UIView setAnimationStartDate:[NSDate date]];
[UIView setAnimationStartDate:[NSDate dateWithTimeIntervalSinceReferenceDate:CFAbsoluteTimeGetCurrent()]];

[UIView setAnimationStartDate:[NSDate dateWithTimeIntervalSinceNow:3]];
[UIView setAnimationStartDate:[NSDate dateWithTimeIntervalSinceReferenceDate:CFAbsoluteTimeGetCurrent()+3]];
```

##### 设置时间函数
时间函数即动画进行速度随着时间的变化曲线,系统采用一个枚举类型来保存

```objc
/**
 typedef NS_ENUM(NSInteger, UIViewAnimationCurve)
 {
     UIViewAnimationCurveEaseInOut // 默认,淡入淡出,先慢后快再慢
     UIViewAnimationCurveEaseIn // 淡入,先慢后快
     UIViewAnimationCurveEaseOut // 淡出,先快后慢
     UIViewAnimationCurveLinear // 线性
 };
 */

// 格式
+ (void)setAnimationCurve:(UIViewAnimationCurve)curve; 
// 样例
[UIView setAnimationCurve:UIViewAnimationCurveEaseInOut];
```

> 注: 该方法必须在修改"可变化属性"之前调用

##### 设置动画重复次数
设置动画重复次数,重复次数可以是小数,小数部分会做相应比例的动画,然后直接变化到动画结束位置

```objc
// 格式
+ (void)setAnimationRepeatCount:(float)repeatCount; 
// 样例
[UIView setAnimationRepeatCount:2.5f];
```

> 注: 该方法必须在修改"可变化属性"之前调用

##### 设置是否反向动画
设置是否反向动画,默认为NO,如果设置为YES,动画向前做完会做一次反向动画,那么一正一反算作一次repeatCount,动画所用时长会加倍

```objc
// 格式
+ (void)setAnimationRepeatAutoreverses:(BOOL)repeatAutoreverses; 
// 样例
[UIView setAnimationRepeatAutoreverses:YES];
```

> 注1: 该方法必须在修改"可变化属性"之前调用

> 注2: 因为当动画结束后一定会定格在动画块中设置的新位置上,为了不在最后产生一次不和谐的跳动,可以将repeatCount设置成0.5次即可

##### 设置是否从当前状态开始动画
该方法用于处理当旧动画正在运行时开始一个新的动画,是否从旧动画的当前状态开始新动画,默认为NO

- 当设置为NO时,旧动画的结束状态会被作为新动画的初始状态,且旧动画会在新动画开始的时候立刻结束动画
- 当设置为YES时,旧动画的当前状态会被作为新动画的初始状态

```objc
// 格式
+ (void)setAnimationBeginsFromCurrentState:(BOOL)fromCurrentState;
// 样例
[UIView setAnimationBeginsFromCurrentState:NO];
```

##### 设置转场效果
设置转场效果,该方法有如下三个参数

- 参数1: 转场动画方向,系统采用一个枚举类型来保存
- 参数2: 参与转场动画的视图(转场动画效果发生在该视图上)
- 参数3: 如果设置为YES,则视图在开始和结束时分别渲染一次,在动画过程中不可以更新视图(这样性能好),如果设置为NO,则视图每一帧都会渲染,在动画过程中可以实时更新视图(这样能力强)

```objc
/**
 typedef NS_ENUM(NSInteger, UIViewAnimationTransition)
 {
     UIViewAnimationTransitionNone,
     UIViewAnimationTransitionFlipFromLeft,
     UIViewAnimationTransitionFlipFromRight,
     UIViewAnimationTransitionCurlUp,
     UIViewAnimationTransitionCurlDown
 };
 */

// 格式
+ (void)setAnimationTransition:(UIViewAnimationTransition)transition forView:(UIView *)view cache:(BOOL)cache;
// 样例
[UIView setAnimationTransition:UIViewAnimationTransitionFlipFromLeft forView:self.view cache:NO];
```

> 注: 该方法每个动画块中只可调用一次

##### 设置是否动画可用
设置是否动画可用,默认为YES,如果设置为NO,则没有动画效果,属性变化立即生效

```objc
// 格式
+ (void)setAnimationsEnabled:(BOOL)enabled;
// 样例
[UIView setAnimationsEnabled:YES];
```

> 注: 该设置只影响在这句代码之后设置的属性变化

返回一个BOOL值标识是否动画可用

```objc
// 格式
+ (BOOL)areAnimationsEnabled;
// 样例
[UIView areAnimationsEnabled];
```

Apple在iOS7中新增加了一个简单的封装,该方法会先检查动画当前是否为可用状态,然后设置动画为不可用状态,执行Block中的代码,最后重新恢复动画为原来状态

```objc
// 格式
+ (void)performWithoutAnimation:(void (^)(void))actionsWithoutAnimation;
// 样例
[UIView performWithoutAnimation:^{
    self.demoImageView.alpha = 0.4;
}];
```

##### 动画块的嵌套
动画块的嵌套使用,是指在commit提交前一个动画之前再次调用begin方法创建一个新的动画,该新动画可以根据需求指定与外层动画不同的"动画持续时长""时间函数"等动画选择项,当调用commit提交一个动画时,如果当前动画块是在最外层,该方法会开始做动画,如果当前动画块嵌套在另一个动画块的里面,该方法会等待外层的动画块提交,直到最外层动画块提交之后一起做动画,动画是在单独的线程运行,这样多个动画便可以一个一个地执行起来

```objc
// 以"嵌套动画的外层历时5s将视图透明度修改为0.2,嵌套动画的内层不继承外层动画的持续时长,将视图大小缩小一半,期间重复2.5次"为例,代码如下
[UIView beginAnimations:@"parent" context:nil];
[UIView setAnimationDuration:5.0f];
self.demoImageView.alpha = 0.2f;

[UIView beginAnimations:@"demo2" context:nil];
[UIView setAnimationDuration:1.0f];
[UIView setAnimationRepeatCount:2.5f];
[UIView setAnimationRepeatAutoreverses:YES];
self.demoImageView.transform = CGAffineTransformMakeScale(0.5, 0.5);
[UIView commitAnimations];

[UIView commitAnimations];
```

![嵌套动画]({{ site.baseurl }}/assets/iOS动画技术/嵌套动画.gif)

##### 两个小例子
例子一

```objc
// 以"将视图大小缩小一半,并做反向动画,最后平滑地停留在预定位置"为例,代码如下
[UIView beginAnimations:@"demo" context:nil];
[UIView setAnimationDuration:1.0f];
[UIView setAnimationRepeatCount:2.5];
[UIView setAnimationRepeatAutoreverses:YES];
self.demoImageView.transform = CGAffineTransformMakeScale(0.5, 0.5);
[UIView commitAnimations];
```

![demo1]({{ site.baseurl }}/assets/iOS动画技术/demo1.gif)

例子二

```objc
// 以"从左侧翻转转场动画,且将视图大小缩小一半(动画效果发生在该视图上)"为例,代码如下
[UIView beginAnimations:@"demo" context:nil];
[UIView setAnimationDuration:1.0f];
[UIView setAnimationTransition:UIViewAnimationTransitionFlipFromLeft forView:self.demoImageView cache:YES];
self.demoImageView.transform = CGAffineTransformMakeScale(0.5, 0.5);
[UIView commitAnimations];
```

![demo2]({{ site.baseurl }}/assets/iOS动画技术/demo2.gif)

### UIViewAnimationWithBlocks(Block动画)
Apple从iOS4开始加入了Block功能,UIView的普通动画方法全面增加了对Block的支持,自此我们便可以非常方便地使用动画功能,推荐使用

##### 普通属性动画

```objc
/**
 * 属性动画
 *
 * @param duration   动画持续时长
 * @param delay      延时动画时长
 * @param options    动画选择项
 * @param animations 动画Block
 * @param completion 动画执行完执行的Block
 *
 * @return 无返回值
 */
+ (void)animateWithDuration:(NSTimeInterval)duration delay:(NSTimeInterval)delay options:(UIViewAnimationOptions)options animations:(void (^)(void))animations completion:(void (^ __nullable)(BOOL finished))completion;

// 以"将视图大小缩小一半,动画结束后恢复"为例,代码如下
[UIView animateWithDuration:3.0f delay:0.0f options:UIViewAnimationOptionCurveEaseInOut animations:^{
    self.demoImageView.transform = CGAffineTransformMakeScale(0.5, 0.5);
} completion:^(BOOL finished) {
    if (finished)
    {
        self.demoView.transform = CGAffineTransformIdentity;
    }
}];
```

上述Block动画共有5个参数,使用起来需要设置的内容比较多,系统为我们提供了两个相对简单的Block动画以用于简单动画

```objc
// delay = 0.0,options = 0的属性动画
+ (void)animateWithDuration:(NSTimeInterval)duration animations:(void (^)(void))animations completion:(void (^ __nullable)(BOOL finished))completion;

// delay = 0.0,options = 0,completion = NULL的属性动画
+ (void)animateWithDuration:(NSTimeInterval)duration animations:(void (^)(void))animations;
```

![普通动画]({{ site.baseurl }}/assets/iOS动画技术/普通动画.gif)

##### 弹性属性动画
Apple从iOS7开始在系统中大量使用SpringAnimation,该动画由于前期速度很快,可以给用户一种干净利落的感觉,所以推荐大家使用以与系统动画接轨

```objc
/**
 * SpringAnimation(弹性动画)
 *
 * @param duration     动画持续时长
 * @param delay        延时动画时长
 * @param dampingRatio 当dampingRatio设置为1时,动画会毫无震荡感地平稳地运动到最终状态,如果dampingRatio的值少于1,值越小在最终状态上震荡效果会越明显
 * @param velocity     初始速度,数值越大一开始移动速度越快(注: 初始速度取值较高而时间取值较短时,也会产生震荡效果)
 * @param options      动画选择项
 * @param animations   动画Block
 * @param completion   动画执行完执行的Block
 *
 * @return 无返回值
 */
+ (void)animateWithDuration:(NSTimeInterval)duration delay:(NSTimeInterval)delay usingSpringWithDamping:(CGFloat)dampingRatio initialSpringVelocity:(CGFloat)velocity options:(UIViewAnimationOptions)options animations:(void (^)(void))animations completion:(void (^ __nullable)(BOOL finished))completion;

// 以"将视图大小缩小一半,动画结束后恢复"为例,代码如下
[UIView animateWithDuration:3.0f delay:0.0f usingSpringWithDamping:0.7f initialSpringVelocity:5.0f options:UIViewAnimationOptionCurveEaseInOut animations:^{
    self.demoImageView.transform = CGAffineTransformMakeScale(0.5, 0.5);
} completion:^(BOOL finished) {
    if (finished)
    {
        self.demoView.transform = CGAffineTransformIdentity;
    }
}];
```

![弹性动画]({{ site.baseurl }}/assets/iOS动画技术/弹性动画.gif)

##### 执行系统动画
Apple从iOS7开始加入了一个方法,允许我们执行系统动画,且可以并行地执行附加动画

```objc
/**
 * 在一个或多个视图上执行系统动画
 *
 * @param animation          系统动画,暂时只有UISystemAnimationDelete一个
 * @param views              执行动画的views数组
 * @param options            动画选择项
 * @param parallelAnimations 并行附加动画Block
 * @param completion         动画执行完执行的Block
 *
 * @return  无返回值
 */
+ (void)performSystemAnimation:(UISystemAnimation)animation onViews:(NSArray<__kindof UIView *> *)views options:(UIViewAnimationOptions)options animations:(void (^ __nullable)(void))parallelAnimations completion:(void (^ __nullable)(BOOL finished))completion;

// 以"执行系统删除动画"为例,代码如下
[UIView performSystemAnimation:UISystemAnimationDelete onViews:@[self.demoImageView] options:UIViewAnimationOptionCurveEaseInOut animations:^{
    // do something....
} completion:^(BOOL finished) {
    if (finished)
    {
        // do something....
    }
}];
```

> 注: 附加动画不要修改正在被系统动画修改的属性

![系统动画]({{ site.baseurl }}/assets/iOS动画技术/系统动画.gif)

##### 转场动画
Apple为我们提供了两个方法用于进行转场动画,为我们解决两个动画需求

- 修改已存在视图的子视图(也可以修改自身)

```objc
/**
 * TransitionAnimation(转场动画)
 *
 * @param view       参与转场动画的视图(转场动画效果发生在该视图上)
 * @param duration   动画持续时长
 * @param options    动画选择项
 * @param animations 动画Block
 * @param completion 动画执行完执行的Block
 *
 * @return  无返回值
 */
+ (void)transitionWithView:(UIView *)view duration:(NSTimeInterval)duration options:(UIViewAnimationOptions)options animations:(void (^ __nullable)(void))animations completion:(void (^ __nullable)(BOOL finished))completion;

// 以"父视图从左侧翻转转场动画,且将子视图大小缩小一半,动画结束后恢复"为例,代码如下
[UIView transitionWithView:self.view duration:3.0f options:UIViewAnimationOptionTransitionFlipFromLeft | UIViewAnimationOptionAllowAnimatedContent animations:^{
    self.demoImageView.transform = CGAffineTransformMakeScale(0.5, 0.5);
} completion:^(BOOL finished) {
    if (finished)
    {
        self.demoImageView.transform = CGAffineTransformIdentity;
    }
}];
```

> 注: 该转场动画作用于view上

![转场动画1]({{ site.baseurl }}/assets/iOS动画技术/转场动画1.gif)

- 在视图层级中用一个视图替换另一个视图

```objc
/**
 * TransitionAnimation(转场动画)
 *
 * @param fromView   参与转场动画的视图(被移除视图)
 * @param toView     参与转场动画的视图(被添加视图)
 * @param duration   动画持续时长
 * @param options    动画选择项
 * @param completion 动画执行完执行的Block
 *
 * @return  无返回值
 */
+ (void)transitionFromView:(UIView *)fromView toView:(UIView *)toView duration:(NSTimeInterval)duration options:(UIViewAnimationOptions)options completion:(void (^ __nullable)(BOOL finished))completion;

// 以"从左侧翻转转场动画,将原视图移除,添加新视图进来"为例,代码如下
UIImageView *toImageView = [[UIImageView alloc] initWithFrame:CGRectMake(20, 20, [[UIScreen mainScreen] bounds].size.width-40, [[UIScreen mainScreen] bounds].size.height-40)];
toImageView.image = [UIImage imageNamed:@"new"];

[UIView transitionFromView:self.demoImageView toView:toImageView duration:3.0f options:UIViewAnimationOptionTransitionFlipFromLeft completion:^(BOOL finished) {
    if (finished)
    {
        NSLog(@"TransitionAnimation finished");
    }
}];
```

> 注: 该转场动画作用于fromView的父视图上,相当于将toView添加到fromView的父视图上,并将fromView从父视图上移除

![转场动画2]({{ site.baseurl }}/assets/iOS动画技术/转场动画2.gif)

##### Block动画的嵌套
Block动画的嵌套使用,是指在Block动画的animations中再创建一个新的Block动画,该新动画可以根据需求指定与外层动画不同的"动画选择项",新动画会继承外层动画的"动画持续时长"和"时间函数",但是一些"动画选择项"不会继承(在动画选择项中提供了两个用于嵌套动画的选择项,UIViewAnimationOptionOverrideInheritedDuration代表忽略嵌套动画中外层动画的时长设置,UIViewAnimationOptionOverrideInheritedCurve代表忽略嵌套动画中外层动画的时间函数设置)

```objc
// 以"嵌套动画的外层历时5s将视图透明度修改为0.2,嵌套动画的内层不继承外层动画的持续时长,将视图大小缩小一半,期间重复2.5次"为例,代码如下
[UIView animateWithDuration:5.0f delay:0.0f options:UIViewAnimationOptionCurveEaseInOut animations:^{
    self.demoImageView.alpha = 0.2f;

    [UIView animateWithDuration:1.0f delay:0.0f options:UIViewAnimationOptionOverrideInheritedDuration | UIViewAnimationOptionRepeat | UIViewAnimationOptionAutoreverse animations:^{
        [UIView setAnimationRepeatCount:2.5];
         self.demoImageView.transform = CGAffineTransformMakeScale(0.5, 0.5);
    } completion:nil];

} completion:nil];
```

> 注: 如果不使用UIViewAnimationOptionOverrideInheritedDuration,嵌套动画的内层会直接继承外层的持续时长

![嵌套动画]({{ site.baseurl }}/assets/iOS动画技术/嵌套动画.gif)

##### Block动画选择项

```objc
typedef NS_OPTIONS(NSUInteger, UIViewAnimationOptions)
{
    // 通用设置(可同时设置多个)
    UIViewAnimationOptionLayoutSubviews            = 1 <<  0, // 动画过程中,子视图随着一起动画
    UIViewAnimationOptionAllowUserInteraction      = 1 <<  1, // 动画过程中,允许用户交互
    UIViewAnimationOptionBeginFromCurrentState     = 1 <<  2, // 是否从旧动画的当前状态开始新动画,参考UIView的+(void)setAnimationBeginsFromCurrentState:(BOOL)fromCurrentState;方法
    UIViewAnimationOptionRepeat                    = 1 <<  3, // 无限次数重复动画
    UIViewAnimationOptionAutoreverse               = 1 <<  4, // 是否开启反向动画,参考UIView的+(void)setAnimationRepeatAutoreverses:方法(BOOL)repeatAutoreverses;方法
    UIViewAnimationOptionOverrideInheritedDuration = 1 <<  5, // 忽略嵌套动画中外层动画的时长设置(只适用于嵌套动画)
    UIViewAnimationOptionOverrideInheritedCurve    = 1 <<  6, // 忽略嵌套动画中外层动画的时间函数设置(只适用于嵌套动画)
    UIViewAnimationOptionAllowAnimatedContent      = 1 <<  7, // 视图每一帧都会渲染,在动画过程中可以实时更新视图(只适用于转场动画)
    UIViewAnimationOptionShowHideTransitionViews   = 1 <<  8, // 视图切换时直接隐藏旧视图并显示新视图,而不是将旧视图从父视图移除并添加新视图(只适用于转场动画)
    UIViewAnimationOptionOverrideInheritedOptions  = 1 <<  9, // 不继承动画选择项(只适用于嵌套动画)
    
    // 时间函数控制(只可同时设置一项),参考UIView的+(void)setAnimationCurve:(UIViewAnimationCurve)curve;方法
    UIViewAnimationOptionCurveEaseInOut            = 0 << 16, // 默认,淡入淡出,先慢后快再慢
    UIViewAnimationOptionCurveEaseIn               = 1 << 16, // 淡入,先慢后快
    UIViewAnimationOptionCurveEaseOut              = 2 << 16, // 淡出,先快后慢
    UIViewAnimationOptionCurveLinear               = 3 << 16, // 线性
 
    // 转场动画方向(只适用于转场动画,只可同时设置一项),参考UIView的+(void)setAnimationTransition:(UIViewAnimationTransition)transition forView:(UIView *)view cache:(BOOL)cache;方法
    UIViewAnimationOptionTransitionNone            = 0 << 20, // 默认,无效果
    UIViewAnimationOptionTransitionFlipFromLeft    = 1 << 20, // 从左侧翻转
    UIViewAnimationOptionTransitionFlipFromRight   = 2 << 20, // 从右侧翻转
    UIViewAnimationOptionTransitionCurlUp          = 3 << 20, // 向后翻页
    UIViewAnimationOptionTransitionCurlDown        = 4 << 20, // 向前翻页
    UIViewAnimationOptionTransitionCrossDissolve   = 5 << 20, // 旧视图溶解显示新视图
    UIViewAnimationOptionTransitionFlipFromTop     = 6 << 20, // 从上方翻转
    UIViewAnimationOptionTransitionFlipFromBottom  = 7 << 20, // 从下方翻转
}
```

### UIViewKeyframeAnimations(关键帧动画)
Apple从iOS7开始加入了关键帧动画,可以通过提供一个动画在多个关键帧处的属性实现一系列动画效果,两个关键帧中间会有系统自动加入补间动画

```objc
/**
 * KeyframeAnimation(关键帧动画)
 *
 * @param duration   动画持续时长
 * @param delay      延时动画时长
 * @param options    关键帧动画选择项
 * @param animations 动画Block
 * @param completion 动画执行完执行的Block
 *
 * @return 无返回值
 */
+ (void)animateKeyframesWithDuration:(NSTimeInterval)duration delay:(NSTimeInterval)delay options:(UIViewKeyframeAnimationOptions)options animations:(void (^)(void))animations completion:(void (^ __nullable)(BOOL finished))completion;

/**
 * 添加关键帧
 *
 * @param frameStartTime 帧动画相对开始时间,取值为0~1
 * @param frameDuration  帧动画相对持续时长,取值为0~1
 * @param animations     帧动画Block
 *
 * @return 无返回值
 */
+ (void)addKeyframeWithRelativeStartTime:(double)frameStartTime relativeDuration:(double)frameDuration animations:(void (^)(void))animations;

// 以"将视图大小缩小一半,再顺时针旋转90度,再放大2倍,再逆时针旋转90度恢复原样"为例,代码如下
[UIView animateKeyframesWithDuration:3.0f delay:0.0f options:UIViewKeyframeAnimationOptionCalculationModeLinear animations:^{
    [UIView addKeyframeWithRelativeStartTime:0.0f relativeDuration:0.25f animations:^{
        self.demoImageView.transform = CGAffineTransformScale(self.demoImageView.transform, 0.5, 0.5);
    }];
    [UIView addKeyframeWithRelativeStartTime:0.25f relativeDuration:0.25f animations:^{
        self.demoImageView.transform = CGAffineTransformRotate(self.demoImageView.transform, M_PI * 0.5);
    }];
    [UIView addKeyframeWithRelativeStartTime:0.5f relativeDuration:0.25f animations:^{
        self.demoImageView.transform = CGAffineTransformScale(self.demoImageView.transform, 2, 2);
    }];
    [UIView addKeyframeWithRelativeStartTime:0.75f relativeDuration:0.25f animations:^{
        self.demoImageView.transform = CGAffineTransformRotate(self.demoImageView.transform, -M_PI * 0.5);
    }];
} completion:^(BOOL finished) {
    if (finished)
    {
        NSLog(@"TransitionAnimation finished");
    }
    else
    {
        NSLog(@"在动画执行过程中再次被触发动画,会调用这里后重新开始动画");
    }
}];
```

> 注: 关键帧动画有两种形式,UIView只支持属性关键帧动画,暂不支持路径关键帧动画

![关键帧动画]({{ site.baseurl }}/assets/iOS动画技术/关键帧动画.gif)

##### 关键帧动画选择项

```objc
typedef NS_OPTIONS(NSUInteger, UIViewKeyframeAnimationOptions)
{
    // 通用设置(可同时设置多个)
    UIViewKeyframeAnimationOptionLayoutSubviews            = UIViewAnimationOptionLayoutSubviews, // 动画过程中,子视图随着一起动画
    UIViewKeyframeAnimationOptionAllowUserInteraction      = UIViewAnimationOptionAllowUserInteraction, // 动画过程中,允许用户交互
    UIViewKeyframeAnimationOptionBeginFromCurrentState     = UIViewAnimationOptionBeginFromCurrentState, // 是否从旧动画的当前状态开始新动画
    UIViewKeyframeAnimationOptionRepeat                    = UIViewAnimationOptionRepeat, // 无限次数重复动画
    UIViewKeyframeAnimationOptionAutoreverse               = UIViewAnimationOptionAutoreverse, // 是否开启反向动画
    UIViewKeyframeAnimationOptionOverrideInheritedDuration = UIViewAnimationOptionOverrideInheritedDuration, // 忽略嵌套动画中外层动画的时长设置(只适用于嵌套动画)
    UIViewKeyframeAnimationOptionOverrideInheritedOptions  = UIViewAnimationOptionOverrideInheritedOptions, // 忽略嵌套动画中外层动画的时间函数设置(只适用于嵌套动画)
    
    // 设置两个关键帧之间的动画模式(可理解为补间动画模式)(只可同时设置一项)
    UIViewKeyframeAnimationOptionCalculationModeLinear     = 0 << 10, // 默认,线性运算模式(匀速补间动画)
    UIViewKeyframeAnimationOptionCalculationModeDiscrete   = 1 << 10, // 离散运算模式(无补间动画)
    UIViewKeyframeAnimationOptionCalculationModePaced      = 2 << 10, // 均匀运算模式
    UIViewKeyframeAnimationOptionCalculationModeCubic      = 3 << 10, // 平滑运算模式
    UIViewKeyframeAnimationOptionCalculationModeCubicPaced = 4 << 10  // 平滑均匀运算模式
}
```

### 参考文献

1. [View Programming Guide for iOS](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewPG_iPhoneOS/AnimatingViews/AnimatingViews.html#//apple_ref/doc/uid/TP40009503-CH6-SW1)
2. [iOS开发之让你的应用动起来](http://www.cocoachina.com/ios/20141022/10005.html)