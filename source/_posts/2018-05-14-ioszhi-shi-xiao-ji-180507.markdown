---
layout: post
title: "iOS知识小集-180507"
date: 2018-05-14 10:21:18 +0800
comments: true
categories: iOS
keywords: sxgfxm, WXApi, proto
description: sxgfxm, WXApi, proto
---

## WXApi无法分享
原因：WXApi针对不同分享内容有各种限制因素，必须符合要求才能分享。实际运用中可能影响分享的主要因素是thumbData的大小。
WXMediaMessage
  thumbData大小不能超过32k

可以通过压缩图片来达到WXApi的要求。

## 添加proto文件至工程
1. 把proto文件拖入工程目录中；
2. 在 **Compile Sources** 中添加对应proto文件，否则在link时会出现`symbol(s) not found for architecture arm64`错误，无法找到对应的`.o`文件；
3. 编译获得proto对应的类文件。