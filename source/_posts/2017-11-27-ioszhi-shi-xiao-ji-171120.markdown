---
layout: post
title: "iOS知识小集-171120"
date: 2017-11-27 11:25:53 +0800
comments: true
categories: iOS
keywords: sxgfxm,NSStrikethroughStyleAttributeName,NSBaselineOffsetAttributeName,preferredStatusBarStyle
description: sxgfxm,NSStrikethroughStyleAttributeName,NSBaselineOffsetAttributeName,preferredStatusBarStyle
---

## iOS 10.3之后删除线失效解决办法
iOS 10.3之前写法：  

```objective-c
[attr addAttributes:@{
    NSStrikethroughStyleAttributeName: @(NSUnderlineStyleSingle)
  }
              range:NSMakeRange(0, string.length)];
```

iOS 10.3之后写法：  

```objective-c
[attr addAttributes:@{
    NSStrikethroughStyleAttributeName: @(NSUnderlineStyleSingle),
    NSBaselineOffsetAttributeName: @(NSUnderlineStyleNone)
  }
              range:NSMakeRange(0, string.length)];
```

<!-- more -->

## 设置状态栏style
在`info.plist`文件中设置`View controller-based status bar appearance`为`YES`。  
在对应ViewController中添加下面的方法：  

```objective-c
- (UIStatusBarStyle)preferredStatusBarStyle {
  return UIStatusBarStyleLightContent;
}
```

