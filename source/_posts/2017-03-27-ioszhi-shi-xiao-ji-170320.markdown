---
layout: post
title: "iOS知识小集-170320"
date: 2017-03-27 09:58:51 +0800
comments: true
categories: iOS
keywords: sxgfxm,UIActivityViewController,AVAssetExportSession,GCDWebServer
description: sxgfxm,UIActivityViewController,AVAssetExportSession,GCDWebServer
---

## iOS系统分享
需增加版本判断：`UIActivityTypeOpenInIBooks`iOS 9.0之后才有。

## iOS导出音乐
如何导出mp3：先按mov格式导出，再转为mp3。  
如何包含metadata信息：m4a格式自动包含metadata信息，mp3无。  
路径去除特殊字符。  

<!-- more -->

## GCDWebserver后台运行
设置option  



~~~
- (BOOL)startServer {
  for (int i = 0; i < 10; i++) {
    //  随机端口号
    NSInteger port = arc4random_uniform(64510) + 1025;
    NSDictionary *options = @{
      GCDWebServerOption_ConnectedStateCoalescingInterval : @(20),
      GCDWebServerOption_Port : @(port)
    };
    NSError *error = nil;
    if ([self.webServer startWithOptions:options error:&error]) {
      DDLogDebug(@"Start local server: %@", self.webServer.serverURL);
      return YES;
    }
  }
  return NO;
}
~~~
