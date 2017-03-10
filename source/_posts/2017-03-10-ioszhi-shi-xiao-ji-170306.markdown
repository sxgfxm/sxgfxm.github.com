---
layout: post
title: "iOS知识小集-170306"
date: 2017-03-10 17:00:41 +0800
comments: true
categories: iOS
keywords: sxgfxm,Block,单例,Healthkit,UIPasteboard,发送本地通知
description: sxgfxm,Block,单例,Healthkit,UIPasteboard,发送本地通知
---

## 异步回调
1. 明确知道操作执行完成；  
2. 操作时间未知；  

<!-- more -->

## Block
1. 对block中的内容强引用；  
2. 循环引用，类A的强property类B的强property block强引用self；  
3. 单例不用管循环引用；  

## 内存划分
1. 全局区；  
2. 静态区；  
3. 堆区；  
4. 栈区；  

## 单例
1. 如何创建单例？  
2. 何时创建单例？  
3. 单例的特性？  

## NSOperationQueue

## HealthKit
功能：存放健康数据，自动合并；  
读：读某一类别健康数据；  
写：写某一类别健康数据；  
改：改自己写的健康数据。  
锁屏时无法读数据，保护用户数据安全。  

## UIPasteboard
将信息写到剪切板中。  



~~~
UIPasteboard *pasteboard = [UIPasteboard generalPasteboard];
[pasteboard setString:string];
~~~

## 发送本地通知
~~~
// 创建一个本地推送
UILocalNotification *notification = [UILocalNotification new];
if (notification != nil) {
  // 推送声音
  notification.soundName = UILocalNotificationDefaultSoundName;
  // 推送内容
  notification.alertBody = NSLocalizedString(@"stopWatch.timing", @"已将打点计时信息复制到剪切板");
  // 显示在icon上的红色圈中的数子
  notification.applicationIconBadgeNumber++;
  // 设置userinfo 方便在之后需要撤销的时候使用
  NSDictionary *info = [NSDictionary dictionaryWithObject:kStopWatchKey forKey:kNotificationKey];
  notification.userInfo = info;
  // 添加推送到UIApplication
  UIApplication *app = [UIApplication sharedApplication];
  [app scheduleLocalNotification:notification];
}
~~~

## string label
~~~
NSStringDrawingUsesLineFragmentOrigin | NSStringDrawingUsesFontLeading
ceil()
~~~

## 相册和相机
取消选择照片和选择没有照片是不同的回调。  
相片可以编辑，相机不可以编辑。  

## 类方法、类变量、单例

## 埋点
点击量；  
人次；  
存留；  
停留时间；  
