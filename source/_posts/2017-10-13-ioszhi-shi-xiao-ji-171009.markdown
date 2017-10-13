---
layout: post
title: "iOS知识小集-171009"
date: 2017-10-13 17:04:25 +0800
comments: true
categories: iOS
keywords: sxgfxm, contentInset, Snapshot CKComponent, Border && Shadow
description: sxgfxm, contentInset, Snapshot CKComponent, Border && Shadow
---

## NSInteger to NSData
```objective-c
  -(NSData*)dataWithInteger:(NSInteger)integer{
    return [NSData dataWithBytes:&integer length:sizeof(integer)];
  }
```

## UICollectionView contentInset
设置内容距离上下左右的间距。

<!-- more -->

## Snapshot CKComponent

```objective-c
#import "CKComponentSnapshotTestCase.h"

#import <ComponentKitTestHelpers/CKComponentLifecycleTestHelper.h>

const CKComponentLayout componentLayout = [component layoutThatFits:sizeRange parentSize:sizeRange.max];
CKComponentLifecycleTestHelper *componentLifecycleTestController = [[CKComponentLifecycleTestHelper alloc] initWithComponentProvider:nil sizeRangeProvider:nil];
[componentLifecycleTestController updateWithState:(CKComponentLifecycleTestHelperState){
  .componentLayout = componentLayout
}];
UIView *view = [[UIView alloc] initWithFrame:{0,0, componentLayout.size.width, componentLayout.size.height}];
[componentLifecycleTestController attachToView:view];
```

```objective-c
- (UIImage *)_imageForViewOrLayer:(id)viewOrLayer
{
  if ([viewOrLayer isKindOfClass:[UIView class]]) {
    if (_usesDrawViewHierarchyInRect) {
      return [UIImage fb_imageForView:viewOrLayer];
    } else {
      return [UIImage fb_imageForViewLayer:viewOrLayer];
    }
  } else if ([viewOrLayer isKindOfClass:[CALayer class]]) {
    return [UIImage fb_imageForLayer:viewOrLayer];
  } else {
    [NSException raise:@"Only UIView and CALayer classes can be snapshotted" format:@"%@", viewOrLayer];
  }
  return nil;
}
```

```objective-c
+ (UIImage *)fb_imageForView:(UIView *)view
{
  CGRect bounds = view.bounds;
  NSAssert1(CGRectGetWidth(bounds), @"Zero width for view %@", view);
  NSAssert1(CGRectGetHeight(bounds), @"Zero height for view %@", view);

  // If the input view is already a UIWindow, then just use that. Otherwise wrap in a window.
  UIWindow *window = [view isKindOfClass:[UIWindow class]] ? (UIWindow *)view : view.window;
  BOOL removeFromSuperview = NO;
  if (!window) {
    window = [[UIApplication sharedApplication] fb_strictKeyWindow];
  }

  if (!view.window && view != window) {
    [window addSubview:view];
    removeFromSuperview = YES;
  }

  UIGraphicsBeginImageContextWithOptions(bounds.size, NO, 0);
  [view layoutIfNeeded];
  [view drawViewHierarchyInRect:view.bounds afterScreenUpdates:YES];

  UIImage *snapshot = UIGraphicsGetImageFromCurrentImageContext();
  UIGraphicsEndImageContext();

  if (removeFromSuperview) {
    [view removeFromSuperview];
  }

  return snapshot;
}
```

## Border && Shadow
圆角可以和Border并存，Border属于bounds里面，不会被剪切掉；
圆角不能和Shadow并存，`maskToBounds`会剪切掉Shadow；