---
layout: post
title: "iOS知识小集-180312"
date: 2018-03-18 18:19:33 +0800
comments: true
categories: iOS
keywords: sxgfxm, embed pods frameworks error, armv7、armv7s、arm64,Today Extension,NEVPNManager
description: sxgfxm, embed pods frameworks error, armv7、armv7s、arm64,Today Extension,NEVPNManager
---

## embed pods frameworks error
`rm -rf ~/Library/Developer/Xcode/DerivedData`

## armv7、armv7s、arm64、i386、x86_64指令集
armv7 | armv7s | arm64 是ARM处理器指令集；
i386 | x86_64 是Intel处理器指令集。

设备相关
arm64： iPhone6s | iPhone5s | iPad Air
armv7s：iPhone5 | iPhone5c | iPad4
armv7： iPhone4 | iPhone4s | iPad3

模拟器32位处理器：i386架构；
模拟器64位处理器：x86_64架构；
真机32位处理器：armv7或armv7架构；
真机64位处理器：arm64架构。

而编译出哪种指令集的包，将由 **Architectures** 与 **Valid Architectures**（因此这个不能为空）的交集来确定。
**Build Active Architecture Only** 指定是否只对当前连接设备所支持的指令集编译。

## Git拉取远程分支
`git checkout -b branch_name remote_branch_name`

## Today Extension 研究
1. self.view.frame是整个屏幕大小。
2. 滑走后，会重新创建。

## NEVPNManager
`#import <NetworkExtension/NEVPNManager.h>`