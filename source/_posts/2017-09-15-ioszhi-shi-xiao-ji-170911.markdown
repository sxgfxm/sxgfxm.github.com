---
layout: post
title: "iOS知识小集-170911"
date: 2017-09-15 17:20:49 +0800
comments: true
categories: iOS
keywords: sxgfxm
description: sxgfxm
---

## 长按语音控制

```
-(void)setupBtn{
  //  按下按钮
  [self.voiceBtn addTarget:self action:@selector(btnTouchBegin:) forControlEvents:UIControlEventTouchDown];
  //  立刻松开
  [self.voiceBtn addTarget:self action:@selector(btnTouchEnd:) forControlEvents:UIControlEventTouchUpInside];
  //  上划取消
  [self.voiceBtn addTarget:self action:@selector(cancelSpeak) forControlEvents:UIControlEventTouchDragExit];
  //  外部松开
  [self.voiceBtn addTarget:self action:@selector(btnTouchEnd:) forControlEvents:UIControlEventTouchUpOutside];
}

//  通过计时器区分“点击”还是“长按”
- (void)btnTouchBegin:(UIButton *)button {
  self.countNum = 0.0f;
  self.timer =
      [NSTimer scheduledTimerWithTimeInterval:0.1 target:self selector:@selector(handleTimer) userInfo:nil repeats:YES];
  [self.timer fire];
}

- (void)btnTouchEnd:(UIButton *)button {
  [self.timer invalidate];

  if ([self isLongPressGesture]) {
    //  执行长按操作
  } else {
    //  执行点击操作
  }
}

// 处理时间
- (void)handleTimer {
  self.countNum += 0.1;

  if ([self isLongPressGesture]) {
    [self.timer invalidate];
    [self longPressVoiceBegin];
  }
}

// 判断是否为长按手势
- (BOOL)isLongPressGesture {
  return self.countNum >= 0.5;
}

// 上划取消语音输入
- (void)cancelSpeak {
  [self voiceEnd];
}  


```