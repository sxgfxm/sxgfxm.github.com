---
layout: post
title: "iOS知识小集-170703"
date: 2017-07-10 11:09:16 +0800
comments: true
categories: iOS
keywords: sxgfxm,Light's Blog,CAKeyFrameAnimation,aniamtionDidStop,removedOnCompletion,fillMode,CAAnimationGroup,UITabBar appearance
description: sxgfxm,Light's Blog,CAKeyFrameAnimation,aniamtionDidStop,removedOnCompletion,fillMode,CAAnimationGroup,UITabBar appearance
---

## CAKeyFrameAnimation
~~~
- (CAKeyframeAnimation *)circleShake {
  if (!_circleShake) {
    CGFloat baseX = self.circleMask.position.x;
    _circleShake = [CAKeyframeAnimation animationWithKeyPath:@"position.x"];
    _circleShake.keyTimes = @[
      @0,
      @(0.1 / 0.8),
      @(0.2 / 0.8),
      @(0.3 / 0.8),
      @(0.5 / 0.8),
      @(0.76 / 0.8)
    ];
    _circleShake.values = @[
      @(baseX),
      @(baseX - 11),
      @(baseX + 11),
      @(baseX - 11),
      @(baseX + 11),
      @(baseX)
    ];
    _circleShake.duration = 0.8;
    _circleShake.removedOnCompletion = YES;
    _circleShake.fillMode = kCAFillModeForwards;
  }
  return _circleShake;
}
~~~

<!-- more -->

## Identify CAAnimation within the aniamtionDidStop delegate
The animation is **copied** before being added to the layer, so any subsequent modifications to `anim` will have no affect unless it is added to another layer.  
Use `animationForKey:` to identify.  
Create one animation and add to multiple layers.  

## Apply changes when animation finished
~~~
animation.removedOnCompletion = NO;
animation.fillMode = kCAFillModeForwards;
~~~

## Resume changes when animation finished
~~~
animation.removedOnCompletion = NO;
animation.fillMode = kCAFillModeBackwards;
~~~

## Use CAAnimationGroup to run animations cocurrently
~~~
- (CAAnimationGroup *)circleBig {
  if (!_circleBig) {
    _circleBig = [CAAnimationGroup animation];
    CABasicAnimation *circleBig =
        [CABasicAnimation animationWithKeyPath:@"path"];
    circleBig.fromValue = (__bridge id)self.circleMask.path;
    circleBig.toValue =
        (__bridge id)[UIBezierPath bezierPathWithArcCenter:self.centerPoint
                                                    radius:12.5
                                                startAngle:0
                                                  endAngle:M_PI * 2
                                                 clockwise:YES]
            .CGPath;
    circleBig.duration = self.spreadDuration;
    circleBig.removedOnCompletion = NO;
    circleBig.fillMode = kCAFillModeForwards;
    circleBig.timingFunction = [CAMediaTimingFunction
        functionWithName:kCAMediaTimingFunctionEaseInEaseOut];
    CABasicAnimation *changeWidth =
        [CABasicAnimation animationWithKeyPath:@"lineWidth"];
    changeWidth.fromValue = @(self.circleMask.lineWidth);
    changeWidth.toValue = @(5);
    changeWidth.duration = self.spreadDuration;
    changeWidth.removedOnCompletion = NO;
    changeWidth.fillMode = kCAFillModeForwards;
    _circleBig.animations = @[ circleBig, changeWidth ];
  }
  return _circleBig;
}
~~~

## TabBar背景设为透明色
~~~
[[UITabBar appearance] setShadowImage:[WWImageUtil imageWithColor:[UIColor clearColor]]];
[[UITabBar appearance] setBackgroundImage:[WWImageUtil imageWithColor:[UIColor clearColor]]];
[UITabBar appearance].translucent = YES;
~~~

## TabBar去除黑线
~~~
[[UITabBar appearance] setClipsToBounds:YES];
~~~

## self.title
`self.title`：同时设置导航栏和tabBar的title；  
`self.navigationItem.title`：设置导航栏title；  
`self.tabBarItem.title`：设置tabBartitle。  

## InHouse 和 Debug配置不同
