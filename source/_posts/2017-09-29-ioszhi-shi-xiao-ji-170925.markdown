---
layout: post
title: "iOS知识小集-170925"
date: 2017-09-29 17:49:31 +0800
comments: true
categories: iOS
keywords: sxgfxm,URL Encode, Git Fetch, NSNumberFormatter,AVAudioSession,AVAudioSessionCategory,AVAudioSessionCategoryOptions,ReactiveCocoa, ReactiveObjC,RACSignal,RACSequence
description: sxgfxm,URL Encode, Git Fetch, NSNumberFormatter,AVAudioSession,AVAudioSessionCategory,AVAudioSessionCategoryOptions,ReactiveCocoa, ReactiveObjC,RACSignal,RACSequence
---

## URL encode
string -> url，特殊字符需要编码。
```objective-c
- (NSString *)urlEncode:(NSString *)string {
  NSCharacterSet *set = [NSCharacterSet characterSetWithCharactersInString:@";/?:@=&<>\"#%{}|\\^~[]` ()"].invertedSet;
  return [string stringByAddingPercentEncodingWithAllowedCharacters:set];
}
```

<!-- more -->

## Git fetch remote branch

```
  git fetch
  git checkout -b localBanchName remoteBranchName
  git branch -vv
```
## NSNumberFormatter
```objective-c
  NSNumberFormatter *formatter = [[NSNumberFormatter alloc] init];
  formatter.numberStyle = NSNumberFormatterPercentStyle;
  NSNumber *number = [formatter numberFromString:index];
```
## layoutSubviews
当frame改变时调用

## AVAudioSession
The session's category and mode together define how the application intends to use audio.

## AVAudioSessionCategory
Each app running in iOS has a single audio session, which in turn has a single category. You can change your audio session’s category while your app is running.  

**AVAudioSessionCategoryAmbient**：可以与其他app混合，锁屏停止播放，受静音键控制。  
**AVAudioSessionCategorySoloAmbient**：默认设置，不可以与其他app混合，锁屏停止播放，受静音键控制。  
**AVAudioSessionCategoryPlayback**：混合其他app需要设置，后台播放需要设置，锁屏继续播放，不受静音键控制。  
**AVAudioSessionCategoryRecord**：用于录音，会打断音频播放，后台录音需要设置，且需要申请录音权限。  
**AVAudioSessionCategoryPlayAndRecord**：既可以录音也可以播放，混合其他app需要设置。  
**AVAudioSessionCategoryMultiRoute**：既可以录音也可以播放，用于多路输出。  

## AVAudioSessionCategoryOptions
Constants that specify optional audio behaviors. Each option is valid only with specific audio session categories.  
options需要配合category使用。  

**AVAudioSessionCategoryOptionMixWithOthers**：允许与其他active session混合。  
**AVAudioSessionCategoryOptionDuckOthers**：使其他audio session声音减弱。  
**AVAudioSessionCategoryOptionAllowBluetooth**：使蓝牙设备可作为音频输入设备。  
**AVAudioSessionCategoryOptionDefaultToSpeaker**：在没有其他输出设备时，通过手机扬声器播放音频，否则通过听筒播放。  
**AVAudioSessionCategoryOptionInterruptSpokenAudioAndMixWithOthers**：会暂停其他音乐播放，适用于指引类app。  
**AVAudioSessionCategoryOptionAllowAirPlay**：允许AirPlay播放。  
**AVAudioSessionCategoryOptionAllowBluetoothA2DP**：允许蓝牙播放。  

语音识别界面：可录音，可播放，不受静音键控制；可混合；扬声器发声；可蓝牙播放；可后台播放。


## [ReactiveCocoa](https://github.com/ReactiveCocoa/ReactiveCocoa)
ReactiveCocoa is an open source library that brings Functional Reactive Programming paradigm to Objective-C.  
Functional Reactive Programming (FRP) is a way of thinking about software in terms of transforming inputs to produce output continuously over time.  

ReactiveCocoa is comprised of two major components: signals (**RACSignal**) and sequences (**RACSequence**).
Both signals and sequences are kinds of streams, sharing many of the same operators. Signals are a push-driven stream, and sequences are a pull-driven stream.  

### RACSignal
1. Handling Asynchronous Or Event-driven Data Sources.
2. Chaining Dependent Operations.
3. Parallelizing Independent Work.

Events signals send:  
1. The **next** event provides a new value from the stream.
2. The **error** event indicates that an error occurred before the signal could finish.
3. The **completed** event indicates that the signal finished successfully, and that no more values will be added to the stream.

### RACSequence
Simplifying Collection Transformations.  
The values in a sequence are evaluated lazily (i.e., only when they are needed) by default.  
Just like Cocoa collections, sequences cannot contain nil.  

### RAC vs. KVO
KVO is neither pleasant nor easy to use: its API is overwrought with unused parameters and sorely lacking a blocks-based interface.

### RAC vs. Bindings
RAC offers a clear, understandable, and extensible code-based API that works in iOS and is apt to replace all but the most trivial uses of bindings in your OS X application.

## [ReactiveObjC](https://github.com/ReactiveCocoa/ReactiveObjC)
ReactiveObjC(RAC) is an Objective-C framework inspired by [Functional Reactive Programming](https://en.wikipedia.org/wiki/Functional_reactive_programming).  
It provides APIs for **composing and transforming streams of values. **  
ReactiveObjC supports OS X 10.8+ and **iOS 8.0+**.  

### Features:
1. RAC provides **signals** (represented by **RACSigal**) that capture present and future values.
2. RAC provides **unified approach** to dealing with asynchronous behaviors.
3. Signals can be **chained together** and operated on.
4. Signals can be used to **derive state**.
5. Signals can be built on **any stream of values** over time.
6. Signals can also represent timers, other UI events, or anything else that **changes over time**.
7. Signals can be chained to **sequentially execute asynchronous operations**, instead of nesting callbacks with blocks.
8. RAC even makes it easy to **bind** to the result of an asynchronous operation.

### When to use ReactiveObjC
1. Handling asynchronous or event-driven data sources.
2. Chaining dependent operations.
3. Parallelizing independent work.
4. Simplifying collection transformations.