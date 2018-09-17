---
layout: post
title: "iOS知识小集-180910"
date: 2018-09-17 20:42:43 +0800
comments: true
categories: iOS
keywords: sxgfxm, sirikit
description: sxgfxm, sirikit
---

## Siri Kit

### Siri Kit作用
通过语音完成第三方应用功能，偏向于工具型操作。

### 实现机制
**Domain**：业务领域；  
**Intent**：领域中的任务或指令；  
语音识别 --> Domain / Intent --> 下发到已注册的Extension进行处理。  
接近固定形式的表述更容易被识别。  

### 集成
需要注意develop target系统版本问题。