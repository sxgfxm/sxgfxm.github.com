---
layout: post
title: "iOS知识小集-201905"
date: 2020-01-17 11:32:44 +0800
comments: true
categories: 
keywords: sxgfxm, Flutter
description: Flutter
---

## 2019.05.05

Flutter 状态管理  
Flutter的很多灵感来自于React，它的设计思想是数据与视图分离，由数据映射渲染视图。所以在Flutter中，它的Widget是immutable的，而它的动态部分全部放到了状态(State)中。  
[Scoped Model](https://juejin.im/post/5b97fa0d5188255c5546dcf8) 
[Redux](https://juejin.im/post/5ba26c086fb9a05ce57697da)  
[BLoC](https://juejin.im/post/5bb6f344f265da0aa664d68a)  
[BLoC](https://medium.com/flutter-community/why-use-rxdart-and-how-we-can-use-with-bloc-pattern-in-flutter-a64ca2c7c52d)  

## 2019.05.06
[Widget - State - BuildContext - InheritedWidget](https://medium.com/flutter-community/widget-state-buildcontext-inheritedwidget-898d671b7956)    

1. Widgets: related to layout;  
2. Widgets Tree: widgets are organized in tree structures;  
3. BuildContext: a reference to the location of a widget with in the widgets tree. a BuildContext only belongs to one widget.  
4. Stateless Widget: 创建之后就无法再改变的控件，比如Text，Row，Container，不会rebuild；  
5. Stateful Widget: 创建之后可以根据状态发生变化；widget.color；  
6. State: 状态的改变会影响控件的行为和布局，触发rebuild；State会和BuildContext绑定，绑定之后称为mounted，只有mounted之后，才可以setState；State mounted之后，不能直接被其他BuildContext访问；  
7. Stateful Widget Life Circle: constructor() -> createState() -> constructor() -> initState(){ controllers, animations, ... } -> mounted -> didChangeDependencies(){ listeners } -> build() { build widgets } -> dispose() { controllers, listeners, ...} ；  
8. 在 widget 的生命周期中，是否需要根据变量的变化来 rebuild widget；  
9. Widget unique identity - key：widgets的唯一标识，可以通过key来获取widget；  
10. State -> BuildContext -> Widget Instance：通过key来访问 state；  
11. InheritedWidget：实现在widget tree中高效O(1)共享信息；  

[Reactive Programming - Streams - BLoC](https://medium.com/flutter-community/reactive-programming-streams-bloc-6f0d2bd2d248)  

1. **BlocBase**  
2. **BlocProvider ** 
3. **ApplicationBloc ** 
4. **LoginBloc ** 
4. RxDart  

dependencies:  
  bloc:^0.8.0  
  flutter_bloc:^0.5.0  
  equatable:^0.1.6  

## 2019.05.07
[bloc](https://felangel.github.io/bloc/#/)  

## 2019.05.13
构造方法：  
    构造方法不会被子类继承；  
    convenient constructors；  
    named constructors；  
    initializer list；  
    initializer list -> superclass's no-arg constructor -> main class's no-arg constructor；  
    redirecting constructors；  
    factory constructors；  

## 2019.05.14
使用SingleChildScrollView避免超出边界。  
本地图片路径名称。  
shared_preference 引入问题，增加pod xcconfig，编码问题。  