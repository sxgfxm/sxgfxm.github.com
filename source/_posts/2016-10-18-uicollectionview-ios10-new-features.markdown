---
layout: post
title: "UICollectionView iOS 10 New Features"
date: 2016-10-18 20:01:45 +0800
comments: true
categories: iOS
keywords: iOS，UICollectionView，iOS 10.0
description: UICollectionView 
---

## Background

iPhone屏幕的刷新频率固定为60fps，为了达到流畅的滑动效果，iOS应用展示必须满足该条件。当帧率很低时，就会出现明显的卡顿现象。

60fps相当于每帧16.67毫秒，在这么短的时间内collection view可能并不能完成从相对较慢的数据源加载数据。为了提升collection view性能，一个常用的技巧是使`cellForItemAtIndexPath`尽可能快的返回cell，比如异步加载网络图片等。为了进一步提高collection view性能，并且尽量减少开发者的工作，在iOS 10中引入了新特性。

<!--more-->

## UICollectionView API变化

#### 新增UICollectionViewDataSourcePrefetching协议

```objective-c
@protocol UICollectionViewDataSourcePrefetching <NSObject>
  
@required
// indexPaths are ordered ascending by geometric distance from the collection view
- (void)collectionView:(UICollectionView *)collectionView prefetchItemsAtIndexPaths:(NSArray<NSIndexPath *> *)indexPaths NS_AVAILABLE_IOS(10_0);

@optional
// indexPaths that previously were considered as candidates for pre-fetching, but were not actually used; may be a subset of the previous call to -collectionView:prefetchItemsAtIndexPaths:
- (void)collectionView:(UICollectionView *)collectionView cancelPrefetchingForItemsAtIndexPaths:(NSArray<NSIndexPath *> *)indexPaths  NS_AVAILABLE_IOS(10_0);

@end
```

这两个方法均异步执行，可用于异步加载图片等。

#### 新增prefetchDataSource代理

```objective-c
@property (nonatomic, weak, nullable) id<UICollectionViewDataSourcePrefetching> prefetchDataSource NS_AVAILABLE_IOS(10_0);
```

#### 新增prefetchingEnabled属性

```objective-c
@property (nonatomic, getter=isPrefetchingEnabled) BOOL prefetchingEnabled NS_AVAILABLE_IOS(10_0);
```

## Prefetching

当collection view滑动速率将要超过`cellForItemAtIndexPath`返回cell的速率时，collection view会调用`prefetchItemAtIndexPaths:`方法。

collection view会把**可能**即将需要展示的cell的IndexPath放入数组中传递给prefetch方法。这为我们提供了预处理数据机会。比如，当我们需要加载网络图片时，可以在prefetch方法中请求网络数据，并把下载的数据插入到**data source**中，为`cellForItemAtIndexPath`的使用做准备。

当collection view滑动方向改变时，collection view会调用`cancelPrefetchingForItemsAtIndexPaths`方法。

该方法的目的是取消**原本可能**即将展示的cell的预加载数据工作。参数同样是IndexPath的数组。

## UICollectionView Cell生命周期变化

#### UICollectionViewCell Lifecycle: iOS <= 9

 ![UICollectionViewLifecycle_iOS_9](http://ofj92itlz.bkt.clouddn.com/UICollectionView:UICollectionViewLifecycle_iOS_9.jpeg)

1. 首先，调用`cellForItemAtIndexPath:`，从复用队列中弹出一个**cell**，准备对其调用`prepareForReuse`。
2. 然后，根据需求设置**cell**的内容，比如**labels**等。
3. 当**cell**即将出现时，调用`collectionView:willDisplayCell:forItemAtindexPath:`。
4. 当**cell**消失时，调用`collectionView:didEndDisplayingCell:forItemAtIndexPath:`。此时**cell**会重新进入复用队列，等待复用。
5. 当用户向相反方向再次把**cell**滑回屏幕时，会重新从第一步开始执行。

#### UICollectionViewCell Lifecycle: iOS 10

 ![UICollectionViewLifecycle_iOS_10](http://ofj92itlz.bkt.clouddn.com/UICollectionView:UICollectionViewLifecycle_iOS_10.jpeg)

在iOS 10中，前3个步骤与iOS 9是相同的，新的变化发生在**cell**滑出屏幕的时候。

当调用`collectionView:didEndDisplayingCell:forItemAtIndexPath:`后，**cell**不会立刻进入复用队列，系统会**keeps it around for a bit**。相当于会缓存该**cell**一小段时间，在这段时间内如果该**cell**再次回到屏幕中，便不会重新调用`cellForItemAtIndexPath:`，而是直接显示。

至于系统会缓存多久，官方并没有给出明确的时间，感觉跟程序运行时开销有关。

如果想关闭该功能，需要设置`collectionView.prefetchingEnabled = NO;`。

 ![multiple_cells](http://ofj92itlz.bkt.clouddn.com/UICollectionView:multiple_cells.jpeg)

collection view包含多列的情况，主要体现cell的**独立性**。

当某一行需要展示时，每个cell独立出队并调用`cellForItemAtIndexPath:`方法；

当该行即将展示时，每个cell调用`willDisplayCell:atIndexPath:`。

## 总结

- 这些变化对开发者都是透明的，对开发者来说只需利用好prefetch特性。
- prefetch进一步提升了collection view的性能，尤其是获取cell数据开销比较大或者比较慢时。
- 每个cell独立出队，单独设置，确保cell在展示之前总是ready。
- UITableView拥有相同的新特性。

## 参考资料：

[WWDC2016 UICollectionView相关视频](https://developer.apple.com/videos/play/wwdc2016/219/)

[Adoption Curve Dot Net](https://adoptioncurve.net/archives/2016/06/collection-view-updates-in-ios10/)

[little bites of cocoa](https://littlebitesofcocoa.com/241-uicollectionview-cell-pre-fetching)

