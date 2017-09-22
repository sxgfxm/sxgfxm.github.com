---
layout: post
title: "ComponentKit Tutorial"
date: 2017-06-13 16:23:00 +0800
comments: true
categories: ComponentKit
keywords: sxgfxm, ComponentKit
description: sxgfxm, ComponentKit
---

## What's Component ?
Components are **immutable objects** that specify how to configure views.  
A component is a fixed description that can be used to paint a view but that is **not a view itself**.  

<!-- more -->

## What's Special ?
**Declarative**: You directly declare the hierarchy of the component from up to down. It's different from what we used to layout views. It turns to **layout your children**, not **layout yourself**.  
**Functional**: Data flows in one direction. Methods take data models and return totally **immutable** components. When state changes, ComponentKit re-renders from the root and reconciles the two component trees from the top with as few changes to the view hierarchy as possible. Cause of one direction data flow, you can focus on how to display the model and simply write unit test for the component.  
**Composable**: A component is ofen composed of other components, building up a component hierarchy that describes a user interface. You can esaily reuse a component just in one line.  

## What's Limitation ？
**ListViews**: ComponetKit is optimized to work well with a **UICollectionView**, and with more effort for **UITableView**. It's not efficient in other situation.  
**Gestures**: ComponetKit can simply handle tap gesture but it's hard to implement more complicated gestures like panning, pinching, swiping and so on.  
**Animation**: CKComponent forms a declarative mapping from model to view configuration. Animations should be handled fullyi n the view layer instead of inside Components.  
**LearningCost**: ComponetKit is developed in C++. It will be difficult when you dig into if you are not familiar with C++. You have to get used to **Reactive Philosophy**.  

## How to Define a Component ?
Avoid subclassing `CKComponent` directly. Instead, subclassing `CKCompositeComponent`.  
A "composite component" simply wraps another component, hiding its implementation details from the outside world.  
Steps to define a component:    
1. Define the model;  
2. Define a class inherit from `CKCompositeComponent`, and change the suffix from `.m` to `.mm`, because ComponetKit is developed in C++;  
3. Add a constructor method use `+newWith...`;  
4. Implement the `+newWith...` method by directly declare the hierarchy of the component.  
5. Anywhere using component, the suffix of the implementation file should also be `.mm`.  

An example to implement a custom VPADateComponent.      

```objective-c
@implementation VPADateComponent

//  Data flows in one direction. Component is immutable.
+(instancetype)newWithDateModel:(VPADateModel *)dateModel{
  if (!dateModel) {
    return nil;
  }
  //  Directly declare the hierarchy of the component from up to down.
  //  VPADateComponent is composed of CKInsetComponent, CKCenterLayoutComponent and CKLabelComponent and they are alse reuseable.
  return [super newWithComponent:[CKInsetComponent
                                  newWithInsets:{.left = 10, .top = 10, .right = 10, .bottom = 10}
                                  component:[CKCenterLayoutComponent
                                             newWithCenteringOptions:CKCenterLayoutComponentCenteringXY
                                             sizingOptions:CKCenterLayoutComponentSizingOptionDefault
                                             child:[CKLabelComponent
                                                    newWithLabelAttributes:{
                                                      .string = formatDate(dateModel.date),
                                                      .font = [UIFont systemFontOfSize:12],
                                                      .color = [UIColor whiteColor]
                                                    }
                                                    viewAttributes:{
                                                      {@selector(setBackgroundColor:),[UIColor clearColor]},
                                                      {@selector(setUserInteractionEnabled:), @NO}
                                                    }
                                                    size:{}]
                                             size:{}]]];
}

static NSString* formatDate(NSDate* date){
  NSDateFormatter *formatter = [[NSDateFormatter alloc] init];
  formatter.dateFormat = @"yyyy-MM-dd hh:mm:ss";
  return [formatter stringFromDate:date];
}

@end
```

## Common UI Components

### CKComponent
The base class of all Components. You can never directly subclass `CKComponent`.  
Constructor:    

```objective-c
/**
 @param view A struct describing the view for this component. Pass {} to specify that no view should be created.
 @param size A size constraint that should apply to this component. Pass {} to specify no size constraint.
 */
+ (instancetype)newWithView:(const CKComponentViewConfiguration &)view
                       size:(const CKComponentSize &)size;
```

Exmaple:    

```objective-c
CKComponent *component = [CKComponent
  newWithView:{
    [UIView class],
    {
      //  set common properties of the view
      {@selector(setBackgroundColor:),[UIColor redColor]},
      //  set user interaction
      {@selector(setUserInteractionEnabled:), @YES},
      //  set tap gesture
      {CKComponentTapGestureAttribute(@selector(tapAction))},
      //  set layer properties
      {CKComponentViewAttribute::LayerAttribute(@selector(setCornerRadius:)), @10.0}
    }
  }
  size:{50,50}];

- (void)tapAction{
  //  add tap action here
}
```

### CKCompositeComponent
`CKCompositeComponent` allows you to hide your implementation details and avoid subclassing layout components like `CKStackLayoutComponent`. In almost all cases, you should subclass `CKCompositeComponent` instead of subclassing any other class directly. This hides your layout implementation details from the outside world.

### CKLabelComponent
CKLabelComponent is a simplified text component that just displays NSStrings.  
Constructor:    

```objective-c
/**
 @param attributes The content and styling information for the text component.
 @param viewAttributes These are passed directly to CKTextComponent and its backing view.
 @param size The component size or {} for the default which is for the layout to take the maximum space available.
 */
+ (instancetype)newWithLabelAttributes:(const CKLabelAttributes &)attributes
                        viewAttributes:(const CKViewComponentAttributeValueMap &)viewAttributes
                                  size:(const CKComponentSize &)size;
```

Example:    

```objective-c
CKLabelComponent *labelComponent = [CKLabelComponent
  newWithLabelAttributes:{
    .string = @"Lable Component",
    .color = [UIColor blackColor],
    .font = [UIFont systemFontOfSize:17],
    .alignment = NSTextAlignmentLeft
  }
  viewAttributes:{
    {@selector(setBackgroundColor:),[UIColor clearColor]},
    {@selector(setUserInteractionEnabled:), @NO}
  }
  size:{.maxWidth = 250}]
```

### CKButtonCompoent
A component that creates a UIButton.  
Constructor:    

```objective-c
/**
 This component chooses the smallest size within its SizeRange that will fit its content. If its max size is smaller
 than the size required to fit its content, it will be truncated.
 */
+ (instancetype)newWithTitles:(CKContainerWrapper<std::unordered_map<UIControlState, NSString *>> &&)titles
                  titleColors:(CKContainerWrapper<std::unordered_map<UIControlState, UIColor *>> &&)titleColors
                       images:(CKContainerWrapper<std::unordered_map<UIControlState, UIImage *>> &&)images
             backgroundImages:(CKContainerWrapper<std::unordered_map<UIControlState, UIImage *>> &&)backgroundImages
                    titleFont:(UIFont *)titleFont
                     selected:(BOOL)selected
                      enabled:(BOOL)enabled
                       action:(const CKTypedComponentAction<UIEvent *> &)action
                         size:(const CKComponentSize &)size
   accessibilityConfiguration:(CKButtonComponentAccessibilityConfiguration)accessibilityConfiguration;
```

Example:    

```objective-c
CKButtonComponent *buttonComponent = [CKButtonComponent
  newWithTitles:{
    {UIControlStateNormal,@"button"}
  }
  titleColors:{
    {UIControlStateNormal,[UIColor whiteColor]}
  }
  images:{}
  backgroundImages:{}
  titleFont:[UIFont systemFontOfSize:20]
  selected:NO
  enabled:YES
  action:{scope,@selector(tapAction)}
  size:{}
  attributes:{
    {@selector(setBackgroundColor:),[UIColor yellowColor]}
  }
  accessibilityConfiguration:{}]

- (void)tapAction{
  //  add tap action here
}
```

### CKImageComponent
A component that displays an image using UIImageView.  
Constructor:    

```objective-c
/**
 Uses a static layout with the given image size and applies additional attributes.
 */
+ (instancetype)newWithImage:(UIImage *)image
                  attributes:(const CKViewComponentAttributeValueMap &)attributes
                        size:(const CKComponentSize &)size;
```

Example:    

```objective-c
CKImageComponent *imageComponent = [CKImageComponent
  newWithImage:[UIImage imageNamed:imageName]
  attributes:{
    {@selector(setUserInteractionEnabled:), @NO}
  }
  size:{40, 40}]]
```

## Common Layout Components
ComponentKit includes a library of components that can be composed to declaratively specify a layout.

### CKStackLayoutComponent
A simple layout component that stacks a list of children vertically or horizontally.    

Constructor:    

```objective-c
  /** @param view A view configuration, or {} for no view. @param size A size, or {} for the default size. @param style Specifies how children are laid out. @param children A vector of children components. */
+ (instancetype)newWithView:(const CKComponentViewConfiguration &)view                       size:(const CKComponentSize &)size                      style:(const CKStackLayoutComponentStyle &)style                   children:(CKContainerWrapper<std::vector<CKStackLayoutComponentChild>> &&)children;
```

Exmaple:    

```objective-c
CKStackLayoutComponent *stackComponent = [CKStackLayoutComponent
  newWithView:{}
  size:{}
  style:{.direction = CKStackLayoutDirectionVertical, .alignItems = CKStackLayoutAlignItemsStretch}
  children:{
    {labelComponent},
    {buttonComponent},
    {imageComponent}
  }];
```

### CKInsetComponent
A component that wraps another component, applying insets around it.  
If the child component has a size specified as a percentage, the percentage is resolved against this component's parent size **after** applying insets.  
CKInsetComponent's child behaves similarly to "box-sizing: border-box".   
Constructor:    

```objective-c
/**
 @param view Passed to CKComponent +newWithView:size:. The view, if any, will extend outside the insets.
 @param insets The amount of space to inset on each side.
 @param component The wrapped child component to inset. If nil, this method returns nil.
 */
+ (instancetype)newWithView:(const CKComponentViewConfiguration &)view
                     insets:(UIEdgeInsets)insets
                  component:(CKComponent *)component;
```

Example:    

```objective-c
CKInsetComponent *insetComponent = [CKInsetComponent
  newWithView:{}
  insets:{.left = 10, .top = 10, .right = 10, .bottom = 10}
  component: labelComponent];
```

### CKCenterLayoutComponent
Lays out a single child component and position it so that it is centered into the layout bounds.  
Constructor:    

```objective-c
/**
 @param centeringOptions, see CKCenterLayoutComponentCenteringOptions.
 @param sizingOptions, see CKCenterLayoutComponentSizingOptions.
 @param child The child to center.
 @param size The component size or {} for the default which is for the layout to take the maximum space available.
 */
+ (instancetype)newWithCenteringOptions:(CKCenterLayoutComponentCenteringOptions)centeringOptions
                          sizingOptions:(CKCenterLayoutComponentSizingOptions)sizingOptions
                                  child:(CKComponent *)child
                                   size:(const CKComponentSize &)size;
```

Exmaple:    

```objective-c
CKCenterLayoutComponent * centerComponent = [CKCenterLayoutComponent
  newWithCenteringOptions:CKCenterLayoutComponentCenteringXY
  sizingOptions:CKCenterLayoutComponentSizingOptionDefault
  child:labelComponent
  size:{}];
```

### CKOverlayLayoutComponent
This component lays out a single component and then overlays a component on top of it streched to its size.  
Constructor:    

```objective-c
+ (instancetype)newWithComponent:(CKComponent *)component
                         overlay:(CKComponent *)overlay;
```

Exmaple:    

```objective-c
CKOverlayLayoutComponent *overlayComponent = [CKOverlayLayoutComponent
  newWithComponent: imageComponent
  overlay: labelComponent];
```

### CKBackgroundLayoutComponent
Lays out a single child component, then lays out a background component behind it stretched to its size.  
Constructor:    

```objective-c
/**
 @param component A child that is laid out to determine the size of this component. If this is nil, then this method
        returns nil.
 @param background A child that is laid out behind it. May be nil, in which case the background is omitted.
 */
+ (instancetype)newWithComponent:(CKComponent *)component
                      background:(CKComponent *)background;
```

Exmaple:    

```objective-c
CKBackgroundLayoutComponent *backgroundComponent = [CKBackgroundLayoutComponent
  newWithComponent: labelComponent
  background: imageComponent];
```

### CKStaticLayoutComponent
A component that positions children at fixed positions.  
Computes a size that is the union of all childrens' frames.  
Constructor:    

```objective-c
@param view Passed to the super class initializer.
@param children Children to be positioned at fixed positions.
*/
+ (instancetype)newWithView:(const CKComponentViewConfiguration &)view
                      size:(const CKComponentSize &)size
                  children:(CKContainerWrapper<std::vector<CKStaticLayoutComponentChild>> &&)children;
```

Exmaple:    

```objective-c
CKStaticLayoutComponent *staticComponent = [CKStaticLayoutComponent
  newWithView:{}
  size:{}
  children:{
    {labelComponent},
    {buttonComponent},
    {imageComponent}
  }];
```

### CKRatioLayoutComponent
For when the content should respect a certain inherent ratio but can be scaled (think photos or videos).  
The ratio passed is the ratio of height / width you expect.  
Constructor:    

```objective-c
+ (instancetype)newWithRatio:(CGFloat)ratio
                        size:(const CKComponentSize &)size
                   component:(CKComponent *)component;
```

Exmaple:    

```objective-c
CKRatioLayoutComponent *ratioComponent = [CKRatioLayoutComponent
  newWithRatio:0.5
  size:{}
  component:imageComponent];
```

## Apply in UICollectionView
The collection view in ComponentKit is really different from the collection view in UIKit.    

The UIKit way to add content to a collection view is:    
1. Tell the `UICollectionView` to add/insert/update items or sections.  
2. Synchronously, the `UICollectionView` ask its data source for number of items, sections and layout info.  
3. Depending on whether or not the updated index paths are visible the `UICollectionView` will Synchronously call `cellForItemAtIndexPath:`.  
4. Finally, the data source returns a configured cell for this index path.  

The ComponentKit uses an idiom that is more "reactive":    
1. Tell the `CKCollectionViewTransactionalDataSource` to add/insert/update items or sections.  
2. Asynchronously, and in the background, computes the corresponding components.  
3. When the computation is done, apply the changes to the  `UICollectionView`.  

ComponentKit really shines when used with a `UICollectionView`:    
1. **Automatic reuse**. You just need to setup the components and ComponentKit will automaitc reuse and reconfiguration.  
2. **Flexible height**. You don't have to compute the height of each items yourself.  
3. **Scroll performance**. ComponentKit addresses common scroll performance issues holistically.  
4. **Simple to use**. You just need to care about two things, **manipulating data** and **providing components**.

Steps to use ComponentKit in `UICollectionView`:    

1、ViewController()<CKComponentProvider>
ViewController需要遵守`CKComponentProvider`协议，实现`+componentForModel: context:`方法，将model转换为component。  
在该方法中，通过不同类型的model返回不同类型的component。  

```objective-c
+ (CKComponent *)componentForModel:(id<NSObject>)model
                           context:(id<NSObject>)context {
  if ([model isKindOfClass:[NewsModel class]]) {
    return [NewsComponent newWithNewsModel:model context:context];
  }
  return nil;
}
```

2、FlowLayout
创建`UICollectionViewFlowLayout`对象，设置滑动方向、间距参数等。  

```objective-c
UICollectionViewFlowLayout *flowLayout = [[UICollectionViewFlowLayout alloc] init];
[flowLayout setScrollDirection:UICollectionViewScrollDirectionVertical];
[flowLayout setMinimumInteritemSpacing:0];
[flowLayout setMinimumLineSpacing:0];
```

3、CollectionView
创建`UICollectionView`。  

```objective-c
self.collectionView = [[UICollectionView alloc] initWithFrame:self.view.bounds collectionViewLayout:flowLayout];
self.collectionView.delegate = self;
self.collectionView.backgroundColor = [UIColor blackColor];
[self.view addSubview:self.collectionView];
```

4、Item size range
Item size range。通过设置`CKComponentSizeRangeFlexibleHeight`使item的高度自适应。  

```objective-c
const CKSizeRange sizeRange = [[CKComponentFlexibleSizeRangeProvider
      providerWithFlexibility:CKComponentSizeRangeFlexibleHeight]
     sizeRangeForBoundingSize:self.collectionView.bounds.size];
```

5、Context
Context可以是任何不可变对象，创建component时的不可变上下文信息，比如设备类型，图片下载器。  
`MyContext *context = [MyContext new];`  
预先在主线程加载图片。  

6、Configuration
`CKTransactionalComponentDataSourceConfiguration`，需要 **ComponentProvider**，**sizeRange**，**context** 三个参数。  

```objective-c
CKTransactionalComponentDataSourceConfiguration *configuration =
      [[CKTransactionalComponentDataSourceConfiguration alloc]
          initWithComponentProvider:[self class]
                            context:context
                          sizeRange:sizeRange];
```

7、DataSource
`CKCollectionViewTransactionalDataSource`,需要 **collectionView**，**supplementaryViewDataSource**，**configuration** 三个参数。  

```objective-c
self.dataSource = [[CKCollectionViewTransactionalDataSource alloc]
           initWithCollectionView:self.collectionView
      supplementaryViewDataSource:nil
                    configuration:configuration];
```

8、Initial changeset
需要初始化DataSource，即向DataSource中添加Section。  

```objective-c
CKTransactionalComponentDataSourceChangeset *initialChangeset =
  [[[CKTransactionalComponentDataSourceChangesetBuilder
      transactionalComponentDataSourceChangeset]
      withInsertedSections:[NSIndexSet indexSetWithIndex:0]] build];
[self.dataSource applyChangeset:initialChangeset
                           mode:CKUpdateModeAsynchronous
                       userInfo:nil];
```

9、Insert/Update items
向DataSource中插入Items才能显示。  

```objective-c
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
```

10、ViewController()<UICollectionViewDelegate, UICollectionViewDelegateFlowLayout>

```objective-c
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
```

## Flexbox layout
**flex container**：容器。  
main axis：main start， main end。  
cross axis：cross start， cross end。  
**flex item**：成员。  
main size。  
cross size。  

### Container Properties
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

### Item Properties
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

## FBSnapshotTestCase
`FBSnapshotTestCase` takes a configured `UIView` or `CALayer` and uses the `renderInContext:` method to get an image snapshot of its contents. It compares this snapshot to a "reference image" stored in your source code repository and fails the test if the two images don't match.  
It makes the comparison by drawing both the view or layer and the existing snapshot into two `CGContextRefs` and doing a memory comparison of them with the C function `memcmp()`.

### Features
1. It is esay to understand and visible and quickly.
2. Automatically names reference images on disk according to test class and selector.
3. Prints a descriptive error message to the console on failure.
4. Supply an optional "identifier" if you want to perform multiple snapshots in a single test method.
5. Support for `UIView` via `FBSnapshotVerifyView`.
6. Support for `CALayer` via `FBSnapshotVerifyLayer`.
7. Support for `CKComponent` via `FBSnapshotVerifyComponent`.
8. `isDeviceAgnostic` to allow appending the device model (iPhone, etc), OS version and screen size to the images (allowing to have multiple tests for the same «snapshot» for different OSs and devices).
9. Provide a diff image to show the difference when test failed.
10. It's esay to test different state of the view.
11. Snapshot tests are run at the same time as the rest of your tests. They can mostly be run without pushing the view to the screen.
12. Snapshots give code reviews a narrative.
13. Snapshot tests are fast.

### Disadvantages
1. Testing asynchronous code is hard.
2. Some components can be hard to test.
3. Apple’s OS patches can change the way their stock components are rendered.
4. Each snapshot is a PNG file stored in your repository, and together they average out at about 30-100kb per file.

### Install
1. `pod 'FBSnapshotTestCase'`.
2. Define `FB_REFERENCE_IMAGE_DIR` and `IMAGE_DIFF_DIR` in scheme(Edit Scheme -> Run -> Arguments -> Environment Variables).  
   `FB_REFERENCE_IMAGE_DIR` = `$(SOURCE_ROOT)/$(PROJECT_NAME)Tests/ReferenceImages`  
   `IMAGE_DIFF_DIR` = `$(SOURCE_ROOT)/$(PROJECT_NAME)Tests/FailureDiffs`  

### Create a snapshot test
1. Subclass `FBSnapshotTestCase` instead of `XCTestCase`.
2. From within your test, use `FBSnapshotVerifyComponent`.
3. Run the test once with `self.recordMode = YES;` in the test's `-setup` method. (This creates the reference images on disk)
4. Run the test again with `self.recordMode = NO;`.

### Notes
Your unit test must be an "application test", not a "logic test." (That is, it must be run within the Simulator so that it has access to `UIKit`.)

### Reference
[FBSnapshotTestCase](https://github.com/facebook/ios-snapshot-test-case)  
[Snapshot Testing](https://www.objc.io/issues/15-testing/snapshot-testing/)  
[Snapshot Plugin](https://github.com/orta/snapshots)  
