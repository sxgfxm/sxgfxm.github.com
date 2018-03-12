---
layout: post
title: "iOS知识小集-180129"
date: 2018-03-12 15:28:49 +0800
comments: true
categories: iOS
keywords: sxgfxm, 圆角定制，NSMutableAttributedString crash
description: sxgfxm, 圆角定制，NSMutableAttributedString crash
---

## 圆角动画与圆角定制
~~~
  UIView *view = [[UIView alloc] initWithFrame:CGRectMake(100, 100, 100, 100)];
  view.backgroundColor = [UIColor redColor];
  view.layer.cornerRadius = 20;
  //  圆角定制
  view.layer.maskedCorners = kCALayerMinXMaxYCorner | kCALayerMaxXMaxYCorner;
  [self.view addSubview:view];
  //  圆角动画
  [UIView animateWithDuration:0.5 animations:^{
    view.layer.cornerRadius = 0;
  }];
~~~

## NSMutableAttributedString crash
~~~
- (void)testAttributedStringInitCrash
{
  NSString *nilStr = nil;
  NSAttributedString *attributedStr = [[NSAttributedString alloc] initWithString:nilStr];
}
~~~
Crash Log
~~~
Terminating app due to uncaught exception 'NSInvalidArgumentException', reason: 'NSConcreteAttributedString initWithString:: nil value'
~~~

~~~
- (void)testMutableAttributedStringInitCrash
{
  NSString *nilStr = nil;
  NSMutableAttributedString *attributedStr = [[NSMutableAttributedString alloc] initWithString:nilStr];
}
~~~
Crash Log
~~~
Terminating app due to uncaught exception 'NSInvalidArgumentException', reason: 'NSConcreteMutableAttributedString initWithString:: nil value'
~~~

~~~
- (void)testMutableAttributedStringAddAttributeCrash
{
  NSString *nonnullStr = @"str";
  NSMutableAttributedString *attributedStr = [[NSMutableAttributedString alloc] initWithString:nonnullStr];

  NSString *nilValue = nil;
  [attributedStr addAttribute:NSAttachmentAttributeName value:nilValue range:NSMakeRange(0, 1)];
}
~~~
Crash Log
~~~
Terminating app due to uncaught exception 'NSInvalidArgumentException', reason: 'NSConcreteMutableAttributedString addAttribute:value:range:: nil value'
~~~