---
layout: post
title: "iOS知识小集-170410"
date: 2017-05-17 16:25:45 +0800
comments: true
categories: iOS
keywords: sxgfxm,ComponentKit Tutorial,Flex box,Component Controllers
description: sxgfxm,ComponentKit Tutorial,Flex box,Component Controllers 
---

# ComponentKit Tutorial - Introduction

## Philosophy
Components are immutable objects that specify how to configure views.  
**Declarative** : You declare the subcomponents of your component.  
**Functional** : Data flows in one direction.  
**Composable** : Reusing it is a one-liner.   

<!-- more -->

## Flex box
**main axis**: The main axis of a flex container is the primary axis along which flex items are laid out. It extends in the main dimension.  
**main-start,main-end**: The flex items are placed within the container starting on the main-start side and going toward the main-end side.  
**main size**: The flex item’s main size property is either the width or height property, whichever is in the main dimension.  
**cross axis**: The axis perpendicular to the main axis is called the cross axis. It extends in the cross dimension.  
**cross-start,cross-end**: Flex lines are filled with items and placed into the container starting on the cross-start side of the flex container and going toward the cross-end side.  
**cross size**: The cross size property is whichever of width or height that is in the cross dimension.  

## Uses
**Strengths**:  
Simple and Declarative: Just like React itself. Why React? sums up these benefits.  
Scroll Performance: All layout is performed on a background thread, ensuring the main thread isn’t tied up measuring text.  
View Recycling: By requiring all view configurations to be expressed declaratively, ComponentKit makes error-free view recycling automatic.  
Composability: By encouraging heavy use of composition, it’s possible to build UIs as complex as News Feed without any single component exceeding 300 lines of code.  
**Considerations**:  
Interfaces that aren’t lists or tables aren’t ideally suited to ComponentKit since it is optimized to work well with a UICollectionView.  
ComponentKit is fully native and compiled. React Native offers an alternative based on JavaScriptCore and React, including features like instant reload with no recompilation.  
Dynamic gesture-driven UIs are currently hard to implement in ComponentKit; consider using AsyncDisplayKit.  
ComponentKit is built on Objective-C++. There is no easy way to interoperate with Swift since Swift cannot bridge to C++.  

## Component API
~~~
@interface CKComponent : NSObject

/** Returns a new component. */
+ (instancetype)newWithView:(const CKComponentViewConfiguration &)view
                       size:(const CKComponentSize &)size;

@end
~~~
Notes:  
A component is totally immutable. For example, there is no `addSubcomponent:` method.  
A component can be created on any thread. This helps keep all sizing and construction operations off the main thread.  
The Objective-C idiom `+newWith...` is used for instantiation instead of the more typical `+alloc/-initWith...` This is mainly for brevity. Getting rid of noise is important to keep components code readable.  

## Composite Components
Avoid subclassing `CKComponent directly`. Instead, subclass `CKCompositeComponent`.  
A “composite component” simply wraps another component, hiding its implementation details from the outside world.   



~~~
@implementation ShareButtonComponent

+ (instancetype)newWithArticle:(ArticleModel *)article
{
  return [super newWithComponent:
          [CKButtonComponent
           newWithTitles:...
           titleColors:...]];
}

- (void)shareTapped
{
  // Share the article
}

@end
~~~

## Views
~~~
struct CKComponentViewConfiguration {
  CKComponentViewClass viewClass;
  std::unordered_map<CKComponentViewAttribute, id> attributes;
};
~~~
The first field is a view class. Ignore `CKComponentViewClass` for now — in most cases you just pass a class like `[UIImageView class]` or `[UIButton class]`.  
The second field holds a map of attributes to values: font, color, background image, and so forth. Again, ignore `CKComponentViewAttribute` for now; you can usually use a `SEL` as the attribute.    



~~~
[CKComponent
 newWithView:{
   [UIImageView class],
   {
     {@selector(setImage:), image},
     {@selector(setContentMode:), @(UIViewContentModeCenter)} // Wrapping into an NSNumber
   }
 }
 size:{image.size.width, image.size.height}];
~~~
In such situations, just pass {} for the view configuration and no view is created.

## Layout
`CKComponent` instances do not have any size or position information. Instead, ComponentKit calls the `layoutThatFits:` method with a given size constraint and the component must return a structure describing both its size, and the position and sizes of its children.  



~~~
struct CKComponentLayout {
  CKComponent *component;
  CGSize size;
  std::vector<CKComponentLayoutChild> children;
};

struct CKComponentLayoutChild {
  CGPoint position;
  CKComponentLayout layout;
};
~~~
**Layout Components**:  
`CKStackLayoutComponent`: It allows you to stack components vertically or horizontally and specify how they should be flexed and aligned to fit in the available space.  
`CKInsetComponent`: Applies an inset margin around a component.  
`CKBackgroundLayoutComponent`: Lays out a component, stretching another component behind it as a backdrop.  
`CKOverlayLayoutComponent`: Lays out a component, stretching another component on top of it as an overlay.  
`CKCenterLayoutComponent`: Centers a component in the available space.  
`CKRatioLayoutComponent`: Lays out a component at a fixed aspect ratio.  
`CKStaticLayoutComponent`: Allows positioning children at fixed offsets.  
If the components above aren’t powerful enough, you can implement `computeLayoutThatFits:` manually.  

## Responder Chain
The ComponentKit responder chain is separate from UIView’s responder chain, so you must manually bridge over to the component responder chain if desired.  
The easiest way to handle taps on UIControl views is to use `CKComponentActionAttribute`.  



~~~
@implementation SomeComponent

+ (instancetype)new
{
  return [self newWithView:{
    [UIButton class],
    {CKComponentActionAttribute(@selector(didTapButton))}
  }];
}

- (void)didTapButton
{
  // Aha! The button has been tapped.
}

@end
~~~

## Component Actions

## State
`State`: Internal to the component, this holds implementation details that the parent should not have to know about.  



~~~
#import "CKComponentSubclass.h" // import to expose updateState:
@implementation MessageComponent

+ (id)initialState
{
  return @NO;
}

+ (instancetype)newWithMessage:(NSAttributedString *)message
{
  CKComponentScope scope(self);
  NSNumber *state = scope.state();
  return [super newWithComponent:
          [CKTextComponent
           newWithAttributes:{
             .attributedString = message,
             .maximumNumberOfLines = [state boolValue] ? 0 : 5,
           }
           viewAttributes:{}
           accessibilityContext:{}]];
}

- (void)didTapContinueReading
{
  [self updateState:^(id oldState){ return @YES; } mode:CKUpdateModeAsynchronous];
}

@end
~~~

## Scopes
`Scopes` give components a persistent, unique identity. They’re needed in three cases:  
Components that have `state` must have a scope.  
Components that have a `controller` must have a scope.  
Components that have child components with state or controllers may need a scope, even if they don’t have state or controllers.  
Use the `CKComponentScope` type to define a component scope at the top of a component’s `+new` method.  



~~~
+ (instancetype)newWithModel:(Model *)model
{
  CKComponentScope scope(self, model.uniqueID);
  ...
  return [super newWithComponent:...];
}
~~~
If your component doesn’t have a model object with a unique identifier, you can omit that parameter as long as there won’t be multiple siblings of the same type.  



~~~
CKComponentScope scope(self);
~~~

## Component Controllers
Every time something changes, an entirely new component is created and the old one is thrown away.  
This means components are short-lived, and their lifecycle is not under your control.  
But sometimes, you do need **an object with a longer lifecycle**. Component controllers fill that role:  
Components can’t be delegates because they are short-lived, but component controllers can be delegates.  
Network downloads take time to complete; the component may have been recreated by the time the download completes. The controller can handle the callback.  
You may need an object to own some other object that should have a long lifetime.  
Controllers are instantiated automatically by ComponentKit. Don’t try to create them manually.  
There is a only a one-way communication channel between the component and its component controller - you can only pass data off of a component to a component controller.  
A component has no reference its corresponding component controller. This is by design.  
To pass data from a component to its controller, expose a `@property` on the component in a class extension.  
The controller can initialize itself with the properties in `initWithComponent:`.  

## Lifecycle Methods
Whenever possible, avoid using lifecycle methods. Think of them as an emergency escape hatch for integrating with stateful code.  
Mounting -> Remounting -> Unmounting -> Updating  

## Animation
animationsOnInitialMount: Override this method to specify how to animate the initial appearance of a component.  
animationsFromPreviousComponent: Override this method to specify how to animate between two versions of a component.  
boundsAnimationFromPreviousComponent: Override this method to specify how the top-level bounds of a component should animate inside a `UICollectionView`.  
If you implement either method, your component must have a `scope`.  
