---
layout: post
title: "iOS知识小集-180514"
date: 2018-05-22 11:52:58 +0800
comments: true
categories: iOS
keywords: sxgfxm, GradientButton, UICollectionViewCell Select Effect, 绘制渐变颜色图片, Masonry Update Constrains
description: sxgfxm, GradientButton, UICollectionViewCell Select Effect, 绘制渐变颜色图片, Masonry Update Constrains
---

## GradientButton
创建渐变背景颜色的Button，如果直接添加CAGradientLayer，会覆盖Button的Image，所以需要创建一张渐变颜色的图片设置为Button的backgroundImage。

## UICollectionViewCell Select Effect
为UICollectionViewCell添加选中效果。  
方法一：  
重写Cell的`-setSelected:`方法，在这个方法中增加动画效果。  
The selected state is toggled when the user lifts up from a highlighted cell.
Override these methods to provide custom UI for a selected or highlighted state.  
方法二：  
在CollectionView回调方法`-collectionView:didHighlightItemAtIndexPath:`和`-collectionView:didUnhighlightItemAtIndexPath:`中添加动画效果。  
方法三：  
为CollectionViewCell添加`-performSelectAnimation`方法展示选中的动画效果，当`-collectionView:didSelectItemAtIndexPath:`时调用该方法。  

<!-- more -->

## 绘制渐变颜色图片
```objective-c
- (UIImage *)gradientImageFromColors:(NSArray *)colors startPoint:(CGPoint)startPoint endPoint:(CGPoint)endPoint frame:(CGRect)frame{
  NSMutableArray *mutColors = [NSMutableArray array];
  for (UIColor *color in colors) {
    [mutColors addObject:(id)color.CGColor];
  }
  UIGraphicsBeginImageContextWithOptions(frame.size, YES, 1);
  CGContextRef context = UIGraphicsGetCurrentContext();
  CGContextSaveGState(context);
  CGColorSpaceRef colorSpace = CGColorGetColorSpace([[colors lastObject] CGColor]);
  CGGradientRef gradient = CGGradientCreateWithColors(colorSpace, (CFArrayRef)mutColors, NULL);
  CGPoint start = startPoint;
  CGPoint end = endPoint;
  CGContextDrawLinearGradient(context, gradient, start, end,
                              kCGGradientDrawsBeforeStartLocation | kCGGradientDrawsAfterEndLocation);
  UIImage *image = UIGraphicsGetImageFromCurrentImageContext();
  CGGradientRelease(gradient);
  CGContextRestoreGState(context);
  CGColorSpaceRelease(colorSpace);
  UIGraphicsEndImageContext();
  return image;
}
```

## Masonry Update Constrains
使用Masonry更新约束时，需要更新已经存在的约束条件，不能`make top`却`update bottom`，这样约束会冲突报错。