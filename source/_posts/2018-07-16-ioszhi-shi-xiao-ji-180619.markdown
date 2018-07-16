---
layout: post
title: "iOS知识小集-180619"
date: 2018-07-16 10:20:28 +0800
comments: true
categories: iOS
keywords: sxgfxm, localStorage.clear(), anchorPoint, 展开动画, 长按移动UITableViewCell
description: sxgfxm, localStorage.clear(), anchorPoint, 展开动画, 长按移动UITableViewCell
---

## 清除UIWebView JavaScript缓存
~~~objective-c
[webView stringByEvaluatingJavaScriptFromString:@"localStorage.clear();"];
~~~

## 类似网易新闻下拉刷新后的展开动画
修改`anchorPoint`为`(1, 0.5)`。   
```objective-c
- (void)spreadFromCenterAnimation{
  CGRect rectFrom = CGRectMake(kScreen_Width / 2, 300, 0, 60);
  CGRect rectTo = CGRectMake(0, 300, kScreen_Width, 60);
  self.testView = [[UIView alloc] initWithFrame:rectFrom];
  self.testView.backgroundColor = [UIColor blueColor];
  self.testView.layer.anchorPoint = CGPointMake(1, 0.5);
  [self.view addSubview:self.testView];
  [UIView animateWithDuration:1 animations:^{
    self.testView.frame = rectTo;
  } completion:^(BOOL finished) {
    self.testView.frame = rectFrom;
  }];
}
```

<!-- more -->

## 长按移动UITableViewCell
```objective-c
- (void)addLongPressGesture{
  UILongPressGestureRecognizer *longPress = [[UILongPressGestureRecognizer alloc] initWithTarget:self action:@selector(longPressGestureRecognized:)];
  [self.tableView addGestureRecognizer:longPress];
}

- (void)longPressGestureRecognized:(UILongPressGestureRecognizer*)longPress{
  //  关键点：通过长按手势获取点击位置，进而获取到对应的indexPath，从而操作对应的cell
  CGPoint location = [longPress locationInView:self.tableView];
  NSIndexPath *indexPath = [self.tableView indexPathForRowAtPoint:location];
  static UIView *snapshot = nil;
  static NSIndexPath *sourceIndexPath = nil;
  UIGestureRecognizerState state = longPress.state;
  switch (state) {
    case UIGestureRecognizerStateBegan:{
      if (indexPath) {
        //  移动开始时，截取对应的cell的snapshot，隐藏对应的cell
        sourceIndexPath = indexPath;
        UITableViewCell *cell = [self.tableView cellForRowAtIndexPath:indexPath];
        snapshot = [self customSnapshotFromView:cell];
        __block CGPoint center = cell.center;
        snapshot.center = center;
        snapshot.alpha = 0;
        [self.tableView addSubview:snapshot];
        [UIView animateWithDuration:0.25 animations:^{
          center.y = location.y;
          snapshot.center = center;
          snapshot.transform = CGAffineTransformMakeScale(1.05, 1.05);
          snapshot.alpha = 0.98;
          cell.alpha = 0;
          cell.hidden = YES;
        }];
      }
      break;
    }
    case UIGestureRecognizerStateChanged:{
      //  移动时，不断修改snapshot的位置，并交换数据和cell
      CGPoint center = snapshot.center;
      center.y = location.y;
      snapshot.center = center;
      if (indexPath && ![indexPath isEqual:sourceIndexPath]) {
        [self.dataSource exchangeObjectAtIndex:indexPath.row withObjectAtIndex:sourceIndexPath.row];
        [self.tableView moveRowAtIndexPath:sourceIndexPath toIndexPath:indexPath];
        sourceIndexPath = indexPath;
      }
      break;
    }
    default:{
      //  移动完成后，显示被移动的cell，移除snapshot
      UITableViewCell *cell = [self.tableView cellForRowAtIndexPath:sourceIndexPath];
      cell.alpha = 0;
      [UIView animateWithDuration:0.25 animations:^{
        snapshot.center = cell.center;
        snapshot.transform = CGAffineTransformIdentity;
        snapshot.alpha = 0.0;
        cell.alpha = 1.0;
      } completion:^(BOOL finished) {
        cell.hidden = NO;
        sourceIndexPath = nil;
        [snapshot removeFromSuperview];
        snapshot = nil;
      }];
      break;
    }
  }
}

- (UIView *)customSnapshotFromView:(UIView *)inputView {
  // Make an image from the input view.
  UIGraphicsBeginImageContextWithOptions(inputView.bounds.size, NO, 0);
  [inputView.layer renderInContext:UIGraphicsGetCurrentContext()];
  UIImage *image = UIGraphicsGetImageFromCurrentImageContext();
  UIGraphicsEndImageContext();
  // Create an image view.
  UIView *snapshot = [[UIImageView alloc] initWithImage:image];
  snapshot.layer.masksToBounds = NO;
  snapshot.layer.cornerRadius = 0.0;
  snapshot.layer.shadowOffset = CGSizeMake(-5.0, 0.0);
  snapshot.layer.shadowRadius = 5.0;
  snapshot.layer.shadowOpacity = 0.4;
  return snapshot;
}
```

