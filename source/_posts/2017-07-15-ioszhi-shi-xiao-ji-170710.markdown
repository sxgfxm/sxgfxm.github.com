---
layout: post
title: "iOS知识小集-170710"
date: 2017-07-15 15:08:46 +0800
comments: true
categories: iOS
keywords: sxgfxm,Light's Blog, Lottie,navigationBar,NSKeyValueObservingOptions,Transition Animation,Reachability,UIKeyboardAppearance
description: sxgfxm,Light's Blog, Lottie,navigationBar,NSKeyValueObservingOptions,Transition Animation,Reachability,UIKeyboardAppearance
---

## [Lottie](https://github.com/airbnb/lottie-ios)
Lottie is a mobile library for Android and iOS that parses Adobe After Effects animations exported as json with bodymovin and renders the vector animations natively on mobile and through React Native!  
Common Animations  



```objective-c
LOTAnimationView *animation = [LOTAnimationView animationNamed:@"Lottie"];
[self.view addSubview:animation];
[animation playWithCompletion:^(BOOL animationFinished) {
  // Do Something
}];
```

<!-- more -->

UIViewController Transition  



```objective-c
- (id<UIViewControllerAnimatedTransitioning>)animationControllerForPresentedController:(UIViewController *)presented
                                                                  presentingController:(UIViewController *)presenting
                                                                      sourceController:(UIViewController *)source {
  LOTAnimationTransitionController *animationController = [[LOTAnimationTransitionController alloc] initWithAnimationNamed:@"vcTransition1"
                                                                                                          fromLayerNamed:@"outLayer"
                                                                                                            toLayerNamed:@"inLayer"];
  return animationController;
}

- (id<UIViewControllerAnimatedTransitioning>)animationControllerForDismissedController:(UIViewController *)dismissed {
  LOTAnimationTransitionController *animationController = [[LOTAnimationTransitionController alloc] initWithAnimationNamed:@"vcTransition2"
                                                                                                          fromLayerNamed:@"outLayer"
                                                                                                            toLayerNamed:@"inLayer"];
  return animationController;
}
```



## 导航栏设为透明
```objective-c
[self.navigationController.navigationBar setBackgroundImage:[UIImage new] forBarMetrics:UIBarMetricsDefault];
[self.navigationController.navigationBar setShadowImage:[UIImage new]];
```



## 导航栏还原
```objective-c
[self.navigationController.navigationBar setBackgroundImage:nil forBarMetrics:UIBarMetricsDefault];
[self.navigationController.navigationBar setShadowImage:nil];
```



## Change UITextField Placeholder Color
```objective-c
  NSString *string = @"请输入Wi-Fi名称";
  NSMutableAttributedString *attributedString = [[NSMutableAttributedString alloc] initWithString:string];
  [attributedString addAttribute:NSForegroundColorAttributeName value:[UIColor whiteColor] range:NSMakeRange(0, string.length)];
  self.wifiNameTF.attributedPlaceholder = attributedString;
```



## KVO NSKeyValueObservingOptions
NSKeyValueObservingOptionNew：获取新值  
NSKeyValueObservingOptionOld：获取旧值  
NSKeyValueObservingOptionInitial：获取初始值  
NSKeyValueObservingOptionPrior：获取新旧值  

## UITextField nil While Editing
```objective-c
- (BOOL)textField:(UITextField*)textField shouldChangeCharactersInRange:(NSRange)range replacementString:(NSString*)string
{
    if (range.location == 0 && string.length == 0)
    {
        //textField is empty
    }
    return YES;
}
```



## UIKeyboardAppearance

```objective-c
textField.keyboardAppearance = UIKeyboardAppearanceDark;
```



## UIView Transition Animation
```objective-c
[UIView transitionFromView:view1
                   toView:view2
                 duration:2
                  options:animationTransitionType
               completion:^(BOOL finished){
                            [view1 removeFromSuperview];
                          }];
[self.view addSubview:view2];
```



## UIView Fade In Fade Out
```objective-c
[view setAlpha:0.f];
[UIView animateWithDuration:2.f delay:0.f options:UIViewAnimationOptionCurveEaseIn animations:^{
    //  fade in
    [view setAlpha:1.f];
} completion:^(BOOL finished) {
    [UIView animateWithDuration:2.f delay:0.f options:UIViewAnimationOptionCurveEaseInOut animations:^{
        //  fade out
        [view setAlpha:0.f];
    } completion:nil];
}];
```



## Reachability
ReachableViaWiFi  



```objective-c
self.reachability = [Reachability reachabilityForInternetConnection];
[self.reachability startNotifier];
[self.reachability stopNotifier];
```

Wi-Fi Name  



```objective-c
+ (NSString *)currentWifiName {
  NSArray *ifs = (__bridge_transfer NSArray *)CNCopySupportedInterfaces();
  NSString *ifnam = [ifs firstObject];
  if (!ifnam) {
    return nil;
  }
  NSDictionary *info = (__bridge_transfer NSDictionary *)CNCopyCurrentNetworkInfo((__bridge CFStringRef)ifnam);
  return info[@"SSID"];
}
```



## NetworkExtension
Configure VPN tunnels. Customize and extend core networking features.
### Personal VPN
`NEVPNManager`: is used to create and manage VPN configurations and to control the resulting VPN tunnel connections.  
Non-Personal VPN configurations take precedence over Personal VPN configurations.  
Enable the "Personal VPN" capability for your app in Xcode.  

## UIViewController

## UINavigationController

## UITabBarController
