---
layout: post
title: "iOS知识小集-170515"
date: 2017-05-17 16:57:00 +0800
comments: true
categories: iOS
keywords: sxgfxm,Get local bundle,custom cocoapods, Localized string in cocoapods,Image in cocoapods,Font in cocoapods
description: sxgfxm,Get local bundle,custom cocoapods, Localized string in cocoapods,Image in cocoapods,Font in cocoapods
---

## 更新cocoapods版本
正式版：`sudo gem install cocoapods`  
开发版：`sudo gem install cocoapods --pre  `

<!-- more -->

## Get local bundle
通过`frameworkName`和`bundleName`获取对应bundle。  



~~~c
+ (NSBundle *)getBundleWithFrameworkName:(NSString *)frameworkName bundleName:(NSString *)bundleName {
  NSString *tmpBundleName = [bundleName copy];
  if (![bundleName hasSuffix:@".bundle"]) {
    tmpBundleName = [NSString stringWithFormat:@"%@.bundle", tmpBundleName];
  }

  NSString *mainBundlePath = [[NSBundle mainBundle] resourcePath];
  NSString *bundlePath = [mainBundlePath stringByAppendingPathComponent:tmpBundleName];
  NSBundle *bundle = [NSBundle bundleWithPath:bundlePath];
  if (bundle) {
    return bundle;
  }

  NSString *tempFramework = [frameworkName copy];
  NSString *frameExtension = @".framework";
  if (![tempFramework hasSuffix:frameExtension]) {
    tempFramework = [tempFramework stringByAppendingString:frameExtension];
  }

  NSString *path = [[[NSBundle mainBundle] privateFrameworksPath] stringByAppendingPathComponent:tempFramework];
  return [NSBundle bundleWithPath:[path stringByAppendingPathComponent:tmpBundleName]];
}
~~~

## Localized string in cocoapods
在自定义pod中添加语言本地化：  
1. 为`MyPod`添加语言路径`MyPod/Languages/**/*`,pod中使用的不同语言的`.strings`存放在该目录下；  
2. 在`MyPod.podspec`文件中添加语言bundle：`s.resource_bundles = {'LanguageBundle' => ['MyPod/Languages/**/*']}`；  
3. 添加简便调用方法：    


~~~c
NSString *XGLocalizedString(NSString *key, NSString *comment) {
  return [XGFitnessLocalize localizedString:key];
}

+ (NSString *)localizedString:(NSString *)key {
  //  key == nil, return nil;
  if (!key) {
    return key;
  }
  //  system language
  NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
  NSArray *languages = [defaults objectForKey:kAppleLanguageKey];
  NSString *language = [languages firstObject];
  //  set default language
  NSString *fileNamePrefix = language;
  if (!([fileNamePrefix isEqualToString:kLanguageEnglish] ||
        [fileNamePrefix isEqualToString:kLanguageSimplifiedChinese] ||
        [fileNamePrefix isEqualToString:kLanguageTraditionalChinese])) {
    fileNamePrefix = kDefaultLanguage;
  }
  //  language bundle
  NSBundle *languageBundle =
      [WWFitnessBundleUtil getBundleWithFrameworkName:kFrameworkName bundleName:kLanguageBundleName];
  //  lproj bundle
  NSString *path = [languageBundle pathForResource:fileNamePrefix ofType:@"lproj"];
  NSBundle *lprojBundle = [NSBundle bundleWithPath:path];
  //  localized string
  NSString *localizedString = [lprojBundle localizedStringForKey:key value:@"" table:@"Localizable"];
  if (!localizedString) {
    localizedString = key;
  }
  return localizedString;
}
~~~

## Image in cocoapods
在自定义pod中添加图片步骤：  
1. 为`MyPod`添加图片路径`MyPod/Assets/Images`,pod中使用的图片存放在该目录下；  
2. 在`MyPod.podspec`文件中添加图片bundle：`s.resource_bundles = {'ImageBundle' => ['MyPod/Assets/Images/**/*']}`；  
3. 为`UIImage`添加类别方法`bundleImageNamed:`    


~~~objective-c
@implementation UIImage (BundleImage)

+ (UIImage *)bundleImageNamed:(NSString *)name {
  NSBundle *imageBundle = [WWFitnessBundleUtil getBundleWithFrameworkName:@"MyPod" bundleName:@"ImageBundle"];
  return [UIImage imageNamed:name inBundle:imageBundle compatibleWithTraitCollection:nil];
}

@end
~~~

## Font in cocoapods
自定义字体无法静态添加到自定义pod中，需要动态注册字体才能使用。  
1. 为`MyPod`添加字体路径`MyPod/Fonts`,pod中使用的字体存放在该目录下；  
2. 在`MyPod.podspec`文件中添加字体bundle：`s.resource_bundles = {'FontBundle' => ['MyPod/Fonts/*']}`；  
3. 创建注册字体方法，并在使用前仅调用一次。  


~~~objective-c
#import <CoreText/CTFontManager.h>

+ (void)registerFitnessFont {
  [self registerFitnessFont:@"DIN-Regular"];
  [self registerFitnessFont:@"DIN-Medium"];
  [self registerFitnessFont:@"DIN-Bold"];
}

+ (void)registerFitnessFont:(NSString *)fontName {
  NSBundle *fontBundle = [WWFitnessBundleUtil getBundleWithFrameworkName:@"MyPod" bundleName:@"FontBundle"];
  NSURL *fontURL = [fontBundle URLForResource:fontName withExtension:@"otf" /*or TTF*/];
  NSData *inData = [NSData dataWithContentsOfURL:fontURL];
  CFErrorRef error;
  CGDataProviderRef provider = CGDataProviderCreateWithCFData((CFDataRef)inData);
  CGFontRef font = CGFontCreateWithDataProvider(provider);
  if (!CTFontManagerRegisterGraphicsFont(font, &error)) {
    CFStringRef errorDescription = CFErrorCopyDescription(error);
    NSLog(@"Failed to load font: %@", errorDescription);
    CFRelease(errorDescription);
  }
  CFSafeRelease(font);
  CFSafeRelease(provider);
}

void CFSafeRelease(CFTypeRef cf) {
  if (cf != NULL) {
    CFRelease(cf);
  }
}
~~~
