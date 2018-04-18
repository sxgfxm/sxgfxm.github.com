---
layout: post
title: "iOS知识小集-180319"
date: 2018-04-18 14:20:28 +0800
comments: true
categories: iOS
keywords: sxgfxm
description: Today Extentsion, NEVPNManager
---

## Today Extension 研究

### Layout
1. self.view.frame是整个屏幕大小。

### Life cycle
1. 滑走后，会重新创建。


## NEVPNManager
`NEVPNManager`是用来创建和管理VPN设置，并且处理VPN连接结果的。
每一个应用只允许创建一个VPA设置，所以`NEVPNManager`是一个单例。
由`NEVPNManager`创建的VPN称为私人VPN，在iOS和macOS上，非私人VPA的优先级高于私人VPN。
使用`NEVPNManager`需要`com.apple.developer.networking.vpn.api entitlement`，需要开启应用的`Personal VPN`权限。
在开启私人VPN前，必须从Network Extension preferences载入VPN设置，在修改VPN设置之后，必须写入Network Extension preferences。
`NEVPNManager`的对象是线程安全的。

`#import <NetworkExtension/NEVPNManager.h>`

## NEVPNProtocol
`NEVPNProtocol`用来配置VPN。

## NEVPNConnection
`NEVPNConnection`用来控制VPN连接。