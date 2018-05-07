---
layout: post
title: "iOS知识小集-180418"
date: 2018-05-07 15:52:23 +0800
comments: true
categories: iOS
keywords: sxgfxm, UISlider, YYModel
description: sxgfxm, UISlider, YYModel
---

## UISlider 自定义thumb大小
需继承`UISlider`并重写`- thumbRectForBounds:trackRect:value:`方法。

```objective-c
- (CGRect)thumbRectForBounds:(CGRect)bounds trackRect:(CGRect)rect value:(float)value{
  CGFloat customWidth = 20;
  CGFloat customHeight = 20;
  bounds = [super thumbRectForBounds:bounds trackRect:rect value:value];
  return CGRectMake(bounds.origin.x, bounds.origin.y, customWidth, customHeight);
}
```

<!-- more -->

## YYModel 自定义转换
以`NSDate`与`NSNumber`转换为例。
```objective-c
// JSON:
{
	"name":"Harry",
	"timestamp" : 1445534567
}

// Model:
@interface User
@property NSString *name;
@property NSDate *createdAt;
@end

@implementation User
- (BOOL)modelCustomTransformFromDictionary:(NSDictionary *)dic {
    NSNumber *timestamp = dic[@"timestamp"];
    if (![timestamp isKindOfClass:[NSNumber class]]) return NO;
    _createdAt = [NSDate dateWithTimeIntervalSince1970:timestamp.floatValue];
    return YES;
}
- (BOOL)modelCustomTransformToDictionary:(NSMutableDictionary *)dic {
    if (!_createdAt) return NO;
    dic[@"timestamp"] = @(n.timeIntervalSince1970);
    return YES;
}
@end
```

