---
layout:     post
title:      UINavigationController与UINavigationBar详解
excerpt:    "UINavigationController继承自UIViewController, 专门用于展示层级结构的内容, 可以让我们更高效地将层级结构的内容呈现给用户"
date:       2017-02-16 19:24:00
author:     "Liu Peng"
header-img: "assets/iOS多控制器管理/UINavigationController与UINavigationBar详解-bg.png"
tags:
    - iOS多控制器管理
---

# UINavigationController相关

### 概述

UINavigationController继承自UIViewController, 专门用于展示层级结构的内容, 可以让我们更高效地将层级结构的内容呈现给用户. UINavigationController在展示界面时模拟数据的层级结构, 在每个层级分别使用一个UIViewController来展示该层级的内容, 下面我们以iOS设备中的"设置"界面为例, 进行一下简单的认识

![UINavigationController样例.png]({{ site.baseurl }}/assets/iOS多控制器管理/UINavigationController样例.png)

UINavigationController(导航控制器)是一个容器控制器, 其内部展示着多个UIViewController(视图控制器)的内容, 我们可以通过UINavigationController的view属性获取到其自身的视图, 在该视图上有一个位于界面顶部的UINavigationBar(导航栏)和位于界面底部的默认隐藏的UIToolbar(工具栏), 以及一个位于界面中间部分的UIViewController的view

![UINavigationController层级.png]({{ site.baseurl }}/assets/iOS多控制器管理/UINavigationController层级.png)

当用户在UINavigationController的层级结构中来回切换时, UINavigationBar和UIToolbar的内容会随着发生变化, 但是其本身并不会发生变化, 唯一发生变化的就是位于界面中间部分的UIViewController的view

### UIViewController堆栈的管理

UINavigationController通过其管理的**UIViewController堆栈**来决定展示在UINavigationController中间部位的内容, 该内容由位于**UIViewController堆栈**栈顶位置的UIViewController来决定

![UIViewController堆栈.png]({{ site.baseurl }}/assets/iOS多控制器管理/UIViewController堆栈.png)

> 注: 上图中viewControllers是**UIViewController堆栈**; 上图中navigationBar是位于界面顶部的UINavigationBar; 上图中toolbar是位于界面底部的UIToolbar

我们可以利用系统提供的方法向**UIViewController堆栈**中Push一个UIViewController, 从**UIViewController堆栈**中Pop一个UIViewController, 也可以直接设置**UIViewController堆栈**中的全部UIViewController, 下面将分别对其进行介绍

> 注: 位于**UIViewController堆栈**栈底位置的UIViewController称为"根视图控制器", 每个UINavigationController在展示到屏幕上之前都必须拥有该根视图控制器

##### 向UIViewController堆栈中Push一个UIViewController

```objc
// 方法
/*
 * 参数一: UIViewController, 该参数不可以使用UITabBarController的实例
 * 参数二: 是否执行动画
 */
- (void)pushViewController:(UIViewController *)viewController animated:(BOOL)animated;
// 示例
[navigationController pushViewController:[[UIViewController alloc] init] animated:YES];
```

```objc
// 方法
/*
 * 参数一: UIViewController, 该参数不可以使用UITabBarController的实例
 * 参数二: 要求展示UIViewController的对象
 */
- (void)showViewController:(UIViewController *)vc sender:(id)sender;
// 示例
[navigationController showViewController:[[UIViewController alloc] init] sender:nil];
```

> 注: 该方法与调用pushViewController:animated:相同, 该方法自iOS8开始生效

##### 从UIViewController堆栈中Pop一个UIViewController

```objc
// 方法
/*
 * 参数一: 是否执行动画
 * 返回值: 从UIViewController堆栈中Pop出来的UIViewController
 */
- (UIViewController *)popViewControllerAnimated:(BOOL)animated;
// 示例
UIViewController *viewController = [navigationController popViewControllerAnimated:YES];
```

> 注: 如果**UIViewController堆栈**中只有一个UIViewController, 则该方法无效

##### 从UIViewController堆栈中一直Pop到根视图控制器

```objc
// 方法
/*
 * 参数一: 是否执行动画
 * 返回值: 从UIViewController堆栈中Pop出来的UIViewController数组
 */
- (NSArray<__kindof UIViewController *> *)popToRootViewControllerAnimated:(BOOL)animated;
// 示例
NSArray *viewControllers = [navigationController popToRootViewControllerAnimated:YES];
```

##### 从UIViewController堆栈中一直Pop到指定UIViewController

```objc
// 方法
/*
 * 参数一: 指定UIViewController, 该UIViewController必须位于当前UIViewController堆栈中
 * 参数二: 是否执行动画
 * 返回值: 从UIViewController堆栈中Pop出来的UIViewController数组
 */
- (NSArray<__kindof UIViewController *> *)popToViewController:(UIViewController *)viewController animated:(BOOL)animated;
// 示例
NSArray *viewControllers = [navigationController popToViewController:navigationController.viewControllers[0] animated:YES];
```

##### 设置UIViewController堆栈中全部UIViewController

```objc
// 属性
@property(nonatomic, copy) NSArray<__kindof UIViewController *> *viewControllers;
// 示例
NSArray *viewControllers = navigationController.viewControllers;
```
> 注: 设置该属性等价于调用setViewControllers:animated:方法, 其中animated参数为NO

##### 设置UIViewController堆栈中全部UIViewController(可选动画)

```objc
// 方法
/*
 * 参数一: UIViewController数组
 * 参数二: 是否执行动画
 */
- (void)setViewControllers:(NSArray<UIViewController *> *)viewControllers animated:(BOOL)animated;
// 示例
[navigationController setViewControllers:@[viewController1, viewController2, viewController3] animated:YES];
```

当animated参数为YES时, 系统会根据如下三种情况选择采用何种动画形式

- 当数组中的最后一个元素在当前堆栈中, 且是位于栈顶, 则采用无动画形式
- 当数组中的最后一个元素在当前堆栈中, 且不是位于栈顶, 则采用Pop动画形式
- 当数组中的最后一个元素不在当前堆栈中, 则采用Push动画形式

##### 获取位于UIViewController堆栈栈顶位置的UIViewController

```objc
// 属性
@property(nonatomic, readonly, strong) UIViewController *topViewController;
// 示例
UIViewController *viewController = navigationController.topViewController;
```

##### 获取在UINavigationController中当前可见视图的UIViewController

```objc
// 属性
@property(nonatomic, readonly, strong) UIViewController *visibleViewController;
// 示例
UIViewController *viewController = navigationController.visibleViewController;
```

> 注: 当前可见视图的UIViewController是位于**UIViewController堆栈**栈顶位置的UIViewController或者通过模态视图展示出来的UIViewController

### UINavigationBar的管理

UINavigationController负责创建、配置及管理UINavigationBar, 其通过位于**UIViewController堆栈**栈顶位置的UIViewController的navigationItem属性(该属性位于UIViewController的UINavigationControllerItem类目中)来管理UINavigationBar展示的内容, 同时UINavigationController也提供了navigationBar属性, 允许开发者通过该属性设置UINavigationBar的外观

> 注: UINavigationController是UINavigationBar的delegate, 其负责响应该UINavigationBarDelegate的代理方法, 并据此更新位于界面中间部分的UIViewController的视图

##### 设置UINavigationBar的外观

```objc
// 属性
@property(nonatomic, readonly) UINavigationBar *navigationBar;
// 示例
navigationController.navigationBar.barStyle = UIBarStyleDefault;
```

> 注: 我们可以通过该属性设置UINavigationBar的外观, 但是不要通过该属性设置其frame, bounds, alpha等属性, 更不要修改其层级结构

##### 设置UINavigationBar的隐藏状态

```objc
// 属性
@property(nonatomic, getter=isNavigationBarHidden) BOOL navigationBarHidden;
// 示例
navigationController.navigationBarHidden = YES;
```

> 注: 设置该属性等价于调用setNavigationBarHidden:animated:方法, 其中animated参数为NO

##### 设置UINavigationBar的隐藏状态(可选动画)

```objc
// 方法
/*
 * 参数一: 隐藏状态
 * 参数二: 是否执行动画
 */
- (void)setNavigationBarHidden:(BOOL)hidden animated:(BOOL)animated;
// 示例
[navigationController setNavigationBarHidden:YES animated:YES];
```

### UIToolbar的管理

UINavigationController负责创建、配置及管理UIToolbar, 其通过位于**UIViewController堆栈**栈顶位置的UIViewController的toolbarItems属性(该属性位于UIViewController的UINavigationControllerContextualToolbarItems类目中)来管理UIToolbar展示的内容, 同时UINavigationController也提供了toolbar属性, 允许开发者通过该属性设置UIToolbar的外观

##### 设置UIToolbar的外观

```objc
// 属性
@property(nonatomic, readonly) UIToolbar *toolbar;
// 示例
navigationController.toolbar.barStyle = UIBarStyleDefault;
```

##### 设置UIToolbar的隐藏状态

```objc
// 属性
@property(nonatomic, getter=isToolbarHidden) BOOL toolbarHidden;
// 示例
navigationController.toolbarHidden = NO;
```

> 注: 设置该属性等价于调用setToolbarHidden:animated:方法, 其中animated参数为NO

##### 设置UIToolbar的隐藏状态(可选动画)

```objc
// 方法
/*
 * 参数一: 隐藏状态
 * 参数二: 是否执行动画
 */
- (void)setToolbarHidden:(BOOL)hidden animated:(BOOL)animated;
// 示例
[navigationController setToolbarHidden:NO animated:YES];
```

### 手势识别器的管理

##### 获取手势识别器

```objc
// 侧滑返回手势识别器
@property(nonatomic, readonly) UIGestureRecognizer *interactivePopGestureRecognizer;
// 用于轻拍隐藏UINavigationBar与UIToolbar的手势识别器
@property(nonatomic, readonly, assign) UITapGestureRecognizer *barHideOnTapGestureRecognizer;
// 用于轻扫隐藏UINavigationBar与UIToolbar的手势识别器
@property(nonatomic, readonly, strong) UIPanGestureRecognizer *barHideOnSwipeGestureRecognizer;
// 示例
UIGestureRecognizer *interactivePopGestureRecognizer = navigationController.interactivePopGestureRecognizer;
```

> 注: 默认情况下, 用户的一个操作只能被一个UIGestureRecognizer(手势识别器)识别, 系统将该手势识别器提供给开发者以便在gestureRecognizer:shouldRecognizeSimultaneouslyWithGestureRecognizer:代理方法中设置允许其他手势识别器与该手势识别器同时被识别

##### 通过手势隐藏UINavigationBar与UIToolbar

```objc
// 轻拍隐藏、再次轻拍显示
@property(nonatomic, readwrite, assign) BOOL hidesBarsOnTap;
// 向上轻扫隐藏、向下轻扫显示
@property(nonatomic, readwrite, assign) BOOL hidesBarsOnSwipe;
// 横屏隐藏(此时轻拍显示)、竖屏显示
@property(nonatomic, readwrite, assign) BOOL hidesBarsWhenVerticallyCompact;
// 键盘出现隐藏、键盘消失保持隐藏(此时轻拍显示)
@property(nonatomic, readwrite, assign) BOOL hidesBarsWhenKeyboardAppears;
// 示例
navigationController.hidesBarsOnTap = YES;
```

> 注: 该属性自iOS8开始生效

### UINavigationController对象的初始化

UINavigationController对象的初始化分为两种方式, 下面将分别对其进行介绍

第一种方式, 我们可以直接通过"根视图控制器"来初始化, 该种方式会自动向**UIViewController堆栈**中Push该UIViewController

```objc
// 方法
/*
 * 参数一: UIViewController, 该参数不可以使用UITabBarController的实例
 */
- (instancetype)initWithRootViewController:(UIViewController *)rootViewController;
// 示例
UINavigationController *navigationController = [[UINavigationController alloc] initWithRootViewController:[[UIViewController alloc] init]];
```

第二种方式, 我们可以通过UINavigationBar及UIToolbar来初始化, 该种方式需要我们在UINavigationController展示在屏幕上之前向**UIViewController堆栈**中Push一个或多个UIViewController

```objc
// 方法
/*
 * 参数一: 自定义UINavigationBar的子类, 如果是nil则为UINavigationBar类
 * 参数二: 自定义UIToolbar的子类, 如果是nil则为UIToolbar类
 */
- (instancetype)initWithNavigationBarClass:(Class)navigationBarClass toolbarClass:(Class)toolbarClass;
// 示例
UINavigationController *navigationController = [[UINavigationController alloc] initWithNavigationBarClass:[UINavigationBar class] toolbarClass:[UIToolbar class]];
```

> 注: 如果只是希望订制全部UINavigationBar或UIToolbar的外观, 可以优先考虑使用UIAppearance API进行全局设置, 而不是自定义其子类, 下文中属性/方法后面标有UI_APPEARANCE_SELECTOR的均可使用UIAppearance API进行全局设置

### UINavigationController的代理方法

##### 将要展示一个UIViewController

```objc
// 方法
/*
 * 参数一: UINavigationController
 * 参数二: UIViewController
 * 参数三: 是否执行动画
 */
- (void)navigationController:(UINavigationController *)navigationController willShowViewController:(UIViewController *)viewController animated:(BOOL)animated;
```

> 注: 该UIViewController将要位于**UIViewController堆栈**栈顶位置(可能是通过Push/Pop/直接设置**UIViewController堆栈**的方式位于**UIViewController堆栈**栈顶位置)

##### 已经展示一个UIViewController

```objc
// 方法
/*
 * 参数一: UINavigationController
 * 参数二: UIViewController
 * 参数三: 是否执行动画
 */
- (void)navigationController:(UINavigationController *)navigationController didShowViewController:(UIViewController *)viewController animated:(BOOL)animated;
```

> 注: 该UIViewController已经位于**UIViewController堆栈**栈顶位置(可能是通过Push/Pop/直接设置**UIViewController堆栈**的方式位于**UIViewController堆栈**栈顶位置)

##### 提供自定义非交互式过渡动画

```objc
// 方法
/*
 * 参数一: UINavigationController
 * 参数二: UINavigationControllerOperation, 可选项为UINavigationControllerOperationNone(无操作), UINavigationControllerOperationPush(Push操作), UINavigationControllerOperationPop(Pop操作)
 * 参数三: UIViewController, 当前可见视图控制器
 * 参数四: UIViewController, 动画结束后可见视图控制器
 * 返回值: 实现了UIViewControllerAnimatedTransitioning协议的NSObject对象
 */
- (id<UIViewControllerAnimatedTransitioning>)navigationController:(UINavigationController *)navigationController animationControllerForOperation:(UINavigationControllerOperation)operation fromViewController:(UIViewController *)fromVC toViewController:(UIViewController *)toVC;
```

> 注: 如果想要提供自定义交互式过渡动画, 需要额外实现navigationController:interactionControllerForAnimationController:代理方法

##### 提供自定义交互式过渡动画

```objc
// 方法
/*
 * 参数一: UINavigationController
 * 参数二: 由navigationController:animationControllerForOperation:fromViewController:toViewController:代理方法提供的实现了UIViewControllerAnimatedTransitioning协议的NSObject对象
 * 返回值: 实现了UIViewControllerInteractiveTransitioning协议的NSObject对象
 */
- (id<UIViewControllerInteractiveTransitioning>)navigationController:(UINavigationController *)navigationController interactionControllerForAnimationController:(id<UIViewControllerAnimatedTransitioning>)animationController;
```

> 注: 需要同时实现navigationController:animationControllerForOperation:fromViewController:toViewController:代理方法

##### 提供优先展示方向

```objc
// 方法
/*
 * 参数一: UINavigationController
 * 返回值: UIInterfaceOrientation
 */
- (UIInterfaceOrientation)navigationControllerPreferredInterfaceOrientationForPresentation:(UINavigationController *)navigationController;
```

##### 提供支持展示方向

```objc
// 方法
/*
 * 参数一: UINavigationController
 * 返回值: UIInterfaceOrientationMask
 */
- (UIInterfaceOrientationMask)navigationControllerSupportedInterfaceOrientations:(UINavigationController *)navigationController;
```

# UINavigationBar相关

### 概述

UINavigationBar是一个在层级结构中起导航作用的视觉控件, 其一般展示形式如下图所示

![UINavigationBar样例.png]({{ site.baseurl }}/assets/iOS多控制器管理/UINavigationBar样例.png)

### UINavigationItem堆栈的管理

UINavigationBar虽然继承自UIView, 但是其并非通过addSubview:方法来添加子视图, 而是通过其管理的**UINavigationItem堆栈**来决定展示在UINavigationBar中的内容

![UINavigationItem堆栈.png]({{ site.baseurl }}/assets/iOS多控制器管理/UINavigationItem堆栈.png)

> 注: 上图中items是**UINavigationItem堆栈**; 上图中topItem是位于**UINavigationItem堆栈**栈顶位置的UINavigationItem; 上图中backItem是位于**UINavigationItem堆栈**栈顶下方位置的UINavigationItem

我们可以利用系统提供的方法向**UINavigationItem堆栈**中Push一个UINavigationItem, 从**UINavigationItem堆栈**中Pop一个UINavigationItem, 也可以直接设置**UINavigationItem堆栈**中的全部UINavigationItem, 下面将分别对其进行介绍

##### 向UINavigationItem堆栈中Push一个UINavigationItem

```objc
// 方法
/*
 * 参数一: UINavigationItem
 * 参数二: 是否执行动画
 */
- (void)pushNavigationItem:(UINavigationItem *)item animated:(BOOL)animated;
// 示例
UINavigationItem *navigationItem = [[UINavigationItem alloc] initWithTitle:@"Owen"];
[navigationBar pushNavigationItem:navigationItem animated:YES];
```

##### 从UINavigationItem堆栈中Pop一个UINavigationItem

```objc
// 方法
/*
 * 参数一: 是否执行动画
 * 返回值: 从UINavigationItem堆栈中Pop出来的UINavigationItem
 */
- (UINavigationItem *)popNavigationItemAnimated:(BOOL)animated;
// 示例
UINavigationItem *navigationItem = [navigationBar popNavigationItemAnimated:YES];
```

##### 设置UINavigationItem堆栈中全部UINavigationItem

```objc
// 属性
@property(nonatomic, copy) NSArray<UINavigationItem *> *items;
// 示例
navigationBar.items = @[navigationItem1, navigationItem2, navigationItem3];
```

> 注: 设置该属性等价于调用setViewControllers:animated:方法, 其中animated参数为NO

##### 设置UINavigationItem堆栈中全部UINavigationItem(可选动画)

```objc
// 方法
/*
 * 参数一: UINavigationItem数组
 * 参数二: 是否执行动画
 */
- (void)setItems:(NSArray<UINavigationItem *> *)items animated:(BOOL)animated;
// 示例
[navigationBar setItems:@[navigationItem1, navigationItem2, navigationItem3] animated:YES];
```

当animated参数为YES时, 系统会根据如下三种情况选择采用何种动画形式

- 当数组中的最后一个元素在当前堆栈中, 且是位于栈顶, 则采用无动画形式
- 当数组中的最后一个元素在当前堆栈中, 且不是位于栈顶, 则采用Pop动画形式
- 当数组中的最后一个元素不在当前堆栈中, 则采用Push动画形式

##### 获取位于UINavigationItem堆栈栈顶位置的UINavigationItem

```objc
// 属性
@property(nonatomic, readonly, strong) UINavigationItem *topItem;
// 示例
UINavigationItem *navigationItem = navigationBar.topItem;
```

##### 获取位于UINavigationItem堆栈栈顶下方位置的UINavigationItem

```objc
// 属性
@property(nonatomic, readonly, strong) UINavigationItem *backItem;
// 示例
UINavigationItem *navigationItem = navigationBar.backItem;
```

> 注: 如果**UINavigationItem堆栈**中只有一个UINavigationItem, 则该值为nil

### UINavigationBar的内容

UINavigationBar通过**UINavigationItem堆栈**按照如下方式来决定展示在UINavigationBar中的内容

位于中间的标题会根据下方顺序选择展示的内容

- 如果topItem设置了标题视图(titleView属性), 则展示标题视图
- 如果topItem设置了标题文字(title属性), 则展示标题文字
- 如果以上都未设置, 则展示空白

位于右侧的按钮会根据下方顺序选择展示的内容

- 如果topItem设置了右侧按钮(rightBarButtonItem属性), 则展示右侧按钮
- 如果以上都未设置, 则展示空白

位于左侧的按钮会根据下方顺序选择展示的内容

- 如果topItem设置了左侧按钮(leftBarButtonItem属性), 则展示左侧按钮
- 如果backItem设置了返回按钮(backBarButtonItem属性), 则展示返回按钮
- 如果backItem设置了标题文字(title属性), 则展示利用标题文字封装的返回按钮
- 如果以上都未设置, 则展示利用文字"Back"封装的返回按钮

> 注: 如果**UINavigationItem堆栈**中只有一个UINavigationItem, 则不会展示返回按钮

##### 初始化UINavigationItem对象

```objc
// 方法
/*
 * 参数一: 标题文字
 */
- (instancetype)initWithTitle:(NSString *)title;
// 示例
UINavigationItem *navigationItem = [[UINavigationItem alloc] initWithTitle:@"Owen"];
```

##### 设置标题文字

```objc
// 属性
@property(nonatomic, copy) NSString *title;
// 示例
navigationItem.title = @"Title";
```

##### 设置标题视图

```objc
// 属性
@property(nonatomic, strong) UIView *titleView;
// 示例
navigationItem.titleView = [UIButton buttonWithType:UIButtonTypeInfoLight];
```

##### 设置提示文字

```objc
// 属性
@property(nonatomic, copy) NSString *prompt;
// 示例
navigationItem.title = @"Prompt";
```

##### 设置返回按钮

```objc
// 属性
@property(nonatomic, strong) UIBarButtonItem *backBarButtonItem;
// 示例
UIBarButtonItem *barButtonItem = [[UIBarButtonItem alloc] initWithTitle:@"Owen" style:UIBarButtonItemStylePlain target:nil action:nil];
navigationItem.backBarButtonItem = barButtonItem;
```

> 注: 该UIBarButtonItem对象需要是通过title或image创建的, 如果是通过自定义视图创建的UIBarButtonItem对象在此不生效

##### 设置返回按钮隐藏状态

```objc
// 属性
@property(nonatomic, assign) BOOL hidesBackButton;
// 示例
navigationItem.hidesBackButton = YES;
```

##### 设置返回按钮隐藏状态(可选动画)

```objc
// 方法
/*
 * 参数一: 是否隐藏返回按钮
 * 参数二: 是否执行动画
 */
- (void)setHidesBackButton:(BOOL)hidesBackButton animated:(BOOL)animated;
// 示例
[navigationItem setHidesBackButton:YES animated:YES];
```

##### 设置左/右侧按钮

```objc
// 属性
@property(nonatomic, strong) UIBarButtonItem *leftBarButtonItem;
@property(nonatomic, copy) NSArray<UIBarButtonItem *> *leftBarButtonItems;
@property(nonatomic, strong) UIBarButtonItem *rightBarButtonItem;
@property(nonatomic, copy) NSArray<UIBarButtonItem *> *rightBarButtonItems;
// 示例
UIBarButtonItem *barButtonItem = [[UIBarButtonItem alloc] initWithBarButtonSystemItem:UIBarButtonSystemItemDone target:nil action:nil];
navigationItem.rightBarButtonItem = barButtonItem;
```

##### 设置左/右侧按钮(可选动画)

```objc
// 方法
/*
 * 参数一: UIBarButtonItem
 * 参数二: 是否执行动画
 */
- (void)setLeftBarButtonItem:(UIBarButtonItem *)item animated:(BOOL)animated;
- (void)setLeftBarButtonItems:(NSArray<UIBarButtonItem *> *)items animated:(BOOL)animated;
- (void)setRightBarButtonItem:(UIBarButtonItem *)item animated:(BOOL)animated;
- (void)setRightBarButtonItem:(UIBarButtonItem *)item animated:(BOOL)animated;
// 示例
UIBarButtonItem *barButtonItem = [[UIBarButtonItem alloc] initWithBarButtonSystemItem:UIBarButtonSystemItemDone target:nil action:nil];
[navigationItem setRightBarButtonItem:barButtonItem animated:YES];
```

##### 设置左侧按钮是否与返回按钮共同存在

```objc
属性
@property(nonatomic) BOOL leftItemsSupplementBackButton;
// 示例
navigationItem.leftItemsSupplementBackButton = YES;
```

> 注: 该属性默认为NO, 即左侧按钮会替代返回按钮而单独存在, 如果设置为YES, 则左侧按钮会与返回按钮共同存在并显示在返回按钮的右侧

### UINavigationBar的外观

UINavigationBar类中提供了大量属性/方法用于设置其外观, 我们可以设置其样式、背景颜色、色彩颜色、文字属性等

![UINavigationBar的外观1.png]({{ site.baseurl }}/assets/iOS多控制器管理/UINavigationBar的外观1.png)

同样, 我们也可以设置其背景图片、阴影图片等

![UINavigationBar的外观2.png]({{ site.baseurl }}/assets/iOS多控制器管理/UINavigationBar的外观2.png)

##### 设置样式

```objc
// 属性
@property(nonatomic,assign) UIBarStyle barStyle UI_APPEARANCE_SELECTOR;
// 示例
navigationBar.barStyle = UIBarStyleDefault;
```

> 注: 该属性默认为UIBarStyleDefault(白底黑字), 可选项为UIBarStyleBlack(黑底白字)

##### 设置透明性

```objc
// 属性
@property(nonatomic, assign, getter=isTranslucent) BOOL translucent UI_APPEARANCE_SELECTOR;
// 示例
navigationBar.translucent = YES;
```

该属性默认为YES

- 当为UINavigationBar设置了背景图片时, 如果图片中有任何像素的alpha小于1.0, 则该属性为YES, 否则为NO
- 当为UINavigationBar设置了不透明的背景图片时, 如果设置属性为YES, 则该图片会被做透明处理
- 当为UINavigationBar设置了透明的背景图片时, 如果设置属性为NO, 则该图片会被添加一个不透明的背景, 该背景在UIBarStyleDefault样式下为白色, 在UIBarStyleBlack样式下为黑色, 如果设置了barTintColor, 则与其同色

##### 设置背景颜色

```objc
// 属性
@property(nonatomic, strong) UIColor *barTintColor UI_APPEARANCE_SELECTOR;
// 示例
navigationBar.barTintColor = [UIColor orangeColor];
```

##### 设置色彩颜色

```objc
// 属性
@property(nonatomic, strong) UIColor *tintColor;
// 示例
navigationBar.tintColor = [UIColor greenColor];
```

##### 设置标题文字属性

```objc
// 属性
@property(nonatomic, copy) NSDictionary<NSString *,id> *titleTextAttributes UI_APPEARANCE_SELECTOR;
// 示例
navigationBar.titleTextAttributes = @{NSFontAttributeName: [UIFont systemFontOfSize:30], NSForegroundColorAttributeName: [UIColor whiteColor]};
```

##### 设置/获取标题栏竖直位置偏移

```objc
// 方法
/*
 * 参数一: 偏移量
 * 参数二: 可选项为UIBarMetricsDefault(竖屏), UIBarMetricsCompact(横屏), UIBarMetricsDefaultPrompt(拥有提示文字的竖屏), UIBarMetricsCompactPrompt(拥有提示文字的横屏)
 */
- (void)setTitleVerticalPositionAdjustment:(CGFloat)adjustment forBarMetrics:(UIBarMetrics)barMetrics UI_APPEARANCE_SELECTOR;
- (CGFloat)titleVerticalPositionAdjustmentForBarMetrics:(UIBarMetrics)barMetrics UI_APPEARANCE_SELECTOR;
// 示例
[navigationBar setTitleVerticalPositionAdjustment:10.0f forBarMetrics:UIBarMetricsDefault];
```

##### 设置/获取背景图片

```objc
// 方法
/*
 * 参数一: 背景图片
 * 参数二: 默认为UIBarPositionAny(不指定), 可选项为UIBarPositionBottom(在容器下方), UIBarPositionTop(在容器上方), UIBarPositionTopAttached(在屏幕上方, 与容器平级)
 * 参数三: 可选项为UIBarMetricsDefault(竖屏, 横屏未设置也使用该效果), UIBarMetricsCompact(横屏), UIBarMetricsDefaultPrompt(拥有提示文字的竖屏, 横屏未设置也使用该效果), UIBarMetricsCompactPrompt(拥有提示文字的横屏)
 */
- (void)setBackgroundImage:(UIImage *)backgroundImage forBarMetrics:(UIBarMetrics)barMetrics  UI_APPEARANCE_SELECTOR;
- (void)setBackgroundImage:(UIImage *)backgroundImage forBarPosition:(UIBarPosition)barPosition barMetrics:(UIBarMetrics)barMetrics  UI_APPEARANCE_SELECTOR;
- (UIImage *)backgroundImageForBarMetrics:(UIBarMetrics)barMetrics  UI_APPEARANCE_SELECTOR;
- (UIImage *)backgroundImageForBarPosition:(UIBarPosition)barPosition barMetrics:(UIBarMetrics)barMetrics  UI_APPEARANCE_SELECTOR;
// 示例
[navigationBar setBackgroundImage:[UIImage imageNamed:@""] forBarPosition:UIBarPositionTop barMetrics:UIBarMetricsDefault];
```

##### 设置阴影图片

```objc
// 属性
@property(nonatomic, strong) UIImage *shadowImage UI_APPEARANCE_SELECTOR;
// 示例
navigationBar.shadowImage = [UIImage imageNamed:@""];
```

> 注: 该阴影图片默认为位于UINavigationBar最下方的一条黑线, 在设置该属性之前, 需要通过setBackgroundImage:forBarMetrics:设置一个背景图片方可生效

> 注: 我们可以通过设置该属性为[UIImage new]去除默认阴影图片

##### 设置返回按钮图片

```objc
// 属性
@property(nonatomic, strong) UIImage *backIndicatorImage UI_APPEARANCE_SELECTOR;
@property(nonatomic, strong) UIImage *backIndicatorTransitionMaskImage UI_APPEARANCE_SELECTOR;
// 示例
navigationBar.backIndicatorImage = [UIImage imageNamed:@""];
navigationBar.backIndicatorTransitionMaskImage = [UIImage imageNamed:@""];
```

> 注: 以上两个属性需要同时设置方可生效

### UINavigationBar的代理方法

##### 是否允许向UINavigationItem堆栈中Push一个UINavigationItem

```objc
// 方法
/*
 * 参数一: UINavigationBar
 * 参数二: UINavigationItem
 */
- (BOOL)navigationBar:(UINavigationBar *)navigationBar shouldPushItem:(UINavigationItem *)item;
```

##### 已经向UINavigationItem堆栈中Push一个UINavigationItem

```objc
// 方法
/*
 * 参数一: UINavigationBar
 * 参数二: UINavigationItem
 */
- (void)navigationBar:(UINavigationBar *)navigationBar didPushItem:(UINavigationItem *)item;
```

> 注: 如果在Push的过程中有动画效果, 则该代理方法在动画结束后调用

##### 是否允许从UINavigationItem堆栈中Pop一个UINavigationItem

```objc
// 方法
/*
 * 参数一: UINavigationBar
 * 参数二: UINavigationItem
 */
- (BOOL)navigationBar:(UINavigationBar *)navigationBar shouldPopItem:(UINavigationItem *)item;
```

##### 已经从UINavigationItem堆栈中Pop一个UINavigationItem

```objc
// 方法
/*
 * 参数一: UINavigationBar
 * 参数二: UINavigationItem
 */
- (void)navigationBar:(UINavigationBar *)navigationBar didPopItem:(UINavigationItem *)item;
```

> 注: 如果在Pop的过程中有动画效果, 则该代理方法在动画结束后调用

# 附录A

在UINavigationController界面中, 如果前一个界面的UINavigationBar处于隐藏状态, 而后一个界面的UINavigationBar处于显示状态, 我们经常的处理方式可能是在前一个界面的UIViewController中写入如下代码

```objc
- (void)viewWillAppear:(BOOL)animated
{
    [super viewWillAppear:animated];
    
    [self.navigationController setNavigationBarHidden:YES];
}

- (void)viewWillDisappear:(BOOL)animated
{
    [super viewWillDisappear:animated];
    
    [self.navigationController setNavigationBarHidden:NO];
}
```

![附录A.1.gif]({{ site.baseurl }}/assets/iOS多控制器管理/附录A.1.gif)

利用上述方法得到的过渡结果并不尽如人意, 为了不让UINavigationBar发生闪动, 我们只需在设置隐藏状态时利用setNavigationBarHidden:animated:方法来设置即可

```objc
- (void)viewWillAppear:(BOOL)animated
{
    [super viewWillAppear:animated];
    
    [self.navigationController setNavigationBarHidden:YES animated:YES];
}

- (void)viewWillDisappear:(BOOL)animated
{
    [super viewWillDisappear:animated];
    
    [self.navigationController setNavigationBarHidden:NO animated:YES];
}
```

![附录A.2.gif]({{ site.baseurl }}/assets/iOS多控制器管理/附录A.2.gif)

# 附录B

自iOS7开始, 系统在UINavigationController中提供了"侧滑返回"功能, 即通过沿着屏幕侧面滑动可以进行Pop操作, 下面将对其实现过程进行分析

```objc
// 侧滑返回手势识别器
@property(nonatomic, readonly) UIGestureRecognizer *interactivePopGestureRecognizer;
```

首先, 我们打印该属性, 通过观察我们可以知道该手势识别器为UIScreenEdgePanGestureRecognizer(继承自UIPanGestureRecognizer)对象, 且其target为_UINavigationInteractiveTransition对象、action为handleNavigationTransition:方法

其次, 我们打印该属性的delegate, 通过观察我们可以知道该delegate与上文target地址相同, 即二者为同一个对象

最终, 通过分析我们可以知道, 当用户触发"侧滑返回"手势时, 该手势识别器就是调用了其delegate的handleNavigationTransition:方法来进行过渡处理

```objc
// interactivePopGestureRecognizer = <UIScreenEdgePanGestureRecognizer: 0x7fb1c17702f0; state = Possible; enabled = NO; delaysTouchesBegan = YES; view = <UILayoutContainerView 0x7fb1c1646280>; target= <(action=handleNavigationTransition:, target=<_UINavigationInteractiveTransition 0x7fb1c176fd40>)>>

// interactivePopGestureRecognizer.delegate = <_UINavigationInteractiveTransition: 0x7fb1c176fd40>
```

随着iOS设备屏幕越来越大, 侧滑返回对于手小的用户来说已经有些不方便, 这时便出现了全屏范围内滑动返回的需求, 该功能已经普遍用于网易新闻、手机QQ、新浪微博等大型APP中, 下面我们将进行该功能的实现

首先, 我们自定义UINavigationController的子类, 在该子类中获取到系统侧滑返回手势的信息

```objc
// 侧滑返回所在View
UIView *view = self.interactivePopGestureRecognizer.view;
// 侧滑返回target
id target = self.interactivePopGestureRecognizer.delegate;
// 侧滑返回action
SEL action = NSSelectorFromString(@"handleNavigationTransition:");
```

其次, 我们关闭系统侧滑返回手势识别器, 同时创建一个全屏侧滑手势识别器, 且其属性设置与interactivePopGestureRecognizer相同

```objc
self.interactivePopGestureRecognizer.enabled = NO;

UIPanGestureRecognizer *fullInteractivePopGestureRecognizer = [[UIPanGestureRecognizer alloc] initWithTarget:target action:action];
fullInteractivePopGestureRecognizer.delaysTouchesBegan = YES;
```

最终, 我们模仿系统实现方法, 完整代码如下所示

```objc
@interface LPNavigationController : UINavigationController

@property(nullable, nonatomic, readonly) UIGestureRecognizer *fullInteractivePopGestureRecognizer;

@end

@interface LPNavigationController () <UIGestureRecognizerDelegate>

@property(nullable, nonatomic, readwrite) UIGestureRecognizer *fullInteractivePopGestureRecognizer;

@end

@implementation LPNavigationController

- (void)viewDidLoad
{
    [super viewDidLoad];
    // Do any additional setup after loading the view.
    
    self.interactivePopGestureRecognizer.enabled = NO;
    
    UIView *view = self.interactivePopGestureRecognizer.view;
    id target = self.interactivePopGestureRecognizer.delegate;
    SEL action = NSSelectorFromString(@"handleNavigationTransition:");
    
    self.fullInteractivePopGestureRecognizer = [[UIPanGestureRecognizer alloc] initWithTarget:target action:action];
    self.fullInteractivePopGestureRecognizer.delaysTouchesBegan = YES;
    self.fullInteractivePopGestureRecognizer.delegate = self;
    [view addGestureRecognizer:self.fullInteractivePopGestureRecognizer];
}

- (BOOL)gestureRecognizerShouldBegin:(UIGestureRecognizer *)gestureRecognizer
{
    // 左滑时可能与UITableView左滑删除手势产生冲突
    CGPoint translation = [(UIPanGestureRecognizer *)gestureRecognizer translationInView:gestureRecognizer.view];
    if (translation.x <= 0)
    {
        return NO;
    }
    
    // 跟视图控制器不响应手势
    return ([self.viewControllers count] == 1) ? NO : YES;
}

@end
```

![附录B.1.gif]({{ site.baseurl }}/assets/iOS多控制器管理/附录B.1.gif)