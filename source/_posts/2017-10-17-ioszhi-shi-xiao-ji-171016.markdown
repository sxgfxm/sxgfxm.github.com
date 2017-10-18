---
layout: post
title: "iOS知识小集-171016"
date: 2017-10-17 19:06:22 +0800
comments: true
categories: iOS
keywords: sxgfxm,Horizontal ScrollComponent of CKComponent,CKComponentController,computeLayoutThatFits,CKComponentRootView, iOS DeviceSupport 
description: sxgfxm,Horizontal ScrollComponent of CKComponent,CKComponentController,computeLayoutThatFits,CKComponentRootView, iOS DeviceSupport
---

## Clear iOS DeviceSupport to free disk storage
真机调试时，不同系统的手机会生成不同的Device Support文件，每个大概3GB左右，占用大量磁盘空间。删除不需要的Device Support文件，可以释放大量磁盘空间。
iOS DeviceSupport 目录路径`[User]/Library/Developer/Xcode/iOS DeviceSupport`

## Compute CKComponent size
获取component的size。  

```objective-c
  #import <ComponentKit/CKComponentSubclass.h>

  - (CGSize)sizeOfComponent:(CKComponent*)component{
    return [component computeLayoutThatFits: CKSizeRange()].size;
  }
```

<!-- more -->

## CKComponentRootView
获取component对应的根视图，在CKComponentController中可以自定义根视图。  

```objective-c
  CKComponentRootView *rootView = component.viewContext.view;
```

## CKComponentController

1. 命名必须为**ComponentName+Controller**，继承至CKComponentController；
2. Component必须添加**Scope**，`CKComponentScope scope(self, model)`；
3. ComponentController可以监控component的生命周期，进行设置；
4. `-didMount`，已经加载完成，包括所有的子component；
5. `-willUpdateComponent`，component即将更新时调用；
6. `-willRemount`，component正在update或root component被关联到其他root view时调用；
7. `-componentTreeWillAppear`，当调用`-willDisplayCell:for{Row|Item}AtIndexPath:`时调用；

## [TCZLocalizableTool](https://github.com/lefex/TCZLocalizableTool)
通过Python脚本检查本地化文件中语法错误。

## Horizontal ScrollComponent of CKComponent
Add a horizontal UIScrollView to a vertical UICollectionView by ComponentKit.   

1. Create a **CKStackLayoutComponent** component and set [**UIScrollView** class] in block newWithView;
2. In `-didMount`, get the scroll view and set its `contentSize`;
3. In `-didUpdateComponent`, update its `contentSize` if the component has changed.

.h  

```
#import <ComponentKit/ComponentKit.h>

@interface XGHorizontalScrollComponent : CKCompositeComponent

+ (instancetype)newWithChildren:(std::vector<CKStackLayoutComponentChild>)children;

@end

@interface XGHorizontalScrollComponentController : CKComponentController

@end
```

.mm

```
#import "XGHorizontalScrollComponent.h"
#import <ComponentKit/CKComponentSubclass.h>

@interface XGHorizontalScrollComponent()

@property(nonatomic, strong) CKComponent *scrollComponent;

@end

@implementation XGHorizontalScrollComponent

+ (instancetype)newWithChildren:(std::vector<CKStackLayoutComponentChild>)children{
  CKComponentScope scope(self);
  CKComponent *content = [CKStackLayoutComponent
                          newWithView:{
                            [UIScrollView class]
                          }
                          size:{}
                          style:{
                            .direction = CKStackLayoutDirectionHorizontal,
                            .alignItems = CKStackLayoutAlignItemsStretch
                          }
                          children:{children}];
  XGHorizontalScrollComponent *component = [super newWithComponent:content];
  if (component) {
    component->_scrollComponent = content;
  }
  return component;
}

@end

@implementation XGHorizontalScrollComponentController

- (void)didMount {
  [super didMount];
  //  Get scroll component
  XGHorizontalScrollComponent *component = (XGHorizontalScrollComponent*)self.component;
  CKComponent *scrollComponent = component.scrollComponent;
  //  Get component size
  CKSizeRange range = CKSizeRange();
  CKComponentLayout layout = [scrollComponent computeLayoutThatFits:range];
  //  Get scroll view
  UIScrollView *scrollView = (UIScrollView *)scrollComponent.viewContext.view;
  //  Set scroll view properties, you can custom scrollView here
  scrollView.showsHorizontalScrollIndicator = NO;
  scrollView.showsVerticalScrollIndicator = NO;
  //  Set contentSize
  [scrollView setContentSize:layout.size];
}

//  Invoked when the component is updated
-(void)didUpdateComponent{
  [super didUpdateComponent];
  //  Update contentSize
  XGHorizontalScrollComponent *component = (XGHorizontalScrollComponent*)self.component;
  CKComponent *scrollComponent = component.scrollComponent;
  CKSizeRange range = CKSizeRange();
  CKComponentLayout layout = [scrollComponent computeLayoutThatFits:range];
  UIScrollView *scrollView = (UIScrollView *)scrollComponent.viewContext.view;
  [scrollView setContentSize:layout.size];
}

@end
```

