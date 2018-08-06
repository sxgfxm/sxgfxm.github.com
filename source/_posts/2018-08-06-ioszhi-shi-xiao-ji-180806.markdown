---
layout: post
title: "iOS知识小集-180730"
date: 2018-08-06 11:50:21 +0800
comments: true
categories: iOS
keywords: sxgfxm,UIImpactFeedbackGenerator,OS_ACTIVITY_MODE,Today Extension
description: sxgfxm,UIImpactFeedbackGenerator,OS_ACTIVITY_MODE,Today Extension
---

## UIImpactFeedbackGenerator

```objective-c
- (UIImpactFeedbackGenerator *)impactFeedback{
  if (!_impactFeedback) {
    _impactFeedback = [[UIImpactFeedbackGenerator alloc] initWithStyle:UIImpactFeedbackStyleLight];
  }
  return _impactFeedback;
}

- (void)occurImpactFeedback{
  if (@available(iOS 10.0, *)) {
    [self.impactFeedback prepare];
    [self.impactFeedback impactOccurred];
  }
}
```

## XCode屏蔽控制台多余系统信息
`Edit Scheme` --> `Arguments` --> `Environment Variables` --> `OS_ACTIVITY_MODE = disable`    

<!-- more -->

## NSString查找重复子串

```objective-c
- (NSArray<NSTextCheckingResult*>*)rangesOfString:(NSString*)subString inString:(NSString*)string{
  NSString *parten = subString;
  NSError* error = NULL;
  NSRegularExpression *reg = [NSRegularExpression regularExpressionWithPattern:parten options:NSRegularExpressionCaseInsensitive error:&error];
  NSArray<NSTextCheckingResult*>* match = [reg matchesInString:string options:NSMatchingReportCompletion range:NSMakeRange(0, [string length])];
  return match;
}
```

## inherit! :search_paths
继承上述依赖。

## Today Extension

### 添加Today Extension Target
`Editor` --> `Add Target...` --> `Today Extension`

### podfile增加target
~~~
  target 'CardFlow' do
    inherit! :search_paths
  end
~~~

### Embedded binary's bundle identifier is not prefixed with the parent app's bundle identifier
修改bundle ID；  
修改scheme 参数；  

### disable bitcode
在today extension的`Build Settings`中禁用`bitcode`。

### 视图布局和展开，纯代码约束
需要运行一次主APP，才能显示出来today extension；  
在`info.plist`中删除NSExtensionMainStoryboard；  
在`info.plist`中添加NSExtensionPrincipalClass；  
添加支持展开`[self.extensionContext setWidgetLargestAvailableDisplayMode:NCWidgetDisplayModeExpanded];`并在`-widgetActiveDisplayModeDidChange:withMaximumSize:`方法中返回相应大小；  
header高度固定，折叠高度固定，展开高度有最大值限制；

### 跳转至主APP
1、在主APP`info`中添加URL Scheme入“TodayExtension”，并在`-application:openURL:sourceApplication:annotation:`判断跳转信息是否来自today extension；  
2、在today extension需要跳转到主APP添加  
`[self.extensionContext openURL:[NSURL URLWithString:@"TodayExtension://"] completionHandler:nil];`

### 与主APP共享数据
1、创建APP group，为主APP和widget的APP ID添加对应的组；  
2、为主APP和widget APP添加APP group capability；  
3、使用`NSUserDefault`，suiteName需和groupName一致，
`[[NSUserDefaults alloc] initWithSuiteName:@"group.com.mobvoi.One"]`  
4、使用`NSFileManager`，
`[[NSFileManager defaultManager] containerURLForSecurityApplicationGroupIdentifier:@"group.com.mobvoi.One"]`  
5、`[User Defaults] Couldn't read values in CFPrefsPlistSource`。  

### 与主APP共享文件
需要再次添加compile files。