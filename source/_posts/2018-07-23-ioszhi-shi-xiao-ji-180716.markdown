---
layout: post
title: "iOS知识小集-180716"
date: 2018-07-23 10:17:06 +0800
comments: true
categories: iOS
keywords: sxgfxm,UIScrollView+EmptyDataSet,Update CocoaPods,UIViewAlertForUnsatisfiableConstraints,Prefix Header,UIBarButtonItem
description: sxgfxm,UIScrollView+EmptyDataSet,Update CocoaPods,UIViewAlertForUnsatisfiableConstraints,Prefix Header,UIBarButtonItem
---

## UIScrollView+EmptyDataSet
当状态变化时才会触发；  
当列表内无元素时才会显示。

## Update CocoaPods
`[sudo] gem install cocoapods`

## UITableViewCell左滑删除出现后，TableView编辑状态失效
UITableViewCell需要按规则初始化。

## Masonry、Autolayout断点调试
1、添加symbolic break point`UIViewAlertForUnsatisfiableConstraints`；  
2、添加action`po [[UIWindow keyWindow] _autolayoutTrace]`；  
3、修改出错界面背景颜色`e id $myView = (id) 0x10a005a90`，`e (void)[$myView setBackgroundColor:[UIColor blueColor]]`。（0x10a005a90为问题界面地址）  

## 添加pch文件
1、创建`.pch`文件，引入需要全局引入的头文件；  
2、在`Build Settings`中`Prefix Header`添加pch文件路径`$SRCROOT/工程名/pch文件名`，并设置`Precompile Prefix Header = YES`。  
3、优点：全局导入头文件，不必重复导入；预编译头文件被缓存，提高编译速度；  
4、缺点：非必要全局引入的头文件会带来麻烦；  

<!-- more -->

## 导航栏相关

### UIBarButtonItem
1、非custom view创建的UIBarButtonItem会被`UINavigationBar.tintColor`渲染，可以根据需求确定；  
2、用custom view创建的UIBarButtonItem更加灵活，表现方式更加多样，且可以添加多种手势控制；  
3、`UIBarButtonSystemItemFixedSpace`UIBarButtonItem可以用于调整items间距；  
4、使用自定义返回按钮会使左滑退出失效，可设置`self.navigationController.interactivePopGestureRecognizer.delegate = self;`恢复；  

### UINavigationBar
1、`translucent = YES`半透明，不自动下移；`translucent = NO`不透明，自动下移，bar会变为白色；  
2、`barTintColor = nil`是透明的，`barTintColor = [UIColor clearColor]`是黑色的；  
3、`setBackgroundImage`会移除`UIVisualEffectView`，并且`barTintColor`失效；  
4、`tintColor`会渲染非custom view的UIBarButtonItem；  

### UINavigationController
1、Push至一个透明UIViewController会卡顿，初始动画瞬间进行，透明的话会看到，感觉卡顿；  