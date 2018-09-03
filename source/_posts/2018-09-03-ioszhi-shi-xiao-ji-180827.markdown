---
layout: post
title: "iOS知识小集-180827"
date: 2018-09-03 11:36:54 +0800
comments: true
categories: iOS
keywords: sxgfxm, lldb
description: sxgfxm, lldb
---

## lldb

### 什么是lldb
LLDB是个开源的内置于XCode的具有REPL(read-eval-print-loop)特征的Debugger，其可以安装C++或者Python插件。

<!-- more -->

### expression
执行一个表达式，并将结果输出。  

#### expression
`expression -- self.view.backgroundColor = [UIColor redColor]`
`expression -- (void)[CATransaction flush]`

#### print、p、call
expression的别名，常用与输出某个变量。

#### po
等价于`expression -O -- variable`，输出对象本身的信息，而非对象地址。

### thread
与线程相关操作，可以查看调用栈信息，修改返回值。

#### thread backtrace、bt
打印当前线程的调用栈信息，可以设置打印帧数，从哪个帧开始打印，是否额外显示回溯。

#### thread return
修改某个函数的返回值。

#### c、n、s、finish
continue、next、step in、step out。

### breakpoint
与断点相关操作，可以设置单个断点、批量设置断点、为断点增加命令，删除断点及断点命令。

#### breakpoint set
设置断点，可以指定`-n`方法名，`-f`文件名，`-l`行数，`-o`单次断点。

#### breakpoint list
查看断点。

#### breakpoint disable/enable
设置断点可用、不可用。

#### breakpoint delete
删除断点。

#### breakpoint command add
设置断点命令，可以指定`-o`单行命令，或多行命令。

#### breakpoint command list
查看断点命令。

#### breakpoint command delete
删除断点命令。

### watchpoint
为地址设置断点。

#### watchpoint set
添加观察点，只可接受变量。

#### watchpoint list
查看观察点。

#### breakpoint disable/enable
设置观察点可用、不可用。

#### watchpoint delete
删除观察点。

#### watchpoint command add
设置观察点命令。

#### watchpoint command list
查看观察点命令。

#### watchpoint command delete
删除观察点命令。

### target
查找地址对应代码位置。

#### target modules lookup、image lookup
查找地址对应代码位置。

#### target stop-hook
停止时执行代码。

### extension
`~/.lldbinit`中设置扩展。

### help
查看命令帮助，如`help`，`apropos`。

### shortcut
快捷键。  
暂停/继续   cmd + ctrl + Y  
断点失效/生效 cmd + Y  
控制台显示/隐藏    cmd + shift + Y  
光标切换到控制台    cmd + shift + C  
清空控制台   cmd + K  
step over   F6  
step into   F7  
step out    F8  

### script
执行Python脚本