---
layout: post
title: "ReactiveObjC Memory Management"
date: 2017-09-30 15:06:51 +0800
comments: true
categories: ReactiveObjC
keywords: sxgfxm, ReactiveObjC Memory Management
description: ReactiveObjC Memory Management
---

You don't need to retain signals in order to process them.  

## Subscribers
Any objects referenced from those blocks will therefore be retained as part of the subscription.

## Finite or Short-Lived Signals
Subscription is automatically terminated upon completion or error, and the subscriber removed.  
The **RACSignal -> RACSubscriber** relationship is torn down as soon as signal finishes, breaking the retain cycle.

<!-- more -->

## Infinite Signals
**Disposing of a subscription will remove the associated subscriber**, and just generally clean up any resources associated with that subscription.

## Signals Derived from self
Any time a signal's lifetime is tied to the calling scope, you'll have a much harder cycle to break.  
This commonly occurs when using **RACObserve()** on a key path that's relative to **self**, and then applying a block that needs to capture **self**.  
```objective-c
__weak id weakSelf = self;
[RACObserve(self, username) subscribeNext:^(NSString *username) {
    id strongSelf = weakSelf;
    [strongSelf validateUsername];
}];
```

```objective-c
@weakify(self);
[RACObserve(self, username) subscribeNext:^(NSString *username) {
    @strongify(self);
    [self validateUsername];
}];
```

```objective-c
RACSignal *validated = [RACObserve(self, username) map:^(NSString *username) {
    // Put validation logic here.
    return @YES;
}];
```