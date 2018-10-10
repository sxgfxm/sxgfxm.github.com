---
layout: post
title: "iOS知识小集-180924"
date: 2018-10-10 21:44:06 +0800
comments: true
categories: iOS
keywords: sxgfxm, UISearchBar, 非 Retina 显示器，升级到 Mojave 之后发现文字不清晰问题
description: sxgfxm, UISearchBar, 非 Retina 显示器，升级到 Mojave 之后发现文字不清晰问题
---

## 修改UISearchBar取消按钮颜色
```objective-c
- (void)willPresentSearchController:(UISearchController *)searchController{
  [[UIBarButtonItem appearanceWhenContainedInInstancesOfClasses:@[[UISearchBar class]]] setTitleTextAttributes:@{NSForegroundColorAttributeName:UIColorFromRGBA(0xff4a4a4a)} forState:UIControlStateNormal];
}
```

## 解决XCode10 引入非同一目录下头文件没有自动提示的问题
Xcode --> File --> Workspace Settings --> Build System --> Legacy Build System。  
需重新编译后生效。

## 非 Retina 显示器，升级到 Mojave 之后发现文字不清晰问题
如果你在用 “非 Retina” 的显示器，升级到 Mojave 之后发现文字不清晰了，是因为 Mojave 默认关闭了文字的次像素渲染，如果需要可以通过终端里输入这个命令重新打开：  
`sudo defaults write -g CGFontRenderingFontSmoothingDisabled -bool NO`。  
重新打开对应软件后生效。
