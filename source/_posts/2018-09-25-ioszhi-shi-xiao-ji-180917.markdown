---
layout: post
title: "iOS知识小集-180917"
date: 2018-09-25 13:05:35 +0800
comments: true
categories: iOS
keywords: sxgfxm,imageOrientation,Playground running 卡死
description: sxgfxm,imageOrientation,Playground running 卡死
---

## 相机拍摄图片方向调整
```objective-c
  if (image.imageOrientation != UIImageOrientationUp) {
      UIGraphicsBeginImageContext(image.size);
      [image drawInRect:CGRectMake(0, 0, image.size.width, image.size.height)];
      image = UIGraphicsGetImageFromCurrentImageContext();
      UIGraphicsEndImageContext();
  }
```

## Playground running 卡死
1、修改platform为Mac OS；  
2、修改为手动执行；  
3、为手动执行增加快捷键；