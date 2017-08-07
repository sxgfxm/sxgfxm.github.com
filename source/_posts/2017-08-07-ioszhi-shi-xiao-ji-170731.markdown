---
layout: post
title: "iOS知识小集-170731"
date: 2017-08-07 10:03:41 +0800
comments: true
categories: iOS
keywords: sxgfxm,Light's Blog, UIImage Tint Color, Screen Brightness, cell.contentView, NSURLSession, Background Mode,Logging Malloc Stack
description: sxgfxm,Light's Blog, UIImage Tint Color, Screen Brightness, cell.contentView, NSURLSession, Background Mode,Logging Malloc Stack
---

## UIImage Tint Color
~~~
[cell.icon sd_setImageWithURL:[NSURL URLWithString:myURL] placeholderImage:[UIImage imageNamed:imageName] options:SDWebImageRefreshCached completed:^(UIImage *image, NSError *error, SDImageCacheType cacheType, NSURL *imageURL) {           
    cell.icon.image = [image imageWithRenderingMode:UIImageRenderingModeAlwaysTemplate];            
}];
~~~

## 调整屏幕亮度
~~~
[[UIScreen mainScreen] setBrightness:0.5];
~~~

## 保持屏幕常亮
~~~
[UIApplication sharedApplication].idleTimerDisabled = YES;
~~~

<!-- more -->

## cell.contentView VS cell

If you want to customize cells by simply adding additional views, you should add them to the content view so they will be positioned appropriately as the cell transitions into and out of editing mode.

## NSURLSession
~~~
NSURLSessionConfiguration *config = [NSURLSessionConfiguration defaultSessionConfiguration];
NSURLSession *session =
      [NSURLSession sessionWithConfiguration:config delegate:self delegateQueue:[NSOperationQueue mainQueue]];
[session dataTaskWithRequest:request];

- (void)URLSession:(NSURLSession *)session
                          task:(NSURLSessionTask *)task
    willPerformHTTPRedirection:(NSHTTPURLResponse *)response
                    newRequest:(NSURLRequest *)request
             completionHandler:(void (^)(NSURLRequest *_Nullable))completionHandler {
  NSLog(@"urlsession request %@", request.URL);
}
~~~

## 后台运行
Target -> Capabilities -> Background Modes -> Audio；  
在info.plist中 添加`Required background modes` = `App plays audio or streams audio/video using AirPlay`。  
如果启用后台录音，会有红色显示条。  

## 应用进入前台或后台通知
~~~
// app启动或者app从后台进入前台都会调用这个方法
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(applicationBecomeActive) name:UIApplicationDidBecomeActiveNotification object:nil];
// app从后台进入前台都会调用这个方法
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(applicationBecomeActive) name:UIApplicationWillEnterForegroundNotification object:nil];
// 添加检测app进入后台的观察者
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(applicationEnterBackground) name: UIApplicationDidEnterBackgroundNotification object:nil];
~~~

## Logging Malloc Stack
如果不关闭，会导致大量无法自动清除的系统log，占用巨大磁盘空间。  