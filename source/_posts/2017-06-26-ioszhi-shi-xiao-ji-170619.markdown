---
layout: post
title: "iOS知识小集-170619"
date: 2017-06-26 11:57:15 +0800
comments: true
categories: iOS
keywords: sxgfxm,Light's Blog,Image Slicing, TabBar Height, UIPageViewController
description: sxgfxm,Light's Blog,Image Slicing, TabBar Height, UIPageViewController
---

## Enable file sharing
Application Supports iTunes file sharing = YES

## Image Slicing
使用Xcode Image Slicing图片需为标准的PNG图片。

## TabBar
高度49

<!-- more -->

## UIPageViewController
子controller并不会被缩放，而是从上到下布局，可能被截断。  

```objective-c
-(void)setupPageController{
  self.pageController =
      [[UIPageViewController alloc] initWithTransitionStyle:UIPageViewControllerTransitionStyleScroll
                                      navigationOrientation:UIPageViewControllerNavigationOrientationVertical
                                                    options:options];
  self.pageController.view.frame = CGRectMake(0, 64, kScreen_Width, kScreen_Height - 64 - 49);
  self.pageController.dataSource = self;
  self.pages = @[ self.mainVC, self.bottomVC ];
  [self.pageController setViewControllers:@[ self.pages.firstObject ]
                                direction:UIPageViewControllerNavigationDirectionForward
                                 animated:YES
                               completion:nil];
  [self addChildViewController:self.pageController];
  [self.view addSubview:self.pageController.view];
}

#pragma mark - UIPageViewControllerDataSource
- (UIViewController *)pageViewController:(UIPageViewController *)pageViewController
      viewControllerBeforeViewController:(UIViewController *)viewController {
  if ([viewController isKindOfClass:[ViewController1 class]]) {
    return nil;
  }
  if ([viewController isKindOfClass:[ViewController2 class]]) {
    return self.pages[0];
  }
  return nil;
}

- (UIViewController *)pageViewController:(UIPageViewController *)pageViewController
       viewControllerAfterViewController:(UIViewController *)viewController {
  if ([viewController isKindOfClass:[ViewController1 class]]) {
    return self.pages[1];
  }
  if ([viewController isKindOfClass:[ViewController2 class]]) {
    return nil;
  }
  return nil;
}
```

