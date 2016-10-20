---
layout: post
title: "Today Extension - Widget"
date: 2016-10-19 21:05:18 +0800
comments: true
categories: iOS
---

## Widget简介

从iOS 8开始，引入**widget**特性。可以在通知中心的**Today**栏中添加widget。widget可以简单理解为一个小的扩展程序，可以展示相关应用的简略信息，提供快捷访问等功能。

widget的概念在Android中已经十分成熟，iOS widget与自由度相当高的Android widget有显著的区别。iOS widget的刷新时间为展示widget时，而不是像Android widget时刻保持在后台，消耗系统资源。iOS widget只是作为很小的配角存在，Apple的中心思想还是希望开发者关注App本身的设计和性能。

尽管iOS widget是一种约束状态下的自由，但已经逐渐改变用户的交互行为。

## 创建Widget

1、创建工程；

2、为工程添加新**Target**，并选择**Today Extension**模板；

![step1](../images/widget/step1.jpeg) ![stept](../images/widget/stept.jpeg)

 ![step3](../images/widget/step3.jpeg)

3、改为纯代码创建界面，默认会创建**MainInterface.storyboard**设计界面。删除Info.plist文件中的字段，并添加**NSExtensionPrincipalClass**字段，设为对应的**TodayViewController**； ![step4](../images/widget/step4.jpeg)

4、在TodayViewController中创建界面。

## 点击Widget跳转至主应用

为widget添加跳转至主应用的快捷入口。

为widget添加一层透明UIView，并添加tap手势，当点击widget时，执行下面方法：

```objective-c
- (void)tapAction {
  [self.extensionContext openURL:[NSURL URLWithString:@"customScheme://url"]
               completionHandler:nil];
}
```

其中**customScheme**可以自定义为需要的名字，字符串整体一定要符合**URL**格式，否则无法跳转。

## 主应用响应跳转

tapAction方法发出打开URL请，系统会查找注册过相同scheme的应用，并对其通知。所以主应用要注册相同的scheme，并且实现对通知的响应。

首先，在主应用的**Info.plist**文件中，添加**URL types**字段，并最终添加对应的scheme。

![step5](../images/widget/step5.jpeg)

然后，在主应用**AppDelegate.m**中添加如下方法：

```objective-c
- (BOOL)application:(UIApplication *)application
            openURL:(NSURL *)url
  sourceApplication:(NSString *)sourceApplication
         annotation:(id)annotation {
  if ([[url scheme] isEqualToString:@"customScheme"]) {
    //	scheme相同，打开应用
    return YES;
  }
  //	scheme不同，退出
  return NO;
}
```

## 不同应用间跳转

大致过程与widget跳转至主应用相同，差别在于发送跳转请求的方法不同，如下：

```objective-c
- (void)jumpToApp2 {
  [[UIApplication sharedApplication]
      openURL:[NSURL URLWithString:[NSString stringWithFormat:@"customScheme://"
                                                              @"www.ticwath."
                                                              @"com?name=%@",
                                                              self.tf.text]]
      options:@{}
      completionHandler:^(BOOL success) {
        if (success) {
          NSLog(@"success");
        } else {
          NSLog(@"failed");
        }
      }];
}

```

一定要保持两者约定的scheme相同。跳转时传递的信息保存在**URL**中，按约定解析即可。

## Github源码

[WidgetDemo](https://github.com/sxgfxm/WidgetDemo/tree/master)

