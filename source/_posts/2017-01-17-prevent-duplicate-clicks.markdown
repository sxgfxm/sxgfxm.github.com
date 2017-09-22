---
layout: post
title: "Prevent duplicate clicks"
date: 2017-01-17 16:19:36 +0800
comments: true
categories: iOS
keywords: sxgfxm, duplicate clicks
description: Prevent duplicate clicks
---

在应用开发过程中，点击事件为耗时操作或者延时响应，例如请求服务器数据，push至下一个界面，如果不处理用户重复点击事件，将会重复触发事件。下面介绍几种简单的处理方法。

<!--more-->

##Button
点击后button状态置为disabled；
操作完成后button状态置为enabled；

##View
根据处理状态设置userInteractiveEnable。

##Push
方法一：
    点击后button状态置为disabled；
    viewDidDisappear置为enabled；

方法二：在push前添加判断：如果和上一个视图控制器一样，隔绝此次操作。    

~~~objective-c
if ([self.navigationController.topViewController isKindOfClass:[MyViewController class]]) {
    return;
}
~~~

##时间监听类
用一个静态变量记录上一次点击的时间，每次点击check时间间隔是否达到要求。

##Runtime
使用Runtime监听点击事件，忽略重复点击。
添加一个eventTimeInterval属性，使其规定时间内只能响应一次点击事件。

参考[iOS防止重复点击button](http://www.cnblogs.com/wanxudong/p/5984941.html)  

.h

```objective-c
#import <UIKit/UIKit.h>

@interface UIButton (WXD)

/**
*  为按钮添加点击间隔 eventTimeInterval秒
*/
@property (nonatomic, assign) NSTimeInterval eventTimeInterval;

@end
```

.m   

```objective-c
#import "UIButton+WXD.h"
#import <objc/runtime.h>
#define defaultInterval 1  //默认时间间隔

@interface UIButton ()

/**
*  bool YES 忽略点击事件   NO 允许点击事件
*/
@property (nonatomic, assign) BOOL isIgnoreEvent;

@end

@implementation UIButton (WXD)

static const char *UIControl_eventTimeInterval = "UIControl_eventTimeInterval";
static const char *UIControl_enventIsIgnoreEvent = "UIControl_enventIsIgnoreEvent";


// runtime 动态绑定 属性
- (void)setIsIgnoreEvent:(BOOL)isIgnoreEvent
{
    objc_setAssociatedObject(self, UIControl_enventIsIgnoreEvent, @(isIgnoreEvent), OBJC_ASSOCIATION_RETAIN_NONATOMIC);
}
- (BOOL)isIgnoreEvent{
    return [objc_getAssociatedObject(self, UIControl_enventIsIgnoreEvent) boolValue];
}

- (NSTimeInterval)eventTimeInterval
{
  return [objc_getAssociatedObject(self, UIControl_eventTimeInterval) doubleValue];
}

- (void)setEventTimeInterval:(NSTimeInterval)eventTimeInterval
{
 objc_setAssociatedObject(self, UIControl_eventTimeInterval, @(eventTimeInterval), OBJC_ASSOCIATION_RETAIN_NONATOMIC);
}

+ (void)load
{
  // Method Swizzling
 static dispatch_once_t onceToken;
 dispatch_once(&onceToken, ^{
       SEL selA = @selector(sendAction:to:forEvent:);
       SEL selB = @selector(_wxd_sendAction:to:forEvent:);
       Method methodA = class_getInstanceMethod(self,selA);
       Method methodB = class_getInstanceMethod(self, selB);

       BOOL isAdd = class_addMethod(self, selA, method_getImplementation(methodB), method_getTypeEncoding(methodB));

       if (isAdd) {
           class_replaceMethod(self, selB, method_getImplementation(methodA), method_getTypeEncoding(methodA));
       }else{
           //添加失败了 说明本类中有methodB的实现，此时只需要将methodA和methodB的IMP互换一下即可。
          method_exchangeImplementations(methodA, methodB);
      }
 });
}

- (void)_wxd_sendAction:(SEL)action to:(id)target forEvent:(UIEvent *)event
{
  self.eventTimeInterval = self.eventTimeInterval == 0 ? defaultInterval : self.eventTimeInterval;
  if (self.isIgnoreEvent){
      return;
  }else if (self.eventTimeInterval > 0){
      dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(self.eventTimeInterval * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
       [self setIsIgnoreEvent:NO];
       });
  }

  self.isIgnoreEvent = YES;
  // 这里看上去会陷入递归调用死循环，但在运行期此方法是和sendAction:to:forEvent:互换的，相当于执行sendAction:to:forEvent:方法，所以并不会陷入死循环。
  [self _wxd_sendAction:action to:target forEvent:event];
}  
```


