---
layout: post
title: "iOS知识小集-170403"
date: 2017-05-17 16:16:33 +0800
comments: true
categories: iOS
keywords: sxgfxm, EXC_BAD_ACCESS, dyld Library not loaded,PlantUML
description: sxgfxm, EXC_BAD_ACCESS, dyld Library not loaded,PlantUML
---

## Heap and Stack
Value types are created on the stack, Reference types are created on the heap.  
Stack is attached to a thread, and Heap is attached to the application.  
Stack is faster than Heap.   

<!-- more -->

## dealloc 是否需要调用[super dealloc]

ARC中不能调用`[super dealloc]`。  

MRC中，必须最后调用`[super dealloc]`。

## set a breakpoint in malloc_error_break to debug
The error message indicates a pointer to a block of freed memory doesn't actually point to free memory.
The most likely cause is that the memory is a freed object instance, but your code as an unretained reference to it and has tried to use it like a "live" instance.  
Click on the breakpoints navigator (looks like a sign post) on the top of the left bar on XCode.  
On the bottom left hand corner there is a plus sign. Click on it.  
Add Symbolic Breakpoint and set malloc_error_break as the symbol.  
Click the next breakpoint button.  


## EXC_BAD_ACCESS
Whenever you encounter EXC_BAD_ACCESS, it means that you are sending a message to an object that has already been released.  
In summary, when you run into EXC_BAD_ACCESS, it means that you try to send a message to a block of memory that can't execute that message.  
Put differently, deallocated objects are kept alive for debugging purposes. If you send a message to a zombie object, your application will still crash as a result of EXC_BAD_ACCESS.  
Click the active scheme in the top left and choose Edit Scheme.  
Select Run on the left and open the Diagnostics tab at the top. To enable zombie objects, tick the checkbox labeled Enable Zombie Objects.   

## dyld: Library not loaded
In the target's General tab, there is an Embedded Binaries field. When you add the framework there the crash is resolved.   

Reference is here on Apple Developer Forums.   

## PlantUML
[PlantUML](http://plantuml.com/)是一个开源项目，支持快速绘制时序图、用例图、类图等常用软件开发示意图。  
优点：通过代码实现图形绘制，逻辑清晰，扩展维护，效率高。  
集成方法：  
1. 下载安装[Atom](https://atom.io/)。
2. Atom -> Preferences -> Packages -> plantuml-viewer。
3. 学习语法，编写代码，自动生成对应图形。
