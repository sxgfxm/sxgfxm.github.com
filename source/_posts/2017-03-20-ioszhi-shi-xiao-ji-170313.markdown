---
layout: post
title: "iOS知识小集-170313"
date: 2017-03-20 16:50:46 +0800
comments: true
categories: iOS
keywords: sxgfxm,Masonry,Autolayout,IQActionSheetPickerView
description: sxgfxm,Masonry,Autolayout,IQActionSheetPickerView
---

## Masonry
需要先添加到父视图，再设置约束。  
block中无需使用weakself。  
添加约束：makeConstrains。  
更新约束：updateConstrains，与之相关的布局自动调整。  
重设约束：remakeConstrains，删除之前的约束重新添加。  



~~~
[self.view updateConstraints:^(MASConstraintMaker *make){
  //  updateConstraints
}];
[self.view updateConstraints];
[self.view setNeedsLayout];
[UIView animateWithDuration:3 animations:^{
  [self.view layoutIfNeeded];
}];
~~~

<!-- more -->

## Autolayout

`setNeedsLayout`：使当前布局失效，并在下一个更新循环中触发布局更新，遍历view的结构。  
`layoutIfNeeded`：强制立即更新布局，可以实现动画效果。  
scrollview自动布局：添加tmpview，在tmpview上添加view，最后约束tmpview和contentsize。  

## 退出viewcontroller事件监听

## IQActionSheetPickerView
```objective-c
IQActionSheetPickerView *picker = [IQActionSheetPickerView actionSheetWithTitle:@"Age" delegate:self];
NSArray *dataSource = @[@"10",@"20",@"30",@"40",@"50",@"60"];
[picker setTitlesForComponents:@[dataSource]];
[picker setSelectedTitles:@[@"20"]];
[picker show];
```

代理回调   

```objective-c
- (void)actionSheetPickerView:(IQActionSheetPickerView *)pickerView didSelectTitles:(NSArray *)titles {
}
```

