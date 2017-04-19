---
layout:     post
title:      iOS图标（AppIcon）与启动图（LaunchImage）
excerpt:    "图标（AppIcon）与启动图（LaunchImage）是开发iOS应用程序必不可少的内容，但是在网络上对于这部分的内容讲解的并不详细，所以花些时间写了这篇文章，希望有需要的朋友可以随时查看"
date:       2016-02-22 23:00:00
author:     "Liu Peng"
header-img: "assets/iOS基础技术/iOS图标（AppIcon）与启动图（LaunchImage）-bg.jpg"
tags:
    - iOS基础技术
---

图标(AppIcon)与启动图(LaunchImage)是开发iOS应用程序必不可少的内容, 但是在网络上对于这部分的内容讲解的并不详细, 所以花些时间整理了本篇文章, 希望有需要的朋友可以随时查看

想知道AppIcon与LaunchImage的配置原理, 必须对iOS设备的发展有一个简单的了解, 所以我们先来看一看iPhone系列和iPad系列发展史

### iPhone发展史
| 设备 | 年份 | 尺寸 | 像素 | 点数 | ppi | 系统 |
| :-----: | :----: | :----: | :----: | :----: | :----: | :----: |
| iPhone | 2007年 | 3.5 | 320*480 | 320*480 | 163 | iOS-iOS3 |
| iPhone3G | 2008年 | 3.5 | 320*480 | 320*480 | 163 | iOS2-iOS4 |
| iPhone3GS | 2009年 | 3.5 | 320*480 | 320*480 | 163 | iOS3-iOS6 |
| iPhone4 | 2010年 | 3.5 | 640*960 | 320*480 | 326 | iOS4-iOS7 |
| iPhone4S | 2011年 | 3.5 | 640*960 | 320*480 | 326 | iOS5-iOS9 |
| iPhone5 | 2012年 | 4.0 | 640*1136 | 320*568 | 326 | iOS6-iOS10 |
| iPhone5C | 2013年 | 4.0 | 640*1136 | 320*568 | 326 | iOS7-iOS10 |
| iPhone5S | 2013年 | 4.0 | 640*1136 | 320*568 | 326 | iOS7-iOS10 |
| iPhone6 | 2014年 | 4.7 | 750*1334 | 375*667 | 326 | iOS8-iOS10 |
| iPhone6 Plus | 2014年 | 5.5 | 1242*2208 | 414*736 | 401 | iOS8-iOS10 |
| iPhone6S | 2015年 | 4.7 | 750*1334 | 375*667 | 326 | iOS9-iOS10 |
| iPhone6S Plus | 2015年 | 5.5 | 1242*2208 | 414*736 | 401 | iOS9-iOS10 |
| iPhoneSE | 2016年 | 4.0 | 640*1136 | 320*568 | 326 | iOS9-iOS10 |
| iPhone7 | 2016年 | 4.7 | 750*1334 | 375*667 | 326 | iOS10 |
| iPhone7 Plus | 2016年 | 5.5 | 1242*2208 | 414*736 | 401 | iOS10 |

> 注1: 表中的"尺寸"指的是屏幕对角线长度; 表中的"ppi"指的是每英寸像素数, 计算方式为对角线像素数/对角线长度

> 注2: 苹果5.5英寸设备的真实像素为"1080*1920", 表中像素是方便开发者的渲染像素, 这样开发者可以直接采用@3x图片即可(在开发中无须考虑真实像素)

### iPad发展史

| 设备 | 年份 | 尺寸 | 像素 | 点数 | ppi | 系统 |
| :-----: | :----: | :----: | :----: | :----: | :----: |
| iPad | 2010年 | 9.7 | 768*1024 | 768*1024 | 132 | iOS3-iOS5 |
| iPad2 | 2011年 | 9.7 | 768*1024 | 768*1024 | 132 | iOS4-iOS9 |
| iPad3 | 2012年 | 9.7 | 1536*2048 | 768*1024 | 264 | iOS5-iOS9 |
| iPad4 | 2012年 | 9.7 | 1536*2048 | 768*1024 | 264 | iOS6-iOS10 |
| iPad mini | 2012年 | 7.9 | 768*1024 | 768*1024 | 163 | iOS6-iOS9 |
| iPad Air | 2013年 | 9.7 | 1536*2048 | 768*1024 | 264 | iOS7-iOS10 |
| iPad mini2 | 2013年 | 7.9 | 1536*2048 | 768*1024 | 326 | iOS7-iOS10 |
| iPad Air2 | 2014年 | 9.7 | 1536*2048 | 768*1024 | 264 | iOS8-iOS10 |
| iPad mini3 | 2014年 | 7.9 | 1536*2048 | 768*1024 | 326 | iOS8-iOS10 |
| iPad Pro | 2015年 | 12.9 | 2048*2732 | 1024*1366 | 264 | iOS9-iOS10 |
| iPad mini4 | 2015年 | 7.9 | 1536*2048 | 768*1024 | 326 | iOS9-iOS10 |
| iPad Pro | 2016年 | 9.7 | 1536*2048 | 768*1024 | 264 | iOS9-iOS10 |
| iPad5 | 2017年 | 9.7 | 1536*2048 | 768*1024 | 264 | iOS10 |

从上面的内容可以看出, 在苹果在iPhone4、iPad3、iPad mini2开始使用Retina屏幕, 所以需要提供@2x的图片素材才能展示出来最细腻的效果（注: iPhone Plus系列需要使用@3x的图片素材）, 下面我们便谈一下AppIcon与LaunchImage的选择

### AppIcon选择

| 设备 | iOS5,iOS6 | iOS7-iOS10 |
| :-----: | :----: | :----: |
| iPhone APP | Icon-57.png Icon-57@2x.png | Icon-60@2x.png Icon-60@3x.png |
| iPhone Notification | 无 | Icon-20@2x.png Icon-20@3x.png |
| iPhone Settings | Icon-29.png Icon-29@2x.png | Icon-29@2x.png Icon-29@3x.png |
| iPhone Spotlight | Icon-29.png Icon-29@2x.png | Icon-40@2x.png Icon-40@3x.png |
| iPad APP | Icon-72.png Icon-72@2x.png | Icon-76.png Icon-76@2x.png |
| iPad Notification | 无 | Icon-20.png Icon-20@2x.png |
| iPad Settings | Icon-29.png Icon-29@2x.png | Icon-29.png Icon-29@2x.png |
| iPad Spotlight | Icon-50.png Icon-50@2x.png | Icon-40.png Icon-40@2x.png |
| iPad Pro APP | 无 | Icon-83.5@2x.png |

![icon.png]({{ site.baseurl }}/assets/iOS基础技术/icon.png)

### LaunchImage选择

| 设备 | iOS5,iOS6 | iOS7-iOS9/iOS8-iOS9 |
| :-----: | :----: | :----: |
| iPhone Portrait | LaunchImage-320-480.png LaunchImage-320-480@2x.png LaunchImage-320-568@2x.png | LaunchImage-320-480@2x.png LaunchImage-320-568@2x.png |
| iPhone HD 4.7 Portrait | 无 | LaunchImage-375-667@2x.png |
| iPhone HD 5.5 Portrait | 无 | LaunchImage-414-736@3x.png |
| iPhone HD 5.5 Landscape | 无 | LaunchImage-736-414@3x.png |
| iPad Portrait | LaunchImage-768-1024.png LaunchImage-768-1024@2x.png | LaunchImage-768-1024.png LaunchImage-768-1024@2x.png |
| iPad Landscape | LaunchImage-1024-768.png LaunchImage-1024-768@2x.png | LaunchImage-1024-768.png LaunchImage-1024-768@2x.png |
| iPad Portrait Without Status Bar | LaunchImage-768-1004.png LaunchImage-768-1004@2x.png | 无 |
| iPad Landscape Without Status Bar | LaunchImage-1024-748.png LaunchImage-1024-748@2x.png | 无 |
| iPad Pro Portrait | 无 | LaunchImage-1024-1366@2x.png |

![image.png]({{ site.baseurl }}/assets/iOS基础技术/launchimage.png)

> 笔者附带一个demo[下载地址](https://github.com/wenxiangjiang/iOS-AppIcon-LaunchImage.git), 大家可以直接将AppIcon.appiconset和LaunchImage.launchimage两个文件夹中的图片替换为自己的图片即可
