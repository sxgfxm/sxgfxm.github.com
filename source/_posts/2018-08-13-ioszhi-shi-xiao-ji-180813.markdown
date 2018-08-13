---
layout: post
title: "iOS知识小集-180813"
date: 2018-08-13 14:46:12 +0800
comments: true
categories: iOS
keywords: sxgfxm，灰度发布，widget
description: sxgfxm，灰度发布，widget
---

# iOS知识小集-180806

## APP灰度发布
可以配置APP灰度发布。

## Today Extension

### scroll is disabled
Adding scrolls into a widget, both vertical and horizontal, is not possible. Or more precisely, adding a scroll view is possible but scrolling won’t work. Horizontal scrolling gesture in a scroll view in the Today extension will be intercepted by the notification center which will cause scrolling from Today to the Notification center. Scrolling vertically a scroll view inside a Today extension will be interrupted by scrolling the Today’s View.  
[A Tutorial on iOS 8 App Extensions](https://www.toptal.com/ios/ios-8-app-extensions)

### 数据共享
需要在主工程的entitlements中添加APP groups

### 资源共享
在today extension target中 copy bundle resources

### 高度
折叠高度110，展开高度最大值限制。

## Can't add self as subview
连续push两次会导致该问题。