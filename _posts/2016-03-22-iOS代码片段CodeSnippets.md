---
layout:     post
title:      iOS代码片段CodeSnippets
excerpt:    ""
date:       2016-03-22 15:00:00
author:     "Liu Peng"
header-img: "assets/iOS基础技术/iOS代码片段CodeSnippets-bg.jpg"
tags:
    - iOS基础技术
---

在iOS开发过程中，苹果为开发者提供了代码片段功能来减少重复性工作，比如我们在Xcode中输入`for`则会提示出一个编译器预先设定好的代码片段
```objc
// 注：在Xcode中输入<#something#>，则会变成一个可以使用Tab来切换修改的区域，区域提示内容为something
for (<#initialization#>; <#condition#>; <#increment#>)
{
    <#statements#>
}
```
对于开发者而言，我们可以自己创建属于我们自己的代码片段，具体方式如下：
- 在Xcode中写好想要创建为代码片段的代码

```objc
for (int i = 0; i < <#count#>; i++)
{
    <#statements#>
}
```
- 使用鼠标选中上述代码片段，长按后拖到如下图区域中

![代码片段图1.png]({{ site.baseurl }}/assets/iOS基础技术/codesnippets1.png)

- 该新创建的代码片段可以进行如下编辑
  - Title：名称
  - Summary：描述
  - Platform：作用平台，如iOS、OS X等
  - Language：作用语言，如Objective-C
  - Completion Shortcut：呼出该代码片段的快捷键
  - Completion Scopes：作用区域

![代码片段图2.png]({{ site.baseurl }}/assets/iOS基础技术/codesnippets2.png)

- 编辑完代码片段即可在Xcode中使用快捷键呼出刚刚新创建的代码片段了

> 注：开发者自己创建的代码片段存储路径为~/Library/Developer/Xcode/UserData/CodeSnippets，适用于所有版本的Xcode开发软件共同使用

> 注：系统代码片段存储路径为/Applications/Xcode.app/Contents/Frameworks/IDEKit.framework/Versions/A/Resources/SystemCodeSnippets.codesnippets，仅适用于当前Xcode开发软件独立使用