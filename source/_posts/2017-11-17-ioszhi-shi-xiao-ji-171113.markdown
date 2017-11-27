---
layout: post
title: "iOS知识小集-171113"
date: 2017-11-17 17:28:01 +0800
comments: true
categories: iOS
keywords: sxgfxm, UIBaselineAdjustment,Generated duplicate UUIDs,AFNetworking 内存泄露,include vs. import vs. class, 下拉刷新
description: sxgfxm, UIBaselineAdjustment,Generated duplicate UUIDs,AFNetworking 内存泄露,include vs. import vs. class, 下拉刷新
---

## include vs. import vs. class

### include
C语言中引用头文件的语法，无法防止重复引用头文件。

### import
OC中引用头文件的语法，可以防止重复引用头文件，无法防止循环引用头文件。

### class
使用`@class`告知编译器有这样一个类，书写代码时不要报错，真正调用该类的方法时，再`#import`该类。
可以防止循环引用头文件。  

一般来讲，头文件中使用`@class`引用其他类，在源文件中`#import`该类。

<!-- more -->

## 目录结构
工程目录结构看出技术水平。  
高内聚，低耦合原则。

## Group vs. Folder
Group创建引用，并不会创建实际文件夹，方便工程内移动；  
Folder会创建实际文件夹，方便磁盘文件与工程文件对应；

## 下拉刷新
`-scrollViewDidScroll:`，下拉播放header view的动画；  
`-scrollViewDidEndDragging:willDecelerate:`，松手后请求数据；  
数据返回后切换到正在状态。  

## AFNetworking 内存泄露
如果在一个ViewController中发起网络请求，在数据返回之前退出ViewController，网络请求持有的两个block不会被释放。

## UIBaselineAdjustment
当文字缩放时的对齐方法：  
`UIBaselineAdjustmentNone`：top和top对齐；  
`UIBaselineAdjustmentAlignBaselines`：top和centerY对齐；  
`UIBaselineAdjustmentAlignCenters`：centerY和centerY对齐。  

## Generated duplicate UUIDs
解决CocoaPods 重复生成 UUID。  
在`Podfile`中添加`install! 'cocoapods', :deterministic_uuids => false`。