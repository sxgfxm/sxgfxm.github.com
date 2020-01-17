---
layout: post
title: "iOS知识小集-201911"
date: 2020-01-17 12:03:37 +0800
comments: true
categories: iOS
keywords: sxgfxm
description: 
---

## 2019.11.10
xcode 11.2 无法上传APP至商店，需要使用 xcode 11.2.1 GM 版本上传。  

## 2019.11.11
使用`GCDAsyncSocket`实现APP间通信，需要server端先开启，然后client端去连接。  
可以在后台接收socket消息，但是如果被挂起后，将无法接收。  
可以通过静默推送唤醒后台挂起应用，然后处理socket消息。  

GATT 协议栈。  

## 2019.11.12
iOS 可以实现多个APP作为中心设备与同一个周边设备建立BLE连接。  
APP 可以获取当前正在连接的设备（retrieve），然后直接与之建立BLE连接。  

## 2019.11.13
[Transporter](https://apps.apple.com/cn/app/transporter/id1450874784?l=en&mt=12) 通过非命令行上传APP。  

`Google/SignIn`：3.0版本  
`GoogleSignIn`：4.0版本  