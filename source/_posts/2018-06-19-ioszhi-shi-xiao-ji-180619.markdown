---
layout: post
title: "iOS知识小集-180522"
date: 2018-06-19 17:49:55 +0800
comments: true
categories: iOS
keywords: sxgfxm, fsCachedData, CADisplayLink, autocorrectionType, load, initialize
description: sxgfxm, fsCachedData, CADisplayLink, autocorrectionType, load, initialize
---

## UITextView关闭输入修正
`textView.autocorrectionType = UITextAutocorrectionTypeNo`
`textFiled.autocorrectionType = UITextAutocorrectionTypeNo`

## NSDateFormatter
"yyyy-MM-dd HH:mm:ss"

## UIGraphicsBeginImageContextWithOptions
传入`opaque`参数值为`NO`时可以创建透明的图层。

## load方法、initialize方法
https://www.jianshu.com/p/872447c6dc3f

<!-- more -->

## UIFont to CFTypeRef
`CGFontRef cgFont = CGFontCreateWithFontName((CFStringRef)uiFont.fontName);`

## fsCachedData
在查看Cache文件夹下缓存时，发现fsCachedData文件夹过大，便查了下这个文件夹是如何生成的。  

在使用NSURLSession进行网络请求时可以设置缓存策略，可以参考[iOS开发之Alamofire源码解析前奏--NSURLSession全家桶](https://www.cnblogs.com/ludashi/p/5556088.html)。当设置缓存策略后，会在默认为"~/Library/Caches/[Boundle ID]/fsCachedData/"的目录下生成缓存文件。

## CADisplayLink

### 什么是CADisplayLink
`CADisplayLink`是一个定时器，其调用周期和屏幕刷新频率有关。每秒调用频率 = 60 / frameInterval。

### 适用范围
检测fps，绘制动画。

### 与NSTimer区别
`NSTimer`调用周期更加灵活，适用范围更广，但准确性不如`CADisplayLink`。