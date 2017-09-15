---
layout: post
title: "iOS知识小集-170911"
date: 2017-09-15 17:20:49 +0800
comments: true
categories: iOS
keywords: sxgfxm
description: sxgfxm
---

## 长按语音控制

```
  [self.voiceBtn addTarget:self action:@selector(btnTouchBegin:) forControlEvents:UIControlEventTouchDown];
  [self.voiceBtn addTarget:self action:@selector(btnTouchEnd:) forControlEvents:UIControlEventTouchUpInside];
  [self.voiceBtn addTarget:self action:@selector(cancelSpeak) forControlEvents:UIControlEventTouchDragExit];
  [self.voiceBtn addTarget:self action:@selector(btnTouchEnd:) forControlEvents:UIControlEventTouchUpOutside];
  
```

