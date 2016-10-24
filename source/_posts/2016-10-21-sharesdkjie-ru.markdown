---
layout: post
title: "ShareSDK接入"
date: 2016-10-21 14:12:36 +0800
comments: true
categories: iOS
---

## iOS接入ShareSDK实现第三方分享

因为**友盟**被阿里收购在Android平台的各种令人不悦的后台操作，改用**ShareSDK**实现第三方分享。以下为简单接入流程。详情可参考官方文档<http://wiki.mob.com/ios%E7%AE%80%E6%B4%81%E7%89%88%E5%BF%AB%E9%80%9F%E9%9B%86%E6%88%90/>

## 获取AppKey

在官网注册，并按要求创建应用，获取AppKey，比如为**182947e7afac0**。

## 下载所需版本的ShareSDK

解压后，将**ShareSDK**导入工程中，记得勾选**Copy items into destination group's folder(if needed)**。

## 添加依赖库

```
//	ShareSDK
libicucore.dylib
libz.dylib
libstdc++.dylib
JavaScriptCore.framework

//	新浪微博SDK
ImageIO.framework
libsqlite3.dylib

//	QQ空间SDK
libsqlite3.dylib

//	微信SDK
libsqlite3.dylib

//	根据需要添加
```

## 初始化对应的第三方平台

在**AppDelegate.m**中，导入所需头文件。

```objective-c
#import <ShareSDK/ShareSDK.h>
#import <ShareSDKConnector/ShareSDKConnector.h>

//腾讯开放平台（对应QQ和QQ空间）SDK头文件
#import <TencentOpenAPI/TencentOAuth.h>
#import <TencentOpenAPI/QQApiInterface.h>

//微信SDK头文件
#import "WXApi.h"

//新浪微博SDK头文件
#import "WeiboSDK.h"
//新浪微博SDK需要在项目Build Settings中的Other Linker Flags添加"-ObjC"
```

在`application:didFinishLaunchingWithOptions:`方法中初始化。

```objective-c
- (BOOL)application:(UIApplication *)application
    didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

  /**
   *  设置ShareSDK的appKey，如果尚未在ShareSDK官网注册过App，请移步到http://mob.com/login
   * 登录后台进行应用注册
   *  在将生成的AppKey传入到此方法中。
   *  方法中的第二个第三个参数为需要连接社交平台SDK时触发，
   *  在此事件中写入连接代码。第四个参数则为配置本地社交平台时触发，根据返回的平台类型来配置平台信息。
   *  如果您使用的时服务端托管平台信息时，第二、四项参数可以传入nil，第三项参数则根据服务端托管平台来决定要连接的社交SDK。
   */
  [ShareSDK registerApp:@"182947e7afac0"

      activePlatforms:@[
        @(SSDKPlatformTypeSinaWeibo),
        @(SSDKPlatformTypeWechat),
        @(SSDKPlatformTypeQQ),
      ]
      onImport:^(SSDKPlatformType platformType) {
        switch (platformType) {
        case SSDKPlatformTypeWechat:
          [ShareSDKConnector connectWeChat:[WXApi class]];
          break;
        case SSDKPlatformTypeQQ:
          [ShareSDKConnector connectQQ:[QQApiInterface class]
                     tencentOAuthClass:[TencentOAuth class]];
          break;
        case SSDKPlatformTypeSinaWeibo:
          [ShareSDKConnector connectWeibo:[WeiboSDK class]];
          break;
        default:
          break;
        }
      }
      onConfiguration:^(SSDKPlatformType platformType,
                        NSMutableDictionary *appInfo) {

        switch (platformType) {
        case SSDKPlatformTypeSinaWeibo:
          //设置新浪微博应用信息,其中authType设置为使用SSO＋Web形式授权
          [appInfo
              SSDKSetupSinaWeiboByAppKey:@"3144028685"
                               appSecret:@"11abfe456bc8eefbab49fe7bbcd90bf0"
                             redirectUri:@"http://www.sharesdk.cn"
                                authType:SSDKAuthTypeBoth];
          break;
        case SSDKPlatformTypeWechat:
          [appInfo SSDKSetupWeChatByAppId:@"wx4868b35061f87885"
                                appSecret:@"64020361b8ec4c99936c0e3999a9f249"];
          break;
        case SSDKPlatformTypeQQ:
          [appInfo SSDKSetupQQByAppId:@"100371282"
                               appKey:@"aed9b0303e3ed1e27bae87c33761161d"
                             authType:SSDKAuthTypeBoth];
          break;
        default:
          break;
        }
      }];
  return YES;
}
```

接入不同的平台均需要注册并获取**AppKey**和**AppSecret**值。

## 添加分享实现代码

在**ViewController.m**中触发分享的方法中，添加分享代码。

```objective-c
- (void)shareAction {
  [self.tf endEditing:YES];
  // 1、创建分享参数
  NSArray *imageArray = @[ [UIImage imageNamed:@"junxi5.jpg"] ];
  if (imageArray) {

    NSMutableDictionary *shareParams = [NSMutableDictionary dictionary];
    [shareParams SSDKSetupShareParamsByText:self.tf.text
                                     images:imageArray
                                        url:nil
                                      title:@"Ticwatch Sport"
                                       type:SSDKContentTypeAuto];
    // 2、分享（可以弹出我们的分享菜单和编辑界面）
    [ShareSDK showShareActionSheet:nil
                             items:nil
                       shareParams:shareParams
               onShareStateChanged:^(
                   SSDKResponseState state, SSDKPlatformType platformType,
                   NSDictionary *userData, SSDKContentEntity *contentEntity,
                   NSError *error, BOOL end) {

                 switch (state) {
                 case SSDKResponseStateSuccess: {
                   NSLog(@"分享成功");
                   break;
                 }
                 case SSDKResponseStateFail: {
                   NSLog(@"分享失败");
                   NSLog(@"%@", error);
                   break;
                 }
                 default:
                   break;
                 }
               }];
  }
}
```

## 添加URL Schemes

 ![urlscheme](http://ofj92itlz.bkt.clouddn.com/ShareSDK:WhiteList.jpeg)

## 适配iOS 9问题

### 退回http协议，并设置域

![http](http://ofj92itlz.bkt.clouddn.com/ShareSDK:Https.jpeg)

### 添加scheme白名单

![白名单](http://ofj92itlz.bkt.clouddn.com/ShareSDK:UrlScheme.jpeg)

## 分享平台title为英文问题

默认语言为英文，需要在项目中添加中文本地化。

![localization](http://ofj92itlz.bkt.clouddn.com/ShareSDK:Localization.jpeg)

如需修改标题可以修改对应的本地化文件。

 ![customLocalization](http://ofj92itlz.bkt.clouddn.com/ShareSDK:CustomLocalization.jpeg)

## Github源码

[ShareSDKDemo](https://github.com/sxgfxm/ShareSDKDemo)

