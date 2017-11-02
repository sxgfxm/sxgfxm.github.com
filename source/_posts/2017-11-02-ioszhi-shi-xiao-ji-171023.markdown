---
layout: post
title: "iOS知识小集-171023"
date: 2017-11-02 10:34:10 +0800
comments: true
categories: iOS
keywords: sxgfxm,UITableViewCell高度自适应,UIImageView Animation,AVAudioPlayer Simple Use,UIBlurEffect,CIImage & CIFilter,UIView to UIImage
description: sxgfxm,UITableViewCell高度自适应,UIImageView Animation,AVAudioPlayer Simple Use,UIBlurEffect,CIImage & CIFilter,UIView to UIImage
---

## UITableView AccessoryView TintColor
通过设置Cell的TintColor来设置AccessoryView的tintColor。

## Git Rebase
`git rebase`会删除`merge commit`。  
进行merge操作后不要向其amend代码。  

<!-- more -->

## UITableViewCell
注意在返回cell的代理方法中，不要重复创建view。

## UITableViewCell高度自适应

1. `estimatedRowHeight`；
2. `-systemLayoutSizeFittingSize:` + cache height；
3. ComponentKit；

## layoutIfNeeded、setNeedsLayout、setNeedsDisplay

// TODO

## 动态更新Cell高度
```objective-c
  [self.tableView beginUpdates];
  [self.tableView endUpdates];
```

## UIImageView Animation
```objective-c
  self.imageView = [[UIImageView alloc] initWithFrame:CGRectMake(100, 200, 50, 50)];
  self.imageView.animationImages = @[[UIImage imageNamed:@"Image1"],[UIImage imageNamed:@"Image2"],[UIImage imageNamed:@"Image3"]];
  self.imageView.animationDuration = 0.75;
  self.imageView.animationRepeatCount = 10;
  [self.view addSubview:self.imageView];
  [self.imageView startAnimating];
  [self.imageView stopAnimating];
```

## AVAudioPlayer Simple Use
```objective-c
- (AVAudioPlayer *)startAudioPlayer {
  if (!_startAudioPlayer) {
    NSString *path = [NSString stringWithFormat:@"%@/startAudio.wav", [[NSBundle mainBundle] resourcePath]];
    NSURL *soundURL = [NSURL fileURLWithPath:path];
    NSError *error = nil;
    _startAudioPlayer = [[AVAudioPlayer alloc] initWithContentsOfURL:soundURL error:&error];
    if (error) {
      DDLogDebug(@"Can't find audio file %@", error.localizedDescription);
    }
  }
  return _startAudioPlayer;
}
```

```objective-c
  [self.startAudioPlayer play];
  [self.startAudioPlayer pause];
```

## 模糊效果

### UIBlurEffect
可以选择模糊亮度，但无法自定义模糊半径。   

```objective-c
  UIBlurEffect *blurEffect = [UIBlurEffect effectWithStyle:UIBlurEffectStyleLight];
  UIVisualEffectView *blurView = [[UIVisualEffectView alloc] initWithEffect:blurEffect];
  blurView.frame = myView.bounds;
  [myView addSubview:blurView];
```

### CIImage & CIFilter
可以选择滤波算法，并设置滤波参数。  

```objective-c
- (UIImage *)convertToBlurImage:(UIImage *)image {
  //  create input image
  CIImage *inputImage = [CIImage imageWithCGImage:[image CGImage]];
  //  create filter
  CIFilter *gaussianBlurFilter = [CIFilter filterWithName:@"CIGaussianBlur"];
  //  set filter parameters
  [gaussianBlurFilter setDefaults];
  [gaussianBlurFilter setValue:inputImage forKey:kCIInputImageKey];
  [gaussianBlurFilter setValue:@6 forKey:kCIInputRadiusKey];
  //  create output image
  CIImage *outputImage = [gaussianBlurFilter outputImage];
  CIContext *context = [CIContext contextWithOptions:nil];
  CGImageRef cgimg = [context createCGImage:outputImage fromRect:[inputImage extent]];
  //  get UIImage
  UIImage *convertedImage = [UIImage imageWithCGImage:cgimg];
  return convertedImage;
}
```

## UIView to UIImage
```objective-c
- (UIImage *)imageFromUIView:(UIView *)view {
  UIGraphicsBeginImageContextWithOptions(view.bounds.size, YES, 0.0);
  //  render view in context
  [view.layer renderInContext:UIGraphicsGetCurrentContext()];
  //  get image from context
  UIImage *image = UIGraphicsGetImageFromCurrentImageContext();
  UIGraphicsEndImageContext();
  return image;
}
```

