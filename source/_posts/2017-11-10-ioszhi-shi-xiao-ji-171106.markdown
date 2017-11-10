---
layout: post
title: "iOS知识小集-171106"
date: 2017-11-10 17:01:04 +0800
comments: true
categories: iOS
keywords: sxgfxm, UIPageViewController, UIScorllView, modalPresentationStyle
description: sxgfxm, UIPageViewController, UIScorllView, modalPresentationStyle
---

## UIViewController modalPresentationStyle
当present controller时，可以设置这个属性来控制present时的效果，可用于透明显示被遮盖的conroller，此情况下被遮盖的conroller不会调用viewDidDisappear。

<!-- more -->

## UIPageViewController vs. UIScrollView
两者都可添加controller，实现滑动切换conroller切换的效果。  
UIPageViewController支持子controller生命周期函数调用，添加到UIScrollView上的controller滑动时不会调用生命周期函数。  
UIPageViewController便于代码分离。  
UIPageViewController没有暴露bounces属性，无法通过系统API禁用，需要特殊操作才可以，这一点UIScrollView有优势。  
UIPageViewController滑动时获取的contentOffset不够准确，需要特殊处理，这一点UIScrollView有优势。  
特殊处理方法：   

```objective-c
//  设置UIPageViewController的scrollView的代理为self
for (id view in self.pageController.view.subviews) {
    if ([view isKindOfClass:[UIScrollView class]]) {
      UIScrollView *scrollView = (UIScrollView *)view;
      scrollView.delegate = self;
    }
 }
```

```objective-c
- (void)setOffset:(CGFloat)offset {
    _offset = offset;
    //  Do something with offsetX
}

- (void)scrollViewDidScroll:(UIScrollView *)scrollView {
    CGFloat width = scrollView.bounds.size.width;
    CGFloat offset = self.offset;
    NSInteger childCount = self.childControllers.count;
    // 将当前滑动显示的view 的坐标 => self.view 的坐标
    for (UIViewController *vc in self.childControllers) {
      CGPoint p = [vc.view convertPoint:CGPointMake(0, 0) toView:self.view];
      if (p.x > 0 && p.x < width) {
        NSInteger index = [self.childControllers indexOfObject:vc];
        offset = index * width - p.x;
      }
    }
    if (offset >= (childCount - 1) * width) {
      CGPoint p = [self.childControllers.lastObject.view convertPoint:CGPointMake(0, 0) toView:self.view];
      offset = (childCount - 1) * width - p.x;
    }
    self.offset = offset;
}

- (void)scrollViewDidEndDecelerating:(UIScrollView *)scrollView {
    CGFloat width = scrollView.bounds.size.width;
    NSInteger index = round(self.offset / width);
    if (index < 0) {
      index = self.childControllers.count - 1;
    }
    self.offset = index * width;
}
```

