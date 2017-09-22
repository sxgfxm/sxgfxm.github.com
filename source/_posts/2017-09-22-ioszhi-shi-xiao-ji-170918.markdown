---
layout: post
title: "iOS知识小集-170918"
date: 2017-09-22 10:50:20 +0800
comments: true
categories: iOS
keywords: sxgfxm,UIButton Gestures,kCGBlendModeDestinationIn,interactivePopGestureRecognizer,canGoBack
description: sxgfxm,UIButton Gestures,kCGBlendModeDestinationIn,interactivePopGestureRecognizer,canGoBack
---

## UIButton Gestures
`UIControlEventTouchDown`：按下button；  
`UIControlEventTouchDragExit`：拖动至button范围（button四周有一定范围）外；  
`UIControlEventTouchDragEnter`：拖动至button范围（button四周有一定范围）内。  
`UIControlEventTouchUpInside`：在button内松手；  
`UIControlEventTouchUpOutside`：在button外松手；  
`UIControlEventTouchDragOutside`：在button外拖动。  
`UIControlEventTouchDragInside`：在button内拖动；  
`UIControlEventTouchCancel`：系统行为取消touch；  

<!-- more -->

## UIGraphics Image Context
参考[这篇文章](https://onevcat.com/2013/04/using-blending-in-ios/)。    

关于blend mode参数解释：    
```
R is the premultiplied result
S is the source color, and includes alpha
D is the destination color, and includes alpha
Ra, Sa, and Da are the alpha components of R, S, and D
```

```objective-c
//  UIImage+Tint.m

#import "UIImage+Tint.h"

@implementation UIImage (Tint)
- (UIImage *) imageWithTintColor:(UIColor *)tintColor
{
    return [self imageWithTintColor:tintColor blendMode:kCGBlendModeDestinationIn];
}

- (UIImage *) imageWithGradientTintColor:(UIColor *)tintColor
{
    return [self imageWithTintColor:tintColor blendMode:kCGBlendModeOverlay];
}

- (UIImage *) imageWithTintColor:(UIColor *)tintColor blendMode:(CGBlendMode)blendMode
{
    //We want to keep alpha, set opaque to NO; Use 0.0f for scale to use the scale factor of the device’s main screen.
    UIGraphicsBeginImageContextWithOptions(self.size, NO, 0.0f);
    [tintColor setFill];
    CGRect bounds = CGRectMake(0, 0, self.size.width, self.size.height);
    UIRectFill(bounds);

    //Draw the tinted image in context
    [self drawInRect:bounds blendMode:blendMode alpha:1.0f];

    if (blendMode != kCGBlendModeDestinationIn) {
        [self drawInRect:bounds blendMode:kCGBlendModeDestinationIn alpha:1.0f];
    }

    UIImage *tintedImage = UIGraphicsGetImageFromCurrentImageContext();
    UIGraphicsEndImageContext();

    return tintedImage;
}

@end
```

## 正五边形
绘制正五边形步骤：    

1. 初始化数据；
2. 计算各个顶点坐标，18° + 54° = 72°，通过三角函数计算；
3. 使用UIBezierPath绘制五边形path，使用CAShapeLayer渲染五边形；

注：可使用matrix优化缩放。

## 返回手势

1. 如果无leftBarButtonItem，点击返回及右滑返回均系统支持；
2. 如果有leftBarButtonItem，点击返回逻辑需自己添加，右滑返回默认不支持。如果想支持右滑返回，需要设置`navigationController.interactivePopGestureRecognizer.delegate`，并实现`-gestureRecognizerShouldBegin`代理方法。注意右滑时需要判断当前栈中是否有足够的viewController以供pop。

## View与View的参数分离
自定义view时，将参数与view本身分离，并用常量定义参数，节省代码量，且易于统一修改处理。

## UIWebView
如果webView加载的内容被卡住，`[self.webView canGoBack]`不能立即返回，如果使用该方法判断是否可以pop页面，可能会造成卡顿。