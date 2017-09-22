---
layout: post
title: "iOS知识小集-170220"
date: 2017-03-06 16:06:34 +0800
comments: true
categories: iOS
keywords: sxgfxm,app life circle, background execution,system authorization
description: app life circle, background execution,system authorization
---

## App生命周期
**main.m**：app程序入口，将控制权交给UIKit framework。  
**UIApplication**：管理事件循环和高级行为，传递通知给代理。  
**App Delegate**：处理应用初始化，状态转换，等高级行为。每个应用都要有。  
**Data Model**：存储应用数据。  
**View Controller**：管理应用展示内容。  
**View**：展示内容。  
**Main Run Loop**：Main run loop由UIApplication在主线程中开启，保证用户操作串行执行。
用户操作 -> 操作系统 -> 端口 -> 事件队列 -> Main run loop -> App object -> Core object
-> 操作系统 -> 屏幕反馈。  
**执行状态**：  
- 关闭状态。  
- 未激活状态：在前台运行，但未接收到事件。  
- 激活状态：在前台运行，接收到事件。  
- 后台状态：在后台，且正在执行程序，通常会接着进入挂起状态。  
- 挂起状态：在后台，且没有执行程序。当内存不足时会清理挂起应用。  

<!--more-->

## 后台执行
**限时操作**：调用`beginBackgroundTaskWithName:expirationHandler:`或`beginBackgroundTaskWithExpirationHandler: `开启后台执行，在执行完成后必须调用`endBackgroundTask:`表示结束后台执行，否则程序会被终止。可以通过`application.backgroundTimeRemaining`查看剩余后台执行时间，一般为180秒。  

```objective-c
- (void)applicationDidEnterBackground:(UIApplication *)application
{
    bgTask = [application beginBackgroundTaskWithName:@"MyTask" expirationHandler:^{
        // Clean up any unfinished task business by marking where you
        // stopped or ending the task outright.
        [application endBackgroundTask:bgTask];
        bgTask = UIBackgroundTaskInvalid;
    }];

    // Start the long-running task and return immediately.
    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{

        // Do the work associated with the task, preferably in chunks.

        NSLog(@"begin %.f", application.backgroundTimeRemaining);
        [NSThread sleepForTimeInterval:10];
        NSLog(@"end %.f", application.backgroundTimeRemaining);

        [application endBackgroundTask:bgTask];
        bgTask = UIBackgroundTaskInvalid;
    });
}
```

**下载操作**：必须使用**NSURLSession**开启下载操作，需要通过**NSURLSessionConfiguration**进行设置。  

```objective-c
- (void)startDownloadTask {
  //  Configuration
  NSURLSessionConfiguration *config = [NSURLSessionConfiguration
      backgroundSessionConfigurationWithIdentifier:@"DownloadTask"];
  //  下载完成时唤醒应用
  config.sessionSendsLaunchEvents = YES;
  //  在前台时开启下载任务时有效
  config.discretionary = YES;
  //  NSURLSession
  NSURLSession *session = [NSURLSession sessionWithConfiguration:config];
  //  开启下载任务
  NSURLSessionTask *task =
      [session dataTaskWithURL:[NSURL URLWithString:@"www.baidu.com"]
             completionHandler:^(NSData *_Nullable data,
                                 NSURLResponse *_Nullable response,
                                 NSError *_Nullable error){

             }];
  //  当挂起时恢复任务
  [task resume];
}
```

**耗时操作**：只有特殊的耗时操作可以在后台被执行，且必须申请权限。如后台音乐播放、录音、定位信息更新、蓝牙连接、远程通知、语音服务等。

## ViewController生命周期

## 权限操作
iOS 10开始，获取**隐私**敏感数据需要在**plist.info**文件中配置，否则app会crash。  
配置方法：添加对应权限的key和value，value不许为空。  
常用权限：

- Network：无需添加key
- Location：Privacy - Location Always Usage Description
- Photo：Privacy - Photo Library Usage Description
- Camera：Privacy - Camera Usage Description
- Microphone：Privacy - Microphone Usage Description
- Contact：Privacy - Contacts Usage Description
- Media：Privacy - Media Library Usage Description

手动请求权限：当用户拒绝授权某权限时，需要手动再次请求。  
跳转至权限设置界面：  

```objective-c
[[UIApplication sharedApplication]
                openURL:[NSURL URLWithString:UIApplicationOpenSettingsURLString]
                options:@{}
      completionHandler:nil];
```

[Demo地址](https://github.com/sxgfxm/AuthorizationDemo)

## 文件操作

- NSString I/O
- NSArray I/O
- NSDictionary I/O
- NSData I/O
- NSObject I/O
- NSFileManager
- NSFileHandle

[Demo地址](https://github.com/sxgfxm/FileOperationDemo)

## weakself
作用：**防止循环引用**。  
声明：`__weak typeof(self) weakself = self;`。  
使用：
1. 是不是所有的block中都需要使用weakself？  
   Masonry自动约束中无需使用weakself。
2. 详细解释循环引用及防止方法？

## 单元测试
> If your code isn’t easy to test, it’s not going to be easy to maintain or debug.

验证某个类的某种行为在某种上下文中能得到预期结果。  
保证每个测试用例所针对的仅仅是一个基本单元，而不是一个有很多复杂依赖的综合行为。  
依赖于抽象而不是具体实现细节。  
不应关注于**测试**，而应关注**行为**。测试对象的行为方式。  
降低未来的变化所带来的成本。  
**功能检测**  
**依赖提取**  
**依赖注入**  
**行为方式**  
1. 单元测试测什么？  
   方法？行为？  
2. 如何处理依赖关系？  
   Mock？Stub？  
3. 单元测试的优缺点？  
   优点：验证模块的功能正确，行为正确，模块化编程，尽早发现问题，检验修改，重构；  
   缺点：增加代码量，写好测试不容易，不能保证不出错，考虑后期维护，尽量减少源代码耦合。  
4. 测试库？  
   [OCMockito](https://github.com/jonreid/OCMockito)  
5. 断言？  
   同步断言？异步断言（block，代理）？  

## A/B Test

## 同步策略

## JavaScriptCore
**JSContext**：是运行JavaScript代码的环境。  
**JSValue**：JSContext的运行结果封装在JSValue中。  
OC -> JavaScript：**SubscriptValue**、**CallFunction**，**HandleException**。  
JavaScript -> OC：**Block**或**JSExport**。
