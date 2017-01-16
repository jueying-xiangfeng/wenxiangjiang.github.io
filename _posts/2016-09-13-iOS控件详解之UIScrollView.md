---
layout:     post
title:      iOS控件详解之UIScrollView
excerpt:    ""
date:       2016-09-13 15:00:00
author:     "Liu Peng"
header-img: "assets/iOS高级控件/iOS控件详解之UIScrollView-bg.jpg"
tags:
    - iOS高级控件
---

![UIScrollView.png]({{ site.baseurl }}/assets/iOS高级控件/UIScrollView.png)

### 概述

UIScrollView是一个非常重要的控件,其可以展示比设备屏幕更大区域的内容,我们可以通过手指滑动来查看内容视图的每一部分内容,也可以通过手指捏合来对内容视图进行缩放操作,UITableView、UICollectionView和UITextView等常用控件全部继承自UIScrollView,而UIWebView控件内部也是基于UIScrollView实现的,我们每天开发中都不断显式或隐式地与UIScrollView打交道

> 注: 本文中所说的"内容视图"在官方文档中称作"content view",表示UIScrollView中可以用来展示内容的部分

### 属性与方法
##### 内容视图相关

```objc
// 内容视图的大小,默认为CGSizeZero
@property(nonatomic) CGSize contentSize;

// 为内容视图周围增加可滚动区域,默认为UIEdgeInsetsZero
@property(nonatomic) UIEdgeInsets contentInset;

// 内容视图的原点相对于scrollView的原点的偏移量(左上方向偏移为正数),默认为CGPointZero
@property(nonatomic) CGPoint contentOffset;

// 设置内容视图的原点相对于scrollView的原点的偏移量
- (void)setContentOffset:(CGPoint)contentOffset animated:(BOOL)animated;
```

##### 滑动相关

```objc
// 是否允许滑动,默认为YES
@property(nonatomic,getter=isScrollEnabled) BOOL scrollEnabled;

// 是否只允许同时滑动一个方向,默认为NO,如果设置为YES,用户在水平/竖直方向开始进行滑动,便禁止同时在竖直/水平方向滑动(注: 当用户在对角线方向开始进行滑动,则本次滑动可以同时在任何方向滑动)
@property(nonatomic, getter=isDirectionalLockEnabled) BOOL directionalLockEnabled;

// 是否允许点击状态栏让距离状态栏最近的scrollView滑动到顶部,默认为YES(注: 在iPhone中如果有多个将该属性设置为YES的scrollView,则该方法无效;在iPad中则将距离状态栏最近的scrollView滑动到顶部)
@property(nonatomic) BOOL scrollsToTop;

// 是否按页数进行滑动,默认为NO,如果设置为YES,则在滑动时只会停止在scrollView的bounds的倍数处
@property(nonatomic, getter=isPagingEnabled) BOOL pagingEnabled;

// 是否有触底反弹效果,默认为YES
@property(nonatomic) BOOL bounces;

// 是否总是有触底反弹效果(即使内容视图小于scrollView的大小),默认为NO(注: 生效的前提条件为bounces = YES)
@property(nonatomic) BOOL alwaysBounceHorizontal;
@property(nonatomic) BOOL alwaysBounceVertical;

// 指定用户手指离开屏幕后滑动减速的比率,默认为UIScrollViewDecelerationRateNormal(慢慢停止),其余可选项为UIScrollViewDecelerationRateFast(快速停止)
@property(nonatomic) CGFloat decelerationRate;

// 将指定区域滑动到刚好可见处(即距离边缘最近处)
- (void)scrollRectToVisible:(CGRect)rect animated:(BOOL)animated;
```

##### 指示器相关

```objc
// 指示器样式,默认为UIScrollViewIndicatorStyleDefault(黑内容白边框,适用于任何背景),其余可选项为UIScrollViewIndicatorStyleBlack(全黑)和UIScrollViewIndicatorStyleWhite(全白)
@property(nonatomic) UIScrollViewIndicatorStyle indicatorStyle;

// 为指示器周围增加可滚动区域,默认为UIEdgeInsetsZero
@property(nonatomic) UIEdgeInsets scrollIndicatorInsets;

// 是否在滑动时指示器可见,默认为YES
@property(nonatomic) BOOL showsHorizontalScrollIndicator;
@property(nonatomic) BOOL showsVerticalScrollIndicator;

// 闪一下指示器(注: 建议在scrollView展示给用户时调用一下,以提醒用户该处可滑动)
- (void)flashScrollIndicators;
```

##### 事件相关

UIScrollView处理触摸事件原理

当用户在UIScrollView的一个子视图上按下时,UIScrollView并不知道用户是想要滑动内容视图还是点击对应子视图,所以在按下的一瞬间,事件UIEvent从UIApplication传递到UIScrollView后,其会先将该事件拦截而不会立即传递给对应的子视图,同时开始一个150ms的倒计时,并监听用户接下来的行为

- 当倒计时结束前,如果用户的手指发生了移动,则直接滚动内容视图,不会将该事件传递给对应的子视图;
- 当倒计时结束时,如果用户的手指位置没有改变,则调用自身的-touchesShouldBegin:withEvent:inContentView:方法询问是否将事件传递给对应的子视图(如果返回NO,则该事件不会传递给对应的子视图,如果返回YES,则该事件会传递给对应的子视图,默认为YES)
- 当事件被传递给子视图后,如果手指位置又发生了移动,则调用自身的-touchesShouldCancelInContentView:方法询问是否取消已经传递给子视图的事件

```objc
// 返回是否用户已经触碰了内容视图准备进行滑动(注: 该值被设置为YES的时候可能用户只是触碰了内容视图,但是并没有开始进行滑动)
@property(nonatomic,readonly,getter=isTracking) BOOL tracking;

// 返回是否用户已经开始滑动内容视图(注: 该值被设置为YES之前可能需要先滑动一段时间或距离)
@property(nonatomic,readonly,getter=isDragging) BOOL dragging;

// 返回是否处于减速状态(即手指已经离开屏幕,但scrollView仍然处于滑动中)
@property(nonatomic,readonly,getter=isDecelerating) BOOL decelerating;

// 是否延迟事件传递,默认为YES,如果设置为NO,scrollView会立即调用-touchesShouldBegin:withEvent:inContentView:方法以进行下一步操作
@property(nonatomic) BOOL delaysContentTouches;

// 是否可以取消内容视图被触摸,默认为YES,如果设置为NO,则一旦开始跟踪事件,即使手指进行移动也不会取消已经传递给子视图的事件
@property(nonatomic) BOOL canCancelContentTouches;

// 在UIScrollView的子类中重写该方法,用于返回是否将事件传递给对应的子视图,默认返回YES,如果返回NO,该事件不会传递给对应的子视图
- (BOOL)touchesShouldBegin:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event inContentView:(UIView *)view;

// 在UIScrollView的子类中重写该方法,用于返回是否取消已经传递给子视图的事件,默认当子视图是UIControl时返回NO,否则返回YES(注: 该方法被调用的前提是canCancelContentTouches = YES)
- (BOOL)touchesShouldCancelInContentView:(UIView *)view;
```

##### 缩放相关

当用户使用两个手指进行缩放操作时,我们调整内容视图的偏移量和缩放比例(注: 用户两个手指操作结束后,有可能仍然有一个手指在操作,这时不会将事件传递给子视图)

```objc
// 最小缩放比例,默认为1.0
@property(nonatomic) CGFloat minimumZoomScale;

// 最大缩放比例,默认为1.0(必须大于minimumZoomScale才能正常工作)
@property(nonatomic) CGFloat maximumZoomScale;

// 缩放比例,默认为1.0
@property(nonatomic) CGFloat zoomScale;

// 设置缩放比例
- (void)setZoomScale:(CGFloat)scale animated:(BOOL)animated;

// 缩放到指定区域
- (void)zoomToRect:(CGRect)rect animated:(BOOL)animated;

// 是否允许触底反弹,默认为YES
@property(nonatomic) BOOL bouncesZoom;

// 返回是否正在缩放
@property(nonatomic,readonly,getter=isZooming) BOOL zooming;

// 返回是否正在触底反弹
@property(nonatomic,readonly,getter=isZoomBouncing) BOOL zoomBouncing;
```

##### 键盘相关

```objc
// 隐藏键盘模式,默认为UIScrollViewKeyboardDismissModeNone(不隐藏键盘),其余可选项为UIScrollViewKeyboardDismissModeOnDrag(当拖拽scrollView时隐藏键盘)和UIScrollViewKeyboardDismissModeInteractive(当拖拽键盘上方时隐藏键盘)
@property(nonatomic) UIScrollViewKeyboardDismissMode keyboardDismissMode;
```

### 代理
##### 滑动相关

```objc
// 当scrollView的contentOffset发生变化时调用
- (void)scrollViewDidScroll:(UIScrollView *)scrollView;

// 将要开始拖拽时调用(注: 该方法可能需要先滑动一段时间或距离才会被调用)
- (void)scrollViewWillBeginDragging:(UIScrollView *)scrollView;

// 当用户停止拖拽时调用(注: 应用程序可以通过修改targetContentOffset参数的值来调整内容视图content view停止的位置)
- (void)scrollViewWillEndDragging:(UIScrollView *)scrollView withVelocity:(CGPoint)velocity targetContentOffset:(inout CGPoint *)targetContentOffset;

// 当用户停止拖拽时调用(注: 如果内容视图content view在停止拖拽后继续移动,则decelerate参数为YES)
- (void)scrollViewDidEndDragging:(UIScrollView *)scrollView willDecelerate:(BOOL)decelerate;

// 将要开始减速时调用(仅当停止拖拽后继续移动时才会被调用)
- (void)scrollViewWillBeginDecelerating:(UIScrollView *)scrollView;

// 已经结束减速时调用(仅当停止拖拽后继续移动时才会被调用)
- (void)scrollViewDidEndDecelerating:(UIScrollView *)scrollView;

// 返回是否允许点击状态栏让scrollView滑动到顶部,如果未实现该方法,则默认为YES(仅当scrollsToTop属性为YES时才调用)
- (BOOL)scrollViewShouldScrollToTop:(UIScrollView *)scrollView;

// 当scrollView已经滑动到顶部时调用(仅当点击状态栏让scrollView滑动到顶部才调用)
- (void)scrollViewDidScrollToTop:(UIScrollView *)scrollView;

// 当-setContentOffset:animated:/-scrollRectVisible:animated:方法动画结束时调用(仅当animated设置为YES时才调用)
- (void)scrollViewDidEndScrollingAnimation:(UIScrollView *)scrollView;
```

##### 缩放相关

```objc
// 当缩放比例更改时调用
- (void)scrollViewDidZoom:(UIScrollView *)scrollView;

// 参与缩放的子视图
- (UIView *)viewForZoomingInScrollView:(UIScrollView *)scrollView;

// 将要开始缩放时调用
- (void)scrollViewWillBeginZooming:(UIScrollView *)scrollView withView:(UIView *)view;

// 已经结束缩放时调用
- (void)scrollViewDidEndZooming:(UIScrollView *)scrollView withView:(UIView *)view atScale:(CGFloat)scale;
```

### 常见需求原理解析
##### 导航栏半透明效果

![导航栏半透明效果.gif]({{ site.baseurl }}/assets/iOS高级控件/导航栏半透明效果.gif)

原理解析:

默认情况下,在有UINavigationBar存在时,系统为了防止UIScrollView被遮挡,其contentInset和scrollIndicatorInsets属性都会被设置为UIEdgeInsetsMake(64, 0, 0, 0);在有UITabBar存在时,系统为了防止UIScrollView被遮挡,其contentInset和scrollIndicatorInsets属性都会被设置为UIEdgeInsetsMake(0, 0, 49, 0)

因此,为了使用此种半透明效果,可以直接将UIScrollView的frame设置为整个屏幕的大小

> 注1: 系统只在UIScrollView是控制器视图的第0个子视图时才会自动修改contentInset和scrollIndicatorInsets属性

> 注2: 如果不想让系统自动修改contentInset和scrollIndicatorInsets属性,可以设置self.automaticallyAdjustsScrollViewInsets = NO;

##### 控件悬停

![控件悬停.gif]({{ site.baseurl }}/assets/iOS高级控件/控件悬停.gif)

原理解析:

方式一: 在悬停位置放置一个与待悬停控件相同的控件,通过-scrollViewDidScroll:代理方法跟踪contentOffset的的变化,当不满足悬停条件时,将该控件hidden属性设置为YES;当满足悬停条件时,将该控件hidden属性设置为NO

方式二: 通过-scrollViewDidScroll:代理方法跟踪contentOffset的的变化,当不满足悬停条件时,待悬停控件属于UIScrollView的子视图,当满足悬停条件时,待悬停控件属于UIScrollView的父视图的子视图

```objc
// 以"方式二"为例
- (void)scrollViewDidScroll:(UIScrollView *)scrollView
{
   if (scrollView.contentOffset.y >= 100)
   {
       CGRect rect = label.frame;
       rect.origin.y = 0;
       label.frame = rect;

       [self.view addSubview:label];
   }
   else
   {
       CGRect rect = label.frame;
       rect.origin.y = 100;
       label.frame = rect;

       [scrollView addSubview:label];
   }
}
```

##### 下拉头部图片放大

![下拉头部图片放大.gif]({{ site.baseurl }}/assets/iOS高级控件/下拉头部图片放大.gif)

原理解析:

通过-scrollViewDidScroll:代理方法跟踪contentOffset的的变化,根据contentOffset动态设置图片的缩放比例

```objc
// 以"动态修改图片缩放比例于1倍和2倍之间"为例
- (void)scrollViewDidScroll:(UIScrollView *)scrollView
{
   CGFloat scale = 1 - (scrollView.contentOffset.y / 100);
   scale = (scale >= 1) ? scale : 1;
   scale = (scale <= 2) ? scale : 2;
   imageView.transform = CGAffineTransformMakeScale(scale, scale);
}
```

##### 图片无限轮播

![图片无限轮播.gif]({{ site.baseurl }}/assets/iOS高级控件/图片无限轮播.gif)

原理解析:

在已知图片数组有N个元素前提下,在UIScrollView中创建N+2个UIImageView,其中第1个至第N个图片为真实内容,第0个与第N个一样,第N+1个与第1个一样,通过-scrollViewDidScroll:代理方法跟踪contentOffset的的变化,在滑动到首尾两个图片处直接设置contentOffset到真实图片处即可

![图片无限轮播原理.png]({{ site.baseurl }}/assets/iOS高级控件/图片无限轮播原理.png)