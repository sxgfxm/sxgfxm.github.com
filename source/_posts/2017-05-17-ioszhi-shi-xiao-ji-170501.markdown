---
layout: post
title: "iOS知识小集-170501"
date: 2017-05-17 16:42:40 +0800
comments: true
categories: iOS
keywords: sxgfxm,ComponentKit Tutorial,CKCollectionViewTransactionalDataSource
description: sxgfxm,ComponentKit Tutorial,CKCollectionViewTransactionalDataSource
---

# ComponentKit Tutorial - CollectionView

## Install
通过 **Carthage** 安装，在 **Cartfile** 中添加`github "facebook/ComponentKit" ~> 0.20`，然后运行`carthage update`，编译完成后，在 **Embedded Binaries** 添加`Carthage/Build/iOS/ComponentKit.framework`。所有需要使用ComponentKit的源文件需要修改后缀为 **.mm**。

## Philosophy
Doing so this reverses the traditional approach for a `UICollectionViewDataSource`. Usually the controller layer will **tell** the `UICollectionView` to update and then the `UICollectionView` **ask** the datasource for the data. Here the model is  more Reactive, from an external prospective, the datasource is **told** what changes to apply and then **tell** the collection view to apply the corresponding changes.

<!-- more -->

## 步骤
### CKComponentProvider Protocol

ViewController需要遵守`CKComponentProvider`协议，实现`+componentForModel: context:`方法，将model转换为component。  
在该方法中，通过不同类型的model返回不同类型的component。  

~~~
+ (CKComponent *)componentForModel:(id<NSObject>)model
                           context:(id<NSObject>)context {
  if ([model isKindOfClass:[NewsModel class]]) {
    return [NewsComponent newWithNewsModel:model context:context];
  }
  return nil;
}
~~~

### FlowLayout  

~~~
UICollectionViewFlowLayout *flowLayout = [[UICollectionViewFlowLayout alloc] init];
[flowLayout setScrollDirection:UICollectionViewScrollDirectionVertical];
[flowLayout setMinimumInteritemSpacing:0];
[flowLayout setMinimumLineSpacing:0];
~~~

### CollectionView  



~~~
self.collectionView = [[UICollectionView alloc] initWithFrame:self.view.bounds collectionViewLayout:flowLayout];
self.collectionView.delegate = self;
self.collectionView.backgroundColor = [UIColor blackColor];
[self.view addSubview:self.collectionView];
~~~

### Item size range  

Item size range。通过设置`CKComponentSizeRangeFlexibleHeight`使item的高度自适应。  



~~~
const CKSizeRange sizeRange = [[CKComponentFlexibleSizeRangeProvider
      providerWithFlexibility:CKComponentSizeRangeFlexibleHeight]
     sizeRangeForBoundingSize:self.collectionView.bounds.size];
~~~
### Context  

Context可以是任何不可变对象，创建component时的不可变上下文信息，比如设备类型，图片下载器。  
`MyContext *context = [MyContext new];`  
预先在主线程加载图片。  

### Configuration  

DataSource的configuration，需要 **ComponentProvider**，**sizeRange**，**context** 三个参数。  



~~~
CKTransactionalComponentDataSourceConfiguration *configuration =
      [[CKTransactionalComponentDataSourceConfiguration alloc]
          initWithComponentProvider:[self class]
                            context:context
                          sizeRange:sizeRange];
~~~

### DataSource  

需要 **collectionView**，**supplementaryViewDataSource**，**configuration** 三个参数。  



~~~
self.dataSource = [[CKCollectionViewTransactionalDataSource alloc]
           initWithCollectionView:self.collectionView
      supplementaryViewDataSource:nil
                    configuration:configuration];
~~~

### Initial Changeset  

需要初始化DataSource，即向DataSource中添加Section。  



~~~
CKTransactionalComponentDataSourceChangeset *initialChangeset =
  [[[CKTransactionalComponentDataSourceChangesetBuilder
      transactionalComponentDataSourceChangeset]
      withInsertedSections:[NSIndexSet indexSetWithIndex:0]] build];
[self.dataSource applyChangeset:initialChangeset
                           mode:CKUpdateModeAsynchronous
                       userInfo:nil];
~~~

### insert/update items  

向DataSource中插入Items才能显示。  



~~~
NSMutableDictionary<NSIndexPath *, NewsModel *> *items = [NSMutableDictionary new];
for (NSInteger i = 0; i < 50; i++) {
  NewsModel *newsModel = [[NewsModel alloc] init];
  newsModel.title = [NSString stringWithFormat:@"News Title: Title %ld", i];
  newsModel.category = @"科技";
  newsModel.updateTime = [NSDate date];
  newsModel.source = @"网易新闻";
  [items setObject:newsModel
            forKey:[NSIndexPath indexPathForRow:i inSection:0]];
}
CKTransactionalComponentDataSourceChangeset *changeset =
  [[[CKTransactionalComponentDataSourceChangesetBuilder
      transactionalComponentDataSourceChangeset]
      withInsertedItems:items] build];
[self.dataSource applyChangeset:changeset
                           mode:CKUpdateModeAsynchronous
                       userInfo:nil];
~~~

### UICollectionView delegate  



~~~
- (CGSize)collectionView:(UICollectionView *)collectionView
                  layout:(UICollectionViewLayout *)collectionViewLayout
  sizeForItemAtIndexPath:(NSIndexPath *)indexPath {
  return [self.dataSource sizeForItemAtIndexPath:indexPath];
}

- (void)collectionView:(UICollectionView *)collectionView
       willDisplayCell:(UICollectionViewCell *)cell
    forItemAtIndexPath:(NSIndexPath *)indexPath {
  [self.dataSource announceWillDisplayCell:cell];
}

- (void)collectionView:(UICollectionView *)collectionView
  didEndDisplayingCell:(UICollectionViewCell *)cell
    forItemAtIndexPath:(NSIndexPath *)indexPath {
  [self.dataSource announceDidEndDisplayingCell:cell];
}
~~~
