---
layout: post
title: "iOS知识小集-170605"
date: 2017-06-12 09:42:42 +0800
comments: true
categories: iOS
keywords: sxgfxm, fopen, font, DDLog
description: sxgfxm, fopen, font, DDLog
---

## fopen时路径名为日文时崩溃
路径中尽量不要出现特殊字符。  

<!-- more -->

## 显示 < 号
居然因为是字体原因    

```objective-c
      CGFloat width = kScaleFrom_iPhone6_Desgin(5);
      CAShapeLayer *shapeLayer = [CAShapeLayer layer];
      shapeLayer.frame = CGRectMake(CGRectGetMinX(textLayer.frame) - kScaleFrom_iPhone6_Desgin(9), 0, width, width);
      shapeLayer.position = CGPointMake(shapeLayer.position.x, point.y);
      UIBezierPath *path = [UIBezierPath bezierPath];
      [path moveToPoint:CGPointMake(0, width / 2)];
      [path addLineToPoint:CGPointMake(width, 0)];
      [path moveToPoint:CGPointMake(0, width / 2)];
      [path addLineToPoint:CGPointMake(width, width)];
      shapeLayer.path = path.CGPath;
      shapeLayer.strokeColor = UIColorFromRGBA(0xff959595).CGColor;
      shapeLayer.fillColor = [UIColor clearColor].CGColor;
      shapeLayer.lineWidth = 1;
      shapeLayer.lineCap = kCALineCapButt;
      shapeLayer.lineJoin = kCALineJoinMiter;
      [self.yAxisLayer addSublayer:shapeLayer];
```



## Register DDLog
```objective-c
  // Register DDLog
  [DDLog addLogger:[DDTTYLogger sharedInstance]];  // TTY = Xcode console
  [DDLog addLogger:[DDASLLogger sharedInstance]];  // ASL = File log
```

