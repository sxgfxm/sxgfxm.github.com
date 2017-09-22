---
layout: post
title: "iOS知识小集-170529"
date: 2017-06-09 10:08:09 +0800
comments: true
categories: iOS
keywords: sxgfxm,wifi setting, UITextField, Gradient flow animation, UISlider, UITextView
description: sxgfxm,wifi setting, UITextField, Gradient flow animation, UISlider, UITextView,NSURLSession,Protocol Buffers,IQActionSheetPickerView
---

## Open system wifi settings
`[[UIApplication sharedApplication] openURL:[NSURL URLWithString:@"App-Prefs:root=WIFI"]];`

<!-- more -->

## UITextField
```objective-c
-(void)setupTextField{
  self.wifiNameTF =
      [[UITextField alloc] initWithFrame:CGRectMake(kScaleFrom_iPhone6_Desgin(16), 0,
                                                    self.view.bounds.size.width - kScaleFrom_iPhone6_Desgin(32),
                                                    kScaleFrom_iPhone6_Desgin(54))];
  self.wifiNameTF.placeholder = @"请输入Wi-Fi名称";
  self.wifiNameTF.font = [UIFont systemFontOfSize:kScaleFrom_iPhone6_Desgin(16)];
  self.wifiNameTF.textColor = UIColorFromRGBA(0xff16181d);
  self.wifiNameTF.clearButtonMode = UITextFieldViewModeWhileEditing;
  self.wifiNameTF.autocapitalizationType = UITextAutocapitalizationTypeNone;
  self.wifiNameTF.returnKeyType = UIReturnKeyDone;
  self.wifiNameTF.delegate = self;
  self.wifiNameTF.secureTextEntry = YES;
  self.wifiNameTF.rightView = secureBtn;
  self.wifiNameTF.rightViewMode = UITextFieldViewModeAlways;
  [self.view addSubview:self.wifiNameTF];
}
```

## Gradient flow animation
两倍长度，添加左右移动动画，并添加左右两边覆盖层。  

```objective-c
  //  progress bar
  self.progressBar =
      [[UIView alloc] initWithFrame:CGRectMake((self.view.bounds.size.width - kScaleFrom_iPhone6_Desgin(120)) / 2,
                                               kScaleFrom_iPhone6_Desgin(360), kScaleFrom_iPhone6_Desgin(120),
                                               kScaleFrom_iPhone6_Desgin(4))];
  [self.view addSubview:self.progressBar];
  self.gradientLayer = [CAGradientLayer layer];
  self.gradientLayer.frame =
      CGRectMake(-kScaleFrom_iPhone6_Desgin(120), 0, kScaleFrom_iPhone6_Desgin(240), kScaleFrom_iPhone6_Desgin(4));
  self.gradientLayer.locations = @[ @0, @0.15, @0.3, @0.5, @0.65, @0.8, @1 ];
  self.gradientLayer.colors = @[
    (__bridge id)UIColorFromRGBA(0xff1af28f)
        .CGColor,
    (__bridge id)UIColorFromRGBA(0xff0de9c5).CGColor,
    (__bridge id)UIColorFromRGBA(0xff3293fe).CGColor,
    (__bridge id)UIColorFromRGBA(0xff1af28f).CGColor,
    (__bridge id)UIColorFromRGBA(0xff0de9c5).CGColor,
    (__bridge id)UIColorFromRGBA(0xff3293fe).CGColor,
    (__bridge id)UIColorFromRGBA(0xff1af28f).CGColor
  ];
  self.gradientLayer.startPoint = CGPointMake(0, 0);
  self.gradientLayer.endPoint = CGPointMake(1, 0);
  [self.progressBar.layer addSublayer:self.gradientLayer];
  CALayer *leftMask = [CALayer layer];
  leftMask.frame =
      CGRectMake(-kScaleFrom_iPhone6_Desgin(120), 0, kScaleFrom_iPhone6_Desgin(120), kScaleFrom_iPhone6_Desgin(4));
  leftMask.backgroundColor = [UIColor whiteColor].CGColor;
  [self.progressBar.layer addSublayer:leftMask];
  CALayer *rightMask = [CALayer layer];
  rightMask.frame =
      CGRectMake(kScaleFrom_iPhone6_Desgin(120), 0, kScaleFrom_iPhone6_Desgin(120), kScaleFrom_iPhone6_Desgin(4));
  rightMask.backgroundColor = [UIColor whiteColor].CGColor;
  [self.progressBar.layer addSublayer:rightMask];
  CABasicAnimation *moveAnimation = [CABasicAnimation animationWithKeyPath:@"position.x"];
  moveAnimation.fromValue = @(self.gradientLayer.position.x);
  moveAnimation.toValue = @(self.gradientLayer.position.x + kScaleFrom_iPhone6_Desgin(120));
  moveAnimation.repeatCount = 100;
  moveAnimation.duration = 2;
  moveAnimation.autoreverses = NO;
  [self.gradientLayer addAnimation:moveAnimation forKey:@"animation"];
```

## Alert with text field
```objective-c
- (void)changeName {
  // 修改昵称提示框
  UIAlertController *alertController =
      [UIAlertController alertControllerWithTitle:@"Title"
                                          message:nil
                                   preferredStyle:UIAlertControllerStyleAlert];
  // 添加输入框
  [alertController addTextFieldWithConfigurationHandler:^(UITextField *_Nonnull textField) {
    textField.placeholder = @"Please input";
  }];
  // 添加确定按钮
  [alertController addAction:[UIAlertAction actionWithTitle:@"Done"
                                                      style:UIAlertActionStyleDefault
                                                    handler:^(UIAlertAction *_Nonnull action) {
                                                      // 获得当前输入的字符串
                                                      NSString *name = alertController.textFields[0].text;
                                                      self.tichomeNameLbl.text = name;
                                                    }]];
  // 添加取消按钮
  [alertController addAction:[UIAlertAction actionWithTitle:@"Cancel"
                                                      style:UIAlertActionStyleCancel
                                                    handler:^(UIAlertAction *_Nonnull action){
                                                    }]];
  [self presentViewController:alertController animated:YES completion:nil];
}
```

## UISlider
```objective-c
-(void)setupSlider{
  self.volumeSlider = [UISlider new];
  [self.volumeSlider addTarget:self action:@selector(changeVolumeAction) forControlEvents:UIControlEventTouchUpInside];
  self.volumeSlider.minimumValueImage = [UIImage imageNamed:@"icVolumeDown"];
  self.volumeSlider.maximumValueImage = [UIImage imageNamed:@"icVolumeUp"];
  [self.volumeSlider setThumbImage:[UIImage imageNamed:@"icVolumeBtn"] forState:UIControlStateNormal];
  [self.volumeSlider setMinimumTrackImage:[UIImage imageNamed:@"minTrackBg"] forState:UIControlStateNormal];
  [self.volumeSlider setMaximumTrackImage:[UIImage imageNamed:@"maxTrackBg"] forState:UIControlStateNormal];
  [self.contentView addSubview:self.volumeSlider];
  [self.volumeSlider mas_makeConstraints:^(MASConstraintMaker *make) {
    make.centerX.equalTo(self.contentView.mas_centerX);
    make.bottom.equalTo(self.contentView.mas_bottom).offset(-kScaleFrom_iPhone6_Desgin(15));
    make.width.equalTo(@(kScaleFrom_iPhone6_Desgin(270)));
  }];
}
```

## UITextView
```objective-c
-(void)setupTextView{
  self.textView = [UITextView new];
  self.textView.backgroundColor = [UIColor clearColor];
  self.textView.text = @"placeholder";
  self.textView.textColor = [[UIColor whiteColor] colorWithAlphaComponent:0.2];
  self.textView.font = [UIFont systemFontOfSize:14];
  self.textView.delegate = self;
  self.textView.autocapitalizationType = UITextAutocapitalizationTypeNone;
  self.textView.returnKeyType = UIReturnKeyDone;
  [self.contentView addSubview:self.textView];
}

//  实现placeholder功能
- (BOOL)textViewShouldBeginEditing:(UITextView *)textView {
  if (self.isEmpty) {
    textView.textColor = [UIColor whiteColor];
    textView.text = @"";
    self.isEmpty = NO;
  }
  return YES;
}

- (void)textViewDidEndEditing:(UITextView *)textView {
  if (textView.text.length == 0) {
    self.isEmpty = YES;
    textView.text = @"placeholder";
    textView.textColor = [[UIColor whiteColor] colorWithAlphaComponent:0.2];
  }
  [textView resignFirstResponder];
}

//  实现换行替换为结束编辑
- (BOOL)textView:(UITextView *)textView shouldChangeTextInRange:(NSRange)range replacementText:(NSString *)text {
  if ([text isEqualToString:@"\n"]) {
    [textView resignFirstResponder];
    return NO;
  }
  return YES;
}
```

## IQActionSheetPickerView
```objective-c
-(void)setupPickerView{
  NSArray *dataSource = @[@"item1",@"item2",@"item3"];
  IQActionSheetPickerView *categoryPicker = [[IQActionSheetPickerView alloc] initWithTitle:@"选择" delegate:self];
  //  tool bar
  [categoryPicker setTitleFont:[UIFont systemFontOfSize:17]];
  [categoryPicker setTitleColor:[UIColor whiteColor]];
  [categoryPicker setToolbarTintColor:[UIColorFromRGBA(0xff26272e) colorWithAlphaComponent:0.6]];
  [categoryPicker setDoneButtonAttributes:@{
    kIQActionSheetAttributesForNormalStateKey : @{
      NSForegroundColorAttributeName : [[UIColor whiteColor] colorWithAlphaComponent:0.5],
      NSFontAttributeName : [UIFont systemFontOfSize:15]
    }
  }];
  [categoryPicker setCancelButtonAttributes:@{
    kIQActionSheetAttributesForNormalStateKey : @{
      NSForegroundColorAttributeName : [[UIColor grayColor] colorWithAlphaComponent:0.5],
      NSFontAttributeName : [UIFont systemFontOfSize:15]
    }
  }];
  //  component
  [categoryPicker setPickerViewBackgroundColor:UIColorFromRGBA(0xff1c1d24)];
  [categoryPicker setPickerComponentsFont:[UIFont systemFontOfSize:16]];
  [categoryPicker setPickerComponentsColor:[UIColor whiteColor]];
  //  data source
  [categoryPicker setTitlesForComponents:@[ dataSource ]];
  if (self.category) {
    [categoryPicker setSelectedTitles:@[ @"item2" ]];
  }
  //  show
  [categoryPicker show];
}

- (void)actionSheetPickerView:(IQActionSheetPickerView *)pickerView didSelectTitles:(NSArray *)titles {
  //  do something with selected item
}
```

## NSURLSession
completion handler 并非在主线程回调。  
UI相关操作必须在主线程执行。

## Protocol Buffers
Protocol buffers – a language-neutral, platform-neutral, extensible way of serializing structured data for use in communications protocols, data storage, and more.  
Protocol buffers have many advantages over XML for serializing structured data. Protocol buffers:  
  are simpler  
  are 3 to 10 times smaller  
  are 20 to 100 times faster  
  are less ambiguous  
  generate data access classes that are easier to use programmatically  

1. protoc  
2. build rules  
3. build phases  

## Undefined symbols for architecture arm64
build phases -> compile source

## NSError
error.localizedDescription

## block
循环引用。
