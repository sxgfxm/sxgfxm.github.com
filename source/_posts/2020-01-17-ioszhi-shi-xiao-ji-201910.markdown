---
layout: post
title: "iOS知识小集-201910"
date: 2020-01-17 12:02:44 +0800
comments: true
categories: iOS
keywords: sxgfxm
description: 
---

## 2019.10.09
同一个ViewController不能连续present两个ViewController。  
苹果的思维非同凡响，其实你只需要解散一个Modal View Controller就可以了。即处于最底层的View Controller，这样处于这个层之上的ModalView Controller统统会被解散。  

## 2019.10.21
`MPRemoteCommandCenter` 与 `AVAudioSession` 有关系。  
如果增加`AVAudioSessionCategoryOptionMixWithOthers`，远程控制会消失。  
I faced the same related issue. AVAudioSessionCategory should be mixwithothers, and you will no longer can update Lock Screen Control Center with info.  

## 2019.10.23
以16进制查看文件  
1、`vim`，`:%!xxd`转换为十六进制查看，`:%!xxd -r`转换为文本查看。  
2、`hexdump -C file | more`。  

## 2019.10.31
制定协议需要添加版本号。  

当找不出问题时，考虑是否会受上下文影响。  

BLE通知消息有Notify和Indicate两种。  

iPhone 7及以下机型蓝牙版本为4.2，iPhone 8及以上机型蓝牙版本为5.0。  

iPhone 6 BLE 发送数据发送间隔有最小限制30ms。  

## 2019.10.31
**Symbolic Breakpoint** 查找参数为nil的情况。  