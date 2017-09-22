---
layout: post
title: "iOS知识小集-170417"
date: 2017-05-17 16:33:08 +0800
comments: true
categories: iOS
keywords: sxgfxm, ComponentKit Tutorial,CKComponent,CKLabelComponent,CKButtonComponent
description: sxgfxm, ComponentKit Tutorial,CKComponent,CKLabelComponent,CKButtonComponent 
---

# ComponentKit Tutorial - Component

## CKComponent
A component is an immutable object that specifies how to configure a view, loosely inspired by React.  

```objective-c
+ (instancetype)newWithView:(const CKComponentViewConfiguration &)view
                       size:(const CKComponentSize &)size;
```

Example:  

```objective-c
CKComponent *component = [CKComponent
    newWithView:{
        [UIView class],
        {
            {@selector(setBackgroundColor:),[UIColor redColor]},
            {@selector(setUserInteractionEnabled:), @YES},
            {CKComponentTapGestureAttribute(@selector(didTap))},
            {CKComponentViewAttribute::LayerAttribute(@selector(setCornerRadius:)), @10.0}
        }
    }
    size:{50,50}];

- (void)didTap{
  [self updateState:^(NSNumber *oldState){
    return [oldState boolValue] ? @NO : @YES;
  } mode:CKUpdateModeSynchronous];
}
```

<!-- more -->

## CKLabelComponent
多行文字通过size.width控制。  

```objective-c
CKLabelComponent *titleComponent = [CKLabelComponent newWithLabelAttributes:{
    .string = newsModel.title,
    .color = [UIColor whiteColor],
    .alignment = NSTextAlignmentLeft,
    .font = [UIFont systemFontOfSize:20]
  }
  viewAttributes:{
    { @selector(setBackgroundColor:), [UIColor clearColor] }
  }
  size:{}];
```

## CKButtonComponent
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
    titleFont:[UIFont systemFontOfSize:17]
    selected:NO
    enabled:YES
    action:{@selector(tapAction)}
    size:{}
    attributes:{}
    accessibilityConfiguration:{}];
```

## CKImageComponent
```objective-c
CKImageComponent *image = [CKImageComponent newWithImage:
    [UIImage imageNamed:newsModel.imageURL]
    attributes:{
        { @selector(setBackgroundColor:), [UIColor whiteColor] },
        {CKComponentViewAttribute::LayerAttribute(@selector(setCornerRadius:)), @10.0}
    }
    size:{60, 60}];
```

## CKInsetComponent
A component that wraps another component, applying insets around it.  

```objective-c
+ (instancetype)newWithView:(const CKComponentViewConfiguration &)view
                     insets:(UIEdgeInsets)insets
                  component:(CKComponent *)component;

+ (instancetype)newWithInsets:(UIEdgeInsets)insets component:(CKComponent *)child;
```

Example:  

```objective-c
CKInsetComponent *insetComponent = [CKInsetComponent
    newWithInsets:{.top = 10,.left = 20,.right = 10,.bottom = 20}
    component:[CKComponent
        newWithView:{}
        size:{}]
    ];
```

## CKOverlayLayoutComponent
This component lays out a single component and then overlays a component on top of it streched to its size.  

```objective-c
+ (instancetype)newWithComponent:(CKComponent *)component
                         overlay:(CKComponent *)overlay;
```


## State
Simply ask three questions about each piece of data:  
1. Is it passed in from a parent via props? If so, it probably isn't state.  
2. Does it remain unchanged over time? If so, it probably isn't state.  
3. Can you compute it based on any other state or props in your component? If so, it isn't state.  

For each piece of state in your application:  
1. Identify every component that renders something based on that state.  
2. Find a common owner component (a single component above all the components that need the state in the hierarchy).  
3. Either the common owner or another component higher up in the hierarchy should own the state.  
4. If you can't find a component where it makes sense to own the state, create a new component simply for holding the state and add it somewhere in the hierarchy above the common owner component.  

## Scope
1. Components that have state must have a scope.  
2. Components that have a controller must have a scope.  
3. Components that have child components with state or controllers may need a scope, even if they don’t have state or controllers.  

## ComponentController
与component写在同一个文件里，系统自动创建。  
用来处理代理、事件响应等，有持续的生命周期。  
scope必须唯一，与controller一一对应。  
