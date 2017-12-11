---
layout: post
title: "iOS知识小集-171204"
date: 2017-12-11 16:49:04 +0800
comments: true
categories: iOS
keywords: sxgfxm,UIViewController Transition,transitioningDelegate,UIViewControllerAnimatedTransitioning
description: sxgfxm,UIViewController Transition,transitioningDelegate,UIViewControllerAnimatedTransitioning
---

## UIViewController Transition
自定义Present和Dismiss动画。

<!-- more -->

### 设置待Present的UIViewController的transitioningDelegate
`modalPresentationStyle`为`FullScreen`时，dismiss时会自动移除`fromView`。  
`modalPresentationStyle`为`Custom`时，dismiss时会需手动移除`fromView`。  

```objective-c
@interface ViewController : UIViewController <UIViewControllerTransitioningDelegate>

@end

@implementation ViewController

- (instancetype)init{
  if (self = [super init]) {
    self.modalPresentationStyle = UIModalPresentationOverFullScreen;
    self.transitioningDelegate = self;
  }
  return self;
}

#pragma mark - UIViewControllerTransitioningDelegate
- (id<UIViewControllerAnimatedTransitioning>)animationControllerForPresentedController:(UIViewController *)presented
                                                                  presentingController:(UIViewController *)presenting
                                                                      sourceController:(UIViewController *)source {
  return [PresentAnimator new];
}

- (id<UIViewControllerAnimatedTransitioning>)animationControllerForDismissedController:(UIViewController *)dismissed{
  return [DismissAnimator new];
}

@end
```

### 创建PresentAnimator
```objective-c
@interface PresentAnimator : NSObject <UIViewControllerAnimatedTransitioning>

@end

@implementation PresentAnimator

#pragma mark - UIViewControllerContextTransitioning
- (void)animateTransition:(nonnull id<UIViewControllerContextTransitioning>)transitionContext {
  //  from -> to
  UIViewController *fromViewController = [transitionContext viewControllerForKey:UITransitionContextFromViewControllerKey];
  UIViewController *toViewController = [transitionContext viewControllerForKey:UITransitionContextToViewControllerKey];
  //  contrainer
  UIView *containerView = transitionContext.containerView;
  //  from -> to
  UIView *fromView = [transitionContext viewForKey:UITransitionContextFromViewKey];
  UIView *toView = [transitionContext viewForKey:UITransitionContextToViewKey];
  //  add
  [containerView addSubview:toView];
  //  animation
  toView.alpha = 0;
  toView.frame = CGRectMake(fromView.frame.origin.x, CGRectGetMaxY(fromView.frame) / 2, fromView.frame.size.width, fromView.frame.size.height);
  [UIView animateWithDuration:[self transitionDuration:transitionContext] animations:^{
    toView.alpha = 1;
    toView.frame = [transitionContext finalFrameForViewController:toViewController];
  } completion:^(BOOL finished) {
    [transitionContext completeTransition:![transitionContext transitionWasCancelled]];
  }];
}

- (NSTimeInterval)transitionDuration:(nullable id<UIViewControllerContextTransitioning>)transitionContext {
  return 0.25;
}

@end
```

### 创建DismissAnimator
```objective-c
@interface DismissAnimator : NSObject <UIViewControllerAnimatedTransitioning>

@end

@implementation DismissAnimator

#pragma mark - UIViewControllerContextTransitioning
- (void)animateTransition:(nonnull id<UIViewControllerContextTransitioning>)transitionContext {
  //  from -> to
  UIView *fromView = [transitionContext viewForKey:UITransitionContextFromViewKey];
  //  animation
  fromView.alpha = 1;
  [UIView animateWithDuration:[self transitionDuration:transitionContext] animations:^{
    fromView.alpha = 0;
  } completion:^(BOOL finished) {
    [transitionContext completeTransition:![transitionContext transitionWasCancelled]];
  }];
}

- (NSTimeInterval)transitionDuration:(nullable id<UIViewControllerContextTransitioning>)transitionContext {
  return 0.25;
}

@end
```

