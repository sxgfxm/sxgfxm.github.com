---
layout: post
title: "iOS知识小集-170508"
date: 2017-05-17 16:52:43 +0800
comments: true
categories: iOS
keywords: sxgfxm,Carthage,Objective-C static vs external,NavigationBar
description: sxgfxm,Carthage,Objective-C static vs external,NavigationBar
---

## Carthage
安装：`brew install carthage`  
添加：`Cartfile`  
运行：`carthage update`  
添加：`Carthage/Build/.framework`  

<!-- more -->

## 防止重复push/present类型相同的controller
1. 找到最上层view controller；
2. 判断最上层view controller与待展示view controller是否类型相同；
3. 如果类型不同，才进行操作。
   获取最底层view controller：`[UIApplication sharedApplication].delegate.window.rootViewController`。  
   获取最上层view controller：  


~~~
+ (UIViewController *)topViewController {
  return [self
      topViewControllerWithRootViewController:[UIApplication sharedApplication].delegate.window.rootViewController];
}

+ (UIViewController *)topViewControllerWithRootViewController:(UIViewController *)rootViewController {
  NSLog(@"root to top vc:%@", NSStringFromClass([rootViewController class]));
  if ([rootViewController isKindOfClass:[MMDrawerController class]]) {
    MMDrawerController *mmdVc = (MMDrawerController *)rootViewController;
    return [self topViewControllerWithRootViewController:mmdVc.centerViewController];
  } else if ([rootViewController isKindOfClass:[UITabBarController class]]) {
    UITabBarController *tabBarController = (UITabBarController *)rootViewController;
    return [self topViewControllerWithRootViewController:tabBarController.selectedViewController];
  } else if ([rootViewController isKindOfClass:[UINavigationController class]]) {
    UINavigationController *navigationController = (UINavigationController *)rootViewController;
    return [self topViewControllerWithRootViewController:navigationController.visibleViewController];
  } else if (rootViewController.presentedViewController) {
    UIViewController *presentedViewController = rootViewController.presentedViewController;
    return [self topViewControllerWithRootViewController:presentedViewController];
  } else {
    return rootViewController;
  }
}
~~~

## Create Groups vs Create Folder References
Group：不创建文件夹，不能同名；   
Folder：创建文件夹，可以重名；  

## 拉伸Image
代码：`- (UIImage *)resizableImageWithCapInsets:(UIEdgeInsets)capInsets resizingMode:(UIImageResizingMode)resizingMode NS_AVAILABLE_IOS(6_0);`  
设置：Assets -> Attributes Inspector -> Slicing  

## 隐藏文件
显示隐藏文件：`defaults write com.apple.finder AppleShowAllFiles -bool true`  
隐藏隐藏文件：`defaults write com.apple.finder AppleShowAllFiles -bool false ` 
重启finder：`killall Finder`  

## NavigationBar & ScrollView
自动：`contentOffset.y == -64`  
手动：`self.edgesForExtendedLayout = UIRectEdgeNone;`，y轴0点下移64。  

## Objective-C static vs external
static: In C and Objective-C, a static variable or function at global scope means that that symbol has internal linkage.  
external: If you want to have a single global variable, you can't have it in class scope like in C++. One option is to create a global variable with external linkage: declare the variable with the extern keyword in a header file, and then in one source file, define it at global scope without the extern keyword.  
