---
layout: post
title: "iOS知识小集-180813"
date: 2018-08-20 11:43:27 +0800
comments: true
categories: iOS
keywords: sxgfxm, removedOnCompletion,fillMode,UILabel增加模糊文字阴影,UILabel逐行显示动画
description: sxgfxm, removedOnCompletion,fillMode,UILabel增加模糊文字阴影,UILabel逐行显示动画
---

## 动画结束后使layer保持动画后的状态
需要同时设置`animation.removedOnCompletion = NO`和`animation.fillMode = kCAFillModeForwards`。

<!-- more -->

## UILabel增加模糊文字阴影
`UILabel`的`shadowOffset`和`shadowColor`可以设置无模糊效果的阴影，如果需要设置模糊效果文字阴影，需要使用`NSAttributedString`增加`NSShadowAttributeName`阴影对应的参数。下面是UILabel设置阴影的一个Category方法：    
```objective-c
//  UILabel+Util.m
- (void)setText:(NSString *)text lineSpacing:(CGFloat)lineSpacing shadowColor:(UIColor *)shadowColor{
  if (!text) {
    self.text = text;
    return;
  }
  NSRange range = NSMakeRange(0, text.length);
  NSMutableAttributedString *attributedString = [[NSMutableAttributedString alloc] initWithString:text];
  //  font
  [attributedString addAttribute:NSFontAttributeName value:self.font range:range];
  //  color
  [attributedString addAttribute:NSForegroundColorAttributeName value:self.textColor range:range];
  //  shadow
  if (shadowColor) {
    NSShadow *shadow = [NSShadow new];
    shadow.shadowBlurRadius = 4;
    shadow.shadowOffset = CGSizeMake(0, 2);
    shadow.shadowColor = shadowColor;
    [attributedString addAttribute:NSShadowAttributeName value:shadow range:range];
  }
  //  style
  NSMutableParagraphStyle *paragraphStyle = [[NSMutableParagraphStyle alloc] init];
  [paragraphStyle setLineBreakMode:self.lineBreakMode];
  [paragraphStyle setAlignment:self.textAlignment];
  if (lineSpacing > 0) {
    [paragraphStyle setLineSpacing:lineSpacing];
  }
  [attributedString addAttribute:NSParagraphStyleAttributeName
                           value:paragraphStyle
                           range:range];
  //  attributed string
  self.attributedText = attributedString;
}
```

## UILabel逐行显示动画
增加一个Category方法使`UILabel`文字逐行显示，需要思想是为`UILabel`增加一个渐变的maskLayer，并用动画修改渐变的位置。    
```objective-c
//  UILabel+Util.m
- (void)startLineAnimationWithDuration:(CGFloat)duration{
  //  layout
  [self layoutIfNeeded];
  //  gradient layer
  CAGradientLayer *gradientLayer = [CAGradientLayer layer];
  gradientLayer.frame = self.bounds;
  gradientLayer.locations = @[@0, @0, @0, @1];
  gradientLayer.colors = @[(__bridge id)[UIColor whiteColor].CGColor,
                           (__bridge id)[UIColor whiteColor].CGColor,
                           (__bridge id)[UIColor clearColor].CGColor,
                           (__bridge id)[UIColor clearColor].CGColor,
                           ];
  gradientLayer.startPoint = CGPointMake(0, 0);
  gradientLayer.endPoint = CGPointMake(0, 1);
  //  set mask
  self.layer.mask = gradientLayer;
  //  animate locations
  CABasicAnimation *animation = [CABasicAnimation animationWithKeyPath:@"locations"];
  animation.fromValue = @[@0, @(-0.2), @0, @1];
  animation.toValue = @[@0, @1, @1.2, @1];
  animation.duration = duration;
  animation.timingFunction = [CAMediaTimingFunction functionWithName:@"easeInEaseOut"];
  animation.removedOnCompletion = NO;
  animation.fillMode = kCAFillModeForwards;
  //  add animation
  [gradientLayer addAnimation:animation forKey:@"animation"];
}
```

