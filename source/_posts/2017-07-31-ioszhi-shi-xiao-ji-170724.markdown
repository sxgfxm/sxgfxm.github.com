---
layout: post
title: "iOS知识小集-170724"
date: 2017-07-31 10:17:32 +0800
comments: true
categories: iOS
keywords: sxgfxm, Light's Blog, JavaScriptCore, contentMode,setImageEdgeInsets,Reachability
description: sxgfxm, JavaScriptCore, contentMode,setImageEdgeInsets,Reachability
---

## JavaScriptCore
```objective-c
[self.webView stringByEvaluatingJavaScriptFromString:@"document.getElementsByTagName('audio')[0].play()"];
```

<!-- more -->

## 调整button点击区域
```objective-c
self.playBtn.contentMode = UIViewContentModeScaleAspectFit;
self.closeBtn.contentMode = UIViewContentModeScaleAspectFit;
[self.playBtn setImageEdgeInsets:UIEdgeInsetsMake(0, 10, 0, 0)];
[self.closeBtn setImageEdgeInsets:UIEdgeInsetsMake(0, -10, 0, 0)];
```



## 判断网络状态
```objective-c
- (BOOL)connected {
  NetworkStatus internetStatus = [[Reachability reachabilityForInternetConnection] currentReachabilityStatus];
  return internetStatus != NotReachable;
}
```

