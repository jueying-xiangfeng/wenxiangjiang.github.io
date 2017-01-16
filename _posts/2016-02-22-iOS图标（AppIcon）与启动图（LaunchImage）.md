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

图标（AppIcon）与启动图（LaunchImage）是开发iOS应用程序必不可少的内容，但是在网络上对于这部分的内容讲解的并不详细，所以花些时间写了这篇文章，希望有需要的朋友可以随时查看

想知道AppIcon与LaunchImage的原理，必须对iOS设备的发展有一个简单的了解，所以我们先来看一看iPhone系列和iPad系列发展史

### iPhone发展史
| 设备 | 年份 | 尺寸 | 像素 | 点数 | 系统 |
| :-----: | :----: | :----: | :----: | :----: | :----: |
| iPhone4 | 2010年 | 3.5 | 640*960 | 320*480 | iOS4-iOS7 |
| iPhone4S | 2011年 | 3.5 | 640*960 | 320*480 | iOS5-iOS9 |
| iPhone5 | 2012年 | 4.0 | 640*1136 | 320*568 | iOS6-iOS9 |
| iPhone5C | 2013年 | 4.0 | 640*1136 | 320*568 | iOS7-iOS9 |
| iPhone5S | 2013年 | 4.0 | 640*1136 | 320*568 | iOS7-iOS9 |
| iPhone6 | 2014年 | 4.7 | 750*1334 | 375*667 | iOS8-iOS9 |
| iPhone6 Plus | 2014年 | 5.5 | 1242*2208 | 414*736 | iOS8-iOS9 |
| iPhone6S | 2015年 | 4.7 | 750*1334 | 375*667 | iOS9 |
| iPhone6S Plus | 2015年 | 5.5 | 1242*2208 | 414*736 | iOS9 |
| iPhoneSE | 2016年 | 4.0 | 750* 1334 | 375* 667 | iOS9 |

### iPad发展史

| 设备 | 年份 | 尺寸 | 像素 | 点数 | 系统 |
| :-----: | :----: | :----: | :----: | :----: | :----: |
| iPad | 2010年 | 9.7 | 768*1024 | 768*1024 | iOS3-iOS5 |
| iPad2 | 2011年 | 9.7 | 768*1024 | 768*1024 | iOS4-iOS9 |
| iPad3 | 2012年 | 9.7 | 1536*2048 | 768*1024 | iOS5-iOS9 |
| iPad4 | 2012年 | 9.7 | 1536*2048 | 768*1024 | iOS6-iOS9 |
| iPad mini | 2012年 | 7.9 | 768*1024 | 768*1024 | iOS6-iOS9 |
| iPad Air | 2013年 | 9.7 | 1536*2048 | 768*1024 | iOS7-iOS9 |
| iPad mini2 | 2013年 | 7.9 | 1536*2048 | 768*1024 | iOS7-iOS9 |
| iPad Air2 | 2014年 | 9.7 | 1536*2048 | 768*1024 | iOS8-iOS9 |
| iPad mini3 | 2014年 | 7.9 | 1536*2048 | 768*1024 | iOS8-iOS9 |
| iPad Pro | 2015年 | 12.9 | 2048*2732 | 1024*1366 | iOS9 |
| iPad mini4 | 2015年 | 7.9 | 1536*2048 | 768*1024 | iOS9 |
| iPad Pro | 2016年 | 9.7 | 1536*2048 | 768*1024 | iOS9 |

从上面的内容可以看出，在苹果在iPhone4、iPad3、iPad mini2开始使用Retina屏幕，所以需要提供@2x的图片素材才能展示出来最细腻的效果（注：iPhone Plus系列需要使用@3x的图片素材），下面我们便谈一下AppIcon与LaunchImage的选择

### AppIcon选择

| 设备 | iOS5,iOS6 | iOS7-iOS9 |
| :-----: | :----: | :----: |
| iPhone APP | Icon57.png Icon57@2x.png | Icon60@2x.png Icon60@3x.png |
| iPhone Settings | Icon29.png Icon29@2x.png Icon29@3x.png | Icon29.png Icon29@2x.png Icon29@3x.png |
| iPhone Spotlight | Icon29.png Icon29@2x.png Icon29@3x.png | Icon40@2x.png Icon40@3x.png |
| iPad APP | Icon72.png Icon72@2x.png | Icon76.png Icon76@2x.png |
| iPad Settings | Icon29.png Icon29@2x.png | Icon29.png Icon29@2x.png |
| iPad Spotlight | Icon50.png Icon50@2x.png | Icon40.png Icon40@2x.png |
| iPad Pro APP | 无 | Icon83.5@2x.png |

![icon.png]({{ site.baseurl }}/assets/iOS基础技术/icon.png)

> 笔者注：demo内所使用的图片取自网络

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

![image.png]({{ site.baseurl }}/assets/iOS基础技术/launchimage.png)

> 笔者附带一个demo[下载地址](https://github.com/wenxiangjiang/iOS-AppIcon-LaunchImage.git)，大家可以直接将AppIcon.appiconset和LaunchImage.launchimage中的图片替换为自己的图片即可