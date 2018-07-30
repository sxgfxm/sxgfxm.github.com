---
layout: post
title: "iOS知识小集-180723"
date: 2018-07-30 11:10:55 +0800
comments: true
categories: iOS
keywords: sxgfxm, UITableView Delete Cell,UITableViewCell highlighted
description: sxgfxm, UITableView Delete Cell,UITableViewCell highlighted
---

## UITableView Delete Cell
调用`deleteRowsAtIndexPaths:withRowAnimation:`时界面出现诡异问题，删除一个cell之后无法继续删除下面的cell。  
暂时解决办法是在数据源中删除对应数据后，直接`reloadData`而不再调用上述方法。  

## UITableViewCell高亮效果
重写`-setHighlighted:animated:`方法，当highlighted时改变背景颜色，非highlighted时通过动画修改回去。  

```objective-c
- (void)setHighlighted:(BOOL)highlighted animated:(BOOL)animated{
  [super setHighlighted:highlighted animated:animated];
  [self xg_setHighlighted:highlighted animated:animated color:[UIColor lightGrayColor]];
}

- (void)xg_setHighlighted:(BOOL)highlighted animated:(BOOL)animated color:(UIColor *)highlightedColor{
  if (highlighted) {
    self.contentView.backgroundColor = highlightedColor;
  } else {
    [UIView animateWithDuration:0.25 delay:0 options:UIViewAnimationOptionCurveEaseInOut animations:^{
      self.contentView.backgroundColor = [UIColor whiteColor];
    } completion:nil];
  }
}
```



## Attempt to present UIViewController on UIViewController whose view is not in the window hierarchy
`viewDidLoad:`时不能present viewController，改写为`viewDidAppear:`时进行操作。