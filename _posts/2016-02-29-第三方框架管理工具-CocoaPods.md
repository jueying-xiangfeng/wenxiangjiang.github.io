---
layout:     post
title:      第三方框架管理工具-CocoaPods
excerpt:    "在iOS开发过程中，我们总是不可避免地使用一些第三方框架，比如网络框架AFNetworking，图片框架SDWebImage，刷新框架MJRefresh等，作者以前一直是直接在GitHub上将第三方框架zip压缩文件下载下来，然后将框架拖进自己的项目中使用，每次处理起来很麻烦。最近正在使用优秀的工具CocoaPods来优雅地管理第三方框架，特总结一下使用方法与大家分享"
date:       2016-02-29 17:00:00
author:     "Liu Peng"
header-img: "assets/iOS常用工具/第三方框架管理工具-CocoaPods-bg.jpg"
tags:
    - iOS常用工具
---

![图片来源于网络]({{ site.baseurl }}/assets/iOS常用工具/cocoapods.png)

在iOS开发过程中，我们总是不可避免地使用一些第三方框架，比如网络框架AFNetworking，图片框架SDWebImage，刷新框架MJRefresh等，作者以前一直是直接在GitHub上将第三方框架zip压缩文件下载下来，然后将框架拖进自己的项目中使用，每次处理起来很麻烦。最近正在使用优秀的工具CocoaPods来优雅地管理第三方框架，特总结一下使用方法与大家分享

- 打开终端，本文的操作都是在终端上进行

- 升级Gem到最新版本（使用sudo关键字是管理员权限，需要输入密码）

```objc
sudo gem update --system
```

> Gem是管理Ruby库和程序的标准包，它通过Ruby Gem源来查找、安装、升级和卸载软件包，MacOS自身支持Ruby环境

- 使用Gem安装CocoaPods

```objc
sudo gem install cocoapods
```

> 注意1：如果是大陆用户，可能会在这步安装失败，这是因为Gem默认查找的源为<https://rubygems.org/>,大陆用户可以选择使用VPN翻墙来完成安装，或者将源换为大陆可以访问的地址<https://ruby.taobao.org/>

> 注意2：源地址协议头为https，网上很多教程都是http，现在使用会报错Error fetching http://ruby.taobao.org/: bad response Not Found 404

- 更改Gem的源（这步仅限不能翻墙的大陆用户使用）

```objc
1. 移除默认源 gem sources --remove https://rubygems.org/
2. 添加替代源 gem sources -a https://ruby.taobao.org/
3. 查看Gem的源 gem sources -l
4. 再次尝试安装CocoaPods
```

- 初始化CocoaPods，并更新第三方框架信息到本地

```objc
pod setup
```

- 在使用过程中，可以使用如下指令更新第三方框架信息到本地

```objc
pod repo update
```

- 更改repo地址（这步仅限更新第三方框架不成功的用户使用）

```objc
1. 移除默认 pod repo remove master
2. 添加替代 pod repo add master https://gitcafe.com/akuandev/Specs.git
3. 再次尝试更新第三方框架信息
```

- 搜索第三方框架（以AFNetworking为例），CocoaPods只能帮我们管理能够搜索出来的第三方框架

```objc
pod search AFNetworking
```

- 使用CocoaPods为放置于桌面上的Demo项目添加AFNetworking

```objc
1. 将路径定位到Demo项目根目录 cd /Users/liupeng/Desktop/Demo
2. 创建文件Podfile来写入想要的框架 vim Podfile
3. 输入i则可编辑，首行输入platform :ios, '7.0' 下一行输入pod 'AFNetworking', '~> 3.0.4'
4. 键盘点击ESC退出编辑状态，输入:wq保存退出
5. 安装第三方框架 pod install
```

> 注意1：Podfile文件可以直接手动编辑，不使用终端也可以

> 注意2：第三方框架安装成功后，项目根目录会出现一个后缀为xcworkspace的工作空间，以后开发过程中都是要使用xcworkspace来代替原来的xcodeproj来打开项目

> 注意3：在项目中引入第三方框架时，不再使用#import ""而是使用#import <>

- 升级或卸载第三方框架（以AFNetworking为例）

```objc
1. 将路径定位到Demo项目根目录 cd /Users/liupeng/Desktop/Demo
2. 创建文件Podfile来写入想要的框架 vim Podfile
3. 输入i则可编辑，将pod 'AFNetworking', '~> 3.0.4'这行删除即可卸载，修改版本号即可升级，不写版本号默认会下载最新版本
4. 键盘点击ESC退出编辑状态，输入:wq保存退出
5. 安装第三方框架 pod install
```