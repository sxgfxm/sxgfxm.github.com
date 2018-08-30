---
layout: post
title: "iOS知识小集-180820"
date: 2018-08-30 10:24:16 +0800
comments: true
categories: iOS
keywords: sxgfxm,textView光标位置，超出父视图子视图手势响应，CALayer阴影，ARKit
description: iOS, textView光标位置，超出父视图子视图手势响应，CALayer阴影，ARKit
---

## 获取textview光标所在位置
```objective-c
- (NSUInteger)currentLocation:(UITextView*)textView{
  NSRange range = textView.selectionRange;
  if (range.location == NSNotFound) {
    return textView.text.length;
  }
  return range.location;
}
```

<!-- more -->

## 超出父视图的子视图响应手势事件
```objective-c
- (UIView *)hitTest:(CGPoint)point withEvent:(UIEvent *)event {
    UIView *view = [super hitTest:point withEvent:event];
    if (view == nil) {
        for (UIView *subView in self.subviews) {
            CGPoint tp = [subView convertPoint:point fromView:self];
            if (CGRectContainsPoint(subView.bounds, tp)) {
                view = subView;
            }
        }
    }
    return view;
}

- (BOOL)pointInside:(CGPoint)point withEvent:(UIEvent *)event{
    //if内的条件应该为，触摸点属于子视图范围
    if (.....){
     return YES;
    }
    return NO;
}
```

## CALayer阴影
默认`shadowOpacity = 0`，需要手动设置才能显示出来阴影。

## Macbook Pro触摸板突然失效
键盘检测出现问题，盒盖过一会儿再打开即可。

## ARKit

### 什么是AR
VR，Virtual Reality，虚拟现实，纯虚拟数字画面；  
AR，Augmented Reality，现实增强，裸眼现实 + 虚拟数字画面；  
MR，Mediated Reality，介导现实，数字化现实 + 虚拟数字画面；  

### 什么是ARKit
ARKit 是一个移动端 AR 平台，用于在 iOS 上开发增强现实 APP；  
ARKit 提供了接口简单的高级 API，有一系列强大的功能，从 iOS11 开始支持；  

### ARKit有哪些功能

#### 追踪
世界追踪；

#### 场景理解
平面识别，命中测试；

#### 渲染
整合任意渲染程序；

### 如何使用ARKit

#### ARSession
1、管理增强现实所有处理流程的类：可带参数配置，且可以开始处理流程或暂停处理流程或切换处理流程；  
2、重置追踪；  
3、获取处理结果ARFrame，通过代理或者直接获取currentFrame；  

#### ARSessionConfiguration

#### ARFrame
1、相机图像作为渲染场景背景；  
2、提供设备追踪信息，如设备角度、位置、追踪状态；  
3、提供场景理解，如特征点、空间物理位置及光线估算；  

#### ARAnchor
空间中先对真实世界的位置和角度。