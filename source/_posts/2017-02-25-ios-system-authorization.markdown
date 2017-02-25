---
layout: post
title: "iOS System Authorization"
date: 2017-02-25 14:17:43 +0800
comments: true
categories: iOS
keywords: sxgfxm, iOS authorization
description: iOS authorization
---

iOS 10开始，获取**隐私**敏感数据需要在**plist.info**文件中配置，否则app会crash。  
配置方法：添加对应权限的key和value，value不许为空。  
常用权限：

- Network：无需添加key
- Location：Privacy - Location Always Usage Description
- Photo：Privacy - Photo Library Usage Description
- Camera：Privacy - Camera Usage Description
- Microphone：Privacy - Microphone Usage Description
- Contact：Privacy - Contacts Usage Description
- Media：Privacy - Media Library Usage Description

手动请求权限：当用户拒绝授权某权限时，需要手动再次请求。  
跳转至权限设置界面：

~~~
[[UIApplication sharedApplication]
                openURL:[NSURL URLWithString:UIApplicationOpenSettingsURLString]
                options:@{}
      completionHandler:nil];
~~~

[Demo地址](https://github.com/sxgfxm/AuthorizationDemo)
