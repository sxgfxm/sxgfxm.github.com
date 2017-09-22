---
layout: post
title: "iOS知识小集-170424"
date: 2017-05-17 16:38:39 +0800
comments: true
categories: iOS
keywords: sxgfxm,ComponentKit Tutorial,Flexbox Layout,CKStackLayoutComponent
description: sxgfxm,ComponentKit Tutorial,Flexbox Layout,CKStackLayoutComponent 
---

# ComponentKit Tutorial - Layout

## Flexbox Layout
**flex container**：容器。  
main axis：main start， main end。  
cross axis：cross start， cross end。  
**flex item**：成员。  
main size。  
cross size。  

<!-- more -->

## Container Properties
flex-direction：决定主轴方向。  

```objective-c
typedef NS_ENUM(NSUInteger, CKStackLayoutDirection) {
  //  垂直方向
  CKStackLayoutDirectionVertical,
  //  水平方向
  CKStackLayoutDirectionHorizontal,
};
```

flex-wrap：决定如何换行。  
flex-flow：flex-direction和flex-wrap的简写形式。  
**justify-content**：决定items在主轴上的对齐方式。  

```objective-c
/** If no children are flexible, how should this component justify its children in the available space? */
typedef NS_ENUM(NSUInteger, CKStackLayoutJustifyContent) {
  /**
   On overflow, children overflow out of this component's bounds on the right/bottom side.
   On underflow, children are left/top-aligned within this component's bounds.
   */
  //  左对齐
  CKStackLayoutJustifyContentStart,
  /**
   On overflow, children are centered and overflow on both sides.
   On underflow, children are centered within this component's bounds in the stacking direction.
   */
  //  居中
  CKStackLayoutJustifyContentCenter,
  /**
   On overflow, children overflow out of this component's bounds on the left/top side.
   On underflow, children are right/bottom-aligned within this component's bounds.
   */
  //  右对齐
  CKStackLayoutJustifyContentEnd,
};
```

**align-items**：决定items在交叉轴上的对齐方式。  

```objective-c
typedef NS_ENUM(NSUInteger, CKStackLayoutAlignItems) {
  /** Align children to start of cross axis */
  //  交叉轴起点对齐
  CKStackLayoutAlignItemsStart,
  /** Align children with end of cross axis */
  CKStackLayoutAlignItemsEnd,
  //  交叉轴终点对齐
  /** Center children on cross axis */
  //  交叉轴居中对齐
  CKStackLayoutAlignItemsCenter,
  /** Expand children to fill cross axis */
  //  交叉轴方向拉伸
  CKStackLayoutAlignItemsStretch,
};
```

## Item Properties
order：决定item排列顺序，数值越小，排位越靠前。  
**flex-grow**：决定item主轴方向的放大比例，默认为0，即如果存在剩余空间，也不放大。  
如果所有项目的flex-grow属性都为1，则它们将等分剩余空间（如果有的话）。如果一个项目的flex-grow属性为2，其他项目都为1，则前者占据的剩余空间将比其他项多一倍。  
**flex-shrink**：决定item主轴方向的缩小比例，默认为1，即如果空间不足，该item将缩小。  
如果所有项目的flex-shrink属性都为1，当空间不足时，都将等比例缩小。如果一个项目的flex-shrink属性为0，其他项目都为1，则空间不足时，前者不缩小。  
**flex-basis**：决定了在分配多余空间之前，item的main size大小，根据该值计算主轴是否有多余空间。  
flex：flex-grow, flex-shrink 和 flex-basis的简写。  
**align-self**：决定item单独的对齐方式，可以覆盖 **align-items** 属性。  
默认值为auto，表示继承父元素的align-items属性，如果没有父元素，则等同于stretch。  

```objective-c
/**
 Each child may override their parent stack's cross axis alignment.
 @see CKStackLayoutAlignItems
 */
typedef NS_ENUM(NSUInteger, CKStackLayoutAlignSelf) {
  /** Inherit alignment value from containing stack. */
  CKStackLayoutAlignSelfAuto,
  CKStackLayoutAlignSelfStart,
  CKStackLayoutAlignSelfEnd,
  CKStackLayoutAlignSelfCenter,
  CKStackLayoutAlignSelfStretch,
};
```



## CKStackLayoutComponent
A simple layout component that stacks a list of children vertically or horizontally.  
动态创建children：  

```objective-c
static std::vector<CKStackLayoutComponentChild> createChildren(NSArray* list){
  std::vector<CKStackLayoutComponentChild> children;
  for (VPANewsModel*newsModel in list) {
    children.push_back({[VPANewsComponent newWithNewsModel:newsModel]});
  }
  return children;
}
```

## CKBackgroundLayoutComponent
Lays out a single child component, then lays out a background component behind it stretched to its size.  

```objective-c
+ (instancetype)newWithComponent:(CKComponent *)component
                      background:(CKComponent *)background;
```

## CKStaticLayoutComponent
A component that positions children at fixed positions.  
Computes a size that is the union of all childrens' frames.  

## CKCenterLayoutComponent
Lays out a single child component and position it so that it is centered into the layout bounds.  

```objective-c
+ (instancetype)newWithCenteringOptions:(CKCenterLayoutComponentCenteringOptions)centeringOptions
                          sizingOptions:(CKCenterLayoutComponentSizingOptions)sizingOptions
                                  child:(CKComponent *)child
                                   size:(const CKComponentSize &)size;
```

## CKRatioLayoutComponent
For when the content should respect a certain inherent ratio but can be scaled (think photos or videos).
The ratio passed is the ratio of height / width you expect.  

