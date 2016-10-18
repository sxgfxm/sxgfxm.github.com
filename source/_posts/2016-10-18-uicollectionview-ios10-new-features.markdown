---
layout: post
title: "UICollectionView iOS 10 New Features"
date: 2016-10-18 20:01:45 +0800
comments: true
categories: iOS
---

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

## UICollectionView Cell生命周期变化

#### UICollectionViewCell Lifecycle: iOS <= 9

 ![UICollectionViewLifecycle_iOS_9](../images/UICollectionViewCell/UICollectionViewLifecycle_iOS_9.jpeg)

1. 首先，调用`cellForItemAtIndexPath:`，从复用队列中弹出一个**cell**，准备对其调用`prepareForReuse`。
2. 然后，根据需求设置**cell**的内容，比如**labels**等。
3. 当**cell**即将出现时，调用`collectionView:willDisplayCell:forItemAtindexPath:`。
4. 当**cell**消失时，调用`collectionView:didEndDisplayingCell:forItemAtIndexPath:`。此时**cell**会重新进入复用队列，等待复用。
5. 当用户向相反方向再次把**cell**滑回屏幕时，会重新从第一步开始执行。

#### UICollectionViewCell Lifecycle: iOS 10

 ![UICollectionViewLifecycle_iOS_10](../images/UICollectionViewCell/UICollectionViewLifecycle_iOS_10.jpeg)

在iOS 10中，前3个步骤与iOS 9是相同的，新的变化发生在**cell**滑出屏幕的时候。

当调用`collectionView:didEndDisplayingCell:forItemAtIndexPath:`后，**cell**不会立刻进入复用队列，系统会**keeps it around for a bit**。相当于会缓存该**cell**一小段时间，在这段时间内如果该**cell**再次回到屏幕中，便不会重新调用`cellForItemAtIndexPath:`，而是直接显示。

至于系统会缓存多久，官方并没有给出明确的时间，感觉跟程序运行时开销有关。

如果想关闭该功能，需要设置`collectionView.prefetchingEnabled = NO;`。

## 总结

- 在`cellForItemAtIndexPath:`中设置cell，尽量保持`willDisplay/didEndDisplay`轻量级。
- cell被创建，但不一定会显示。
- UITableView拥有相同的新特性。