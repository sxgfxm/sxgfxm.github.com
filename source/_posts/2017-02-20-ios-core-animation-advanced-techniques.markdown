---
layout: post
title: "iOS Core Animation Advanced Techniques"
date: 2017-02-20 22:10:40 +0800
comments: true
categories: iOS
keywords: sxgfxm,CALayer,UIView,Core Animation
description: iOS Core Animation Advanced Techniques
---

# Introduction

本文主要为**iOS Core Animation Advanced Techniques**的笔记。

<!--more-->

##CALayer和UIView

1.CALayer和UIView有什么关系？  
UIView是对CALayer的封装，可以处理touch事件。  
UIView是通过CALayer显示的。  
UIView会自动重绘，CALayer需要手动重绘。  
UIView是二维的，CALayer是三维的。  
UIView支持autoLayout，CALayer不支持autoLayout。  
2、CALayer的特性是什么？  
可以绘制阴影、圆角、彩色边框；  
可以处理3D变换；  
可以处理非矩形边界；  
可以处理透明遮罩；  
可以处理多级非线性动画。  
3、什么情况下会使用CALayer？  
开发跨平台应用；  
与特殊图层打交道；  
提高性能。  
4、为什么不把CALayer和UIView合二为一，而是有两套并列的层次结构？  
把绘图和事件处理分离，是为了减少重复的代码。  
在Mac OS上已经有CALayer，但在iPhone和Mac的交互是有本质不同的，所以UIView来处理。  

###Contents
1、如何让UIView等比清晰显示图片的右上角？  
`layer.contents = (__bridge id)image.CGImage;`  
`layer.contentsGravity = kCAGravityResizeAspect;`  
`layer.contentsScale = [UIScreen mainScreen].scale;`  
`layer.contentsRect = CGRectMake(0.5,0,0.5,0.5);`  
2、-drawRect:方法的作用是什么？空的-drawRect:方法有何影响？  
UIView自定义绘图。  
如果自己实现-drawRect:方法，会为view创建一个backing image。  
3、如何绘制带阴影的有裁剪效果的UIView？  
设置layer阴影；  
通过layer代理绘图；  

###Geometry
1、center, position的区别？  
center对应view；position对应layer。  
两者都表示anchorPoint在父视图中的位置。  
因为UIView并没有暴露anchorPoint属性，所以被称作center。  
2、anchorPoint理解？  
控制点。  
3、frame的理解？  
是否可以改变view的frame而不改变layer的frame。  
frame是由bounds，position和transform计算而来的。  
4、坐标转换？  



```
- (CGPoint)convertPoint:(CGPoint)point fromLayer:(CALayer *)layer;
- (CGPoint)convertPoint:(CGPoint)point toLayer:(CALayer *)layer;
- (CGRect)convertRect:(CGRect)rect fromLayer:(CALayer *)layer;
- (CGRect)convertRect:(CGRect)rect toLayer:(CALayer *)layer;
```
5、坐标翻转          

`layer.geometryFlipped = YES;`  

6、如何在不修改layer层次结构的情况下，使下层的layer在顶层显示？  
zPosition，用来改变layer的显示层次；  
zAnchorPoint?  
7、如何用CALayer实现touch handling？  
-containsPoint:  
-hitTest: 严格按照layer tree的层次结构判断，与zPosition无关。  
8、如何实现CALayer的自动布局？  
`- (void)layoutSublayersOfLayer:(CALayer *)layer;`，  
当bounds改变或调用`-setNeedsLayout`时触发。  

###Visual Effects
1、如何实现圆角？曲率不同的圆角？  



```
layer.cornerRadius = 5.0f;
layer.maskToBounds = YES;
```
2、如何实现彩色边框？  



```
layer.borderWidth = 1;
layer.borderColor = [UIColor redColor].CGColor;
```
border只与bounds相关。  
3、如何添加阴影？  



```
layer.shadowOpacity = 1;
layer.shadowColor = [UIColor redColor].CGColor;
layer.shadowOffset = CGSizeMake(0,1);
layer.shadowRadius = 5;
```
shadow与形状有关。  
如何添加maskToBounds = YES时的阴影？  
两层。  
如何绘制不规则阴影？  
`layer.shadowPath = path;`
4、不规则裁剪，动态裁剪  
`layer.mask = maskLayer;`
5、scaling filter  
kCAFilterNearest，适用于无对角线，对比明显的图片，保留像素，像素图；  
kCAFilterLinear，适用于复杂图片，保留轮廓；  
6、group opacity  
opacity作用于层次结构，0.5+0.5 = 0.75  
UIViewGroupOpacity = YES，Info.plist，降低性能。  
`layer.shouldRasterize = YES`，在渲染前合成一张图片，防止alpha值影响。  
`layer.rasterizationScale = [UIScreen mainScreen].scale;`  

###Transform
1、Affine Transforms，仿射变换  
CGAffineTransform，用来表示二维旋转，缩放和变换。对一个2D点做2D变换。  
它是一个3行2列的矩阵。  
做运算时需要扩展。  
变换前平行的线，变换后依然平行。  
2、创建仿射变换  



```
CGAffineTransformMakeRotation(CGFloat angle);
CGAffineTransformMakeScale(CGFloat sx, CGFloat sy);
CGAffineTransformMakeTranslation(CGFloat tx, CGFloat ty);
```
角度弧度转换  



```
#define RADIANS_TO_DEGREES(x) ((x)/M_PI*180.0)
#define DEGREES_TO_RADIANS(x) ((x)/180.0*M_PI)
```
3、连续变换  



```
CGAffineTransformRotate(CGAffineTransform t, CGFloat angle);
CGAffineTransformScale(CGAffineTransform t, CGFloat sx, CGFloat sy);
CGAffineTransformTranslate(CGAffineTransform t, CGFloat tx, CGFloat ty);
或
CGAffineTransformConcat(CGAffineTransform t1, CGAffineTransform t2);
```
单位矩阵，CGAffineTransformIdentity。  
变换顺序不同，结果不同。因为矩阵运算不符合交换律。  
4、shear变换  



```
CGAffineTransform CGAffineTransformMakeShear(CGFloat x, CGFloat y) {
  CGAffineTransform transform = CGAffineTransformIdentity; transform.c = -x;
  transform.b = y;
  return transform;
}
```
5、3D Transform，3D变换  
CATransform3D，是一个4行4列的矩阵。对一个3D点做3D变换。  



```
CATransform3DMakeRotation(CGFloat angle, CGFloat x, CGFloat y, CGFloat z);
CATransform3DMakeScale(CGFloat sx, CGFloat sy, CGFloat sz);
CATransform3DMakeTranslation(Gloat tx, CGFloat ty, CGFloat tz);
```
6、Perspective Projection，透视  
矩阵中m34的值用来设置透视，值越小透视约明显，值越大透视越不明显。  
7、The Vanishing Point，消失点  
定义消失点为anchorPoint。  
8、sublayer共享透视视角  
设置sublayerTransform  
9、Backfaces，  
layer是两面都有的。可以设置取消。  

##Specialized Layers

###CAShapeLayer
CAShapeLayer比Core Graphics效率更高，硬件加速；  
CAShapeLayer节省内存空间，不会创建backing image；  
CAShapeLayer不会受bounds限制，Core Graphics不行；  
CAShapeLayer不会变换后不会像素化。  
基本使用方法：  
```
CAShapeLayer *shapeLayer = [CAShapeLayer layer];
shapeLayer.strokeColor = [UIColor redColor].CGColor;
shapeLayer.fillColor = [UIColor clearColor].CGColor;
shapeLayer.lineWidth = 5;
shapeLayer.lineJoin = kCALineJoinRound;
shapeLayer.lineCap = kCALineCapRound;
shapeLayer.path = path.CGPath;
```

###CATextLayer
UILabel，通过layer代理方法使用CG绘制string。  
CATextLayer，用于显示文字，特效，效率比UILabel高。  
基本使用方法：  
```
//set text attributes
textLayer.foregroundColor = [UIColor blackColor].CGColor;
textLayer.alignmentMode = kCAAlignmentJustified;
textLayer.wrapped = YES;

//choose a font
UIFont *font = [UIFont systemFontOfSize:15];

//set layer font
CFStringRef fontName = (__bridge CFStringRef)font.fontName;
CGFontRef fontRef = CGFontCreateWithFontName(fontName);
textLayer.font = fontRef;
textLayer.fontSize = font.pointSize;
CGFontRelease(fontRef);

//text
textLayer.string = @"Text";

//scale
textLayer.contentsScale = [UIScreen mainScreen].scale;
```
富文本：  
```
//create attributed string
NSMutableAttributedString *string = nil;
string = [[NSMutableAttributedString alloc] initWithString:text];

//convert UIFont to a CTFont
CFStringRef fontName = (__bridge CFStringRef)font.fontName; CGFloat fontSize = font.pointSize;
CTFontRef fontRef = CTFontCreateWithName(fontName, fontSize, NULL);

//set text attributes
NSDictionary *attribs = @{
  (__bridge id)kCTForegroundColorAttributeName:(__bridge id)[UIColor blackColor].CGColor,
  (__bridge id)kCTFontAttributeName: (__bridge id)fontRef
};
[string setAttributes:attribs range:NSMakeRange(0, [text length])];
attribs = @{
  (__bridge id)kCTForegroundColorAttributeName: (__bridge id)[UIColor redColor].CGColor,
  (__bridge id)kCTUnderlineStyleAttributeName: @(kCTUnderlineStyleSingle),
  (__bridge id)kCTFontAttributeName: (__bridge id)fontRef
};
[string setAttributes:attribs range:NSMakeRange(6, 5)];

//release the CTFont we created earlier
CFRelease(fontRef);

//set layer text
textLayer.string = string;
```
修改view的根layer。  
```
+ (Class)layerClass {
  //this makes our label create a CATextLayer
  //instead of a regular CALayer for its backing layer
  return [CATextLayer class];
}
- (CATextLayer *)textLayer {
  return (CATextLayer *)self.layer;
}
```

###CATransformLayer
保存变换后的layer  

###CAGradientLayer
用于绘制渐变层。  
线性渐变。  
```
//create gradient layer and add it to our container view
CAGradientLayer *gradientLayer = [CAGradientLayer layer];
gradientLayer.frame = self.containerView.bounds;
[self.containerView.layer addSublayer:gradientLayer];

//set gradient colors
gradientLayer.colors = @[
  (__bridge id)[UIColor redColor].CGColor,
  (__bridge id)[UIColor blueColor].CGColor,
  (__bridge id)[UIColor greenColor].CGColor
];

//set locations
gradientLayer.locations = @[@0.0, @0.25, @0.5];

//set gradient start and end points
gradientLayer.startPoint = CGPointMake(0, 0);
gradientLayer.endPoint = CGPointMake(1, 1);
```
放射性渐变。  

###CAReplicatorLayer
用于高效绘制相似的layer。  
```
//create a replicator layer and add it to our view
CAReplicatorLayer *replicator = [CAReplicatorLayer layer];
replicator.frame = self.containerView.bounds;
[self.containerView.layer addSublayer:replicator];

//configure the replicator
replicator.instanceCount = 10;

//apply a transform for each instance
CATransform3D transform = CATransform3DIdentity;
transform = CATransform3DTranslate(transform, 0, 200, 0);
transform = CATransform3DRotate(transform, M_PI / 5.0, 0, 0, 1);
transform = CATransform3DTranslate(transform, 0, -200, 0);
replicator.instanceTransform = transform;

//apply a color shift for each instance
replicator.instanceBlueOffset = -0.1;
replicator.instanceGreenOffset = -0.1;

//create a sublayer and place it inside the replicator
CALayer *layer = [CALayer layer];
layer.frame = CGRectMake(100.0f, 100.0f, 100.0f, 100.0f);
layer.backgroundColor = [UIColor whiteColor].CGColor;
[replicator addSublayer:layer];
```
绘制镜面  
```
+ (Class)layerClass {
  return [CAReplicatorLayer class];
}
- (void)setUp {
  //configure replicator
  CAReplicatorLayer *layer = (CAReplicatorLayer *)self.layer;
  layer.instanceCount = 2;

  //move reflection instance below original and flip vertically
  CATransform3D transform = CATransform3DIdentity;
  CGFloat verticalOffset = self.bounds.size.height + 2;
  transform = CATransform3DTranslate(transform, 0, verticalOffset, 0);
  transform = CATransform3DScale(transform, 1, -1, 0);
  layer.instanceTransform = transform;

  //reduce alpha of reflection layer
  layer.instanceAlphaOffset = -0.6;
}

- (id)initWithFrame:(CGRect)frame {
  //this is called when view is created in code
  if ((self = [super initWithFrame:frame])) {
    [self setUp];
  }
  return self;
}
```

###CAScrollLayer
用于绘制可以滑动的layer。  
```
+ (Class)layerClass {
  return [CAScrollLayer class];
}

- (void)setUp {
  //enable clipping
  self.layer.masksToBounds = YES;

  //attach pan gesture recognizer
  UIPanGestureRecognizer *recognizer = nil;
  recognizer = [[UIPanGestureRecognizer alloc] initWithTarget:self action:@selector(pan:)];
  [self addGestureRecognizer:recognizer];
}

- (id)initWithFrame:(CGRect)frame {
  //this is called when view is created in code
  if ((self = [super initWithFrame:frame])) {
    [self setUp];
  }
  return self;
}

- (void)pan:(UIPanGestureRecognizer *)recognizer {
  //get the offset by subtracting the pan gesture
  //translation from the current bounds origin
  CGPoint offset = self.bounds.origin;
  offset.x -= [recognizer translationInView:self].x;
  offset.y -= [recognizer translationInView:self].y;

  //scroll the layer
  [(CAScrollLayer *)self.layer scrollToPoint:offset];

  //reset the pan gesture translation
  [recognizer setTranslation:CGPointZero inView:self];
}
```

###CATiledLayer
用于加载尺寸巨大的图片。  
```
//add the tiled layer
CATiledLayer *tileLayer = [CATiledLayer layer];
tileLayer.frame = CGRectMake(0, 0, 2048, 2048);
tileLayer.delegate = self; [self.scrollView.layer addSublayer:tileLayer];

//configure the scroll view
self.scrollView.contentSize = tileLayer.frame.size;

//draw layer
[tileLayer setNeedsDisplay];

- (void)drawLayer:(CATiledLayer *)layer inContext:(CGContextRef)ctx {
  //determine tile coordinate
  CGRect bounds = CGContextGetClipBoundingBox(ctx);
  NSInteger x = floor(bounds.origin.x / layer.tileSize.width);
  NSInteger y = floor(bounds.origin.y / layer.tileSize.height);

  //load tile image
  NSString *imageName = [NSString stringWithFormat: @"Snowman_%02i_%02i, x, y];
  NSString *imagePath = [[NSBundle mainBundle] pathForResource:imageName ofType:@"jpg"];
  UIImage *tileImage = [UIImage imageWithContentsOfFile:imagePath];

  //draw tile
  UIGraphicsPushContext(ctx);
  [tileImage drawInRect:bounds];
  UIGraphicsPopContext();
}
```

###CAEmitterLayer
粒子特效。  
```
//create particle emitter layer
CAEmitterLayer *emitter = [CAEmitterLayer layer];
emitter.frame = self.containerView.bounds;
[self.containerView.layer addSublayer:emitter];

//configure emitter
emitter.renderMode = kCAEmitterLayerAdditive;
emitter.emitterPosition = CGPointMake(emitter.frame.size.width / 2.0,emitter.frame.size.height / 2.0);

//create a particle template
CAEmitterCell *cell = [[CAEmitterCell alloc] init];
cell.contents = (__bridge id)[UIImage imageNamed:@"Spark.png"].CGImage;
cell.birthRate = 150;
cell.lifetime = 5.0;
cell.color = [UIColor colorWithRed:1 green:0.5 blue:0.1 alpha:1.0].CGColor;
cell.alphaSpeed = -0.4;
cell.velocity = 50;
cell.velocityRange = 50;
cell.emissionRange = M_PI * 2.0;

//add particle template to emitter
emitter.emitterCells = @[cell];
```

###AVPlayerLayer
播放视频。  
```
//get video URL
NSURL *URL = [[NSBundle mainBundle] URLForResource:@"Ship" withExtension:@"mp4"];

//create player and player layer
AVPlayer *player = [AVPlayer playerWithURL:URL];
AVPlayerLayer *playerLayer = [AVPlayerLayer playerLayerWithPlayer:player];

//set player layer frame and attach it to our view
playerLayer.frame = self.containerView.bounds;
[self.containerView.layer addSublayer:playerLayer];

//play the video
[player play];
```

##Implicit Animations
隐式动画。改变支持动画的属性，自动生成动画。  
duration -> transaction，默认为0.25秒  
type -> layer action  
transaction只能begin和commit。  
每次runloop会执行一次transaction。  
```
//begin a new transaction
[CATransaction begin];

//set the animation duration to 1 second
[CATransaction setAnimationDuration:1.0];

//add the spin animation on completion
[CATransaction setCompletionBlock:^{
  //rotate the layer 90 degrees
  CGAffineTransform transform = self.colorLayer.affineTransform;
  transform = CGAffineTransformRotate(transform, M_PI_2);
  self.colorLayer.affineTransform = transform;
}];

//randomize the layer background color
CGFloat red = arc4random() / (CGFloat)INT_MAX;
CGFloat green = arc4random() / (CGFloat)INT_MAX;
CGFloat blue = arc4random() / (CGFloat)INT_MAX;
self.colorLayer.backgroundColor = [UIColor colorWithRed:red green:green blue:blue alpha:1.0].CGColor;

//commit the transaction
[CATransaction commit];
}
```
隐式动画流程：  
-actionForLayer:forKey  
actions dictionary  
style dictionary  
-defaultActionForKey:  

UIView默认不开启隐式动画。  
CATransition  

修改layer属性会立即生效，但是并不会立刻显示。  

presentation layer，记录当前layer在哪儿，同步动画和处理点击事件。  
model layer，记录当前layer要去哪儿  
##Explicit Aniamtions
显式动画。  

###Property Animations
只与一个属性相关的动画。  

CABasicAnimation : CAPropertyAnimation : CAAnimation  
fromeValue  
toValue  
byValue  

Animations只作用于presentation，而不作用于model。  
```
- (void)applyBasicAnimation:(CABasicAnimation *)animation toLayer:(CALayer *)layer{
  //set the from value (using presentation layer if available)  
  animation.fromValue = [layer.presentationLayer ?: layer valueForKeyPath:animation.keyPath];

  //update the property in advance
  //note: this approach will only work if toValue != nil
  [CATransaction begin];
  [CATransaction setDisableActions:YES];
  [layer setValue:animation.toValue forKeyPath:animation.keyPath];
  [CATransaction commit];

  //apply animation to layer
  [layer addAnimation:animation forKey:nil];
}
```

CAKeyframeAnimation : CAPropertyAnimation : CAAnimation  
values  
path  
rotationMode = kCAAnimationRotateAuto  

CAAnimationGroup  
animations  
duration  

Transitions  
CATransition : CAAnimation  
type  
subtype  

视图控制器切换时使用，效果更平滑；  

###Cancel animations
`-removeAnimationForKey:  `

##Layer time








##Tuning for speed
合理分配绘图任务至CPU和GPU。

###The Stages of an Animation
Layout：setup layers  
Display：draw backing image，call -drawRect:  
Prepare：prepare send animation data to render server  
Commit：package up and send over IPC  
render server form render tree:  
  calculate intermediate values  
  render to screen  
只有最后一步通过GPU处理。  
开发者只能控制前两步，layout和display。  

影响GPU绘图效率的因素：  
1、过多的layer；  
2、过多的半透明layer；  
3、离屏绘制，如圆角、遮罩、阴影、光栅化；  
4、过大的图片。  

影响CPU绘图效率的因素：  
1、复杂的layout计算；  
2、view的懒加载，从数据库获取数据，从nib中加载view，加载图片；  
3、Core Graphics 绘图；  
4、图片解压缩；  

IO限制因素：  
IO操作耗时严重；  

通过测量发现实际的瓶颈点：  
1、在真机上测试，使用release版本，在低性能机器上测试；  
2、保持稳定的帧率；  
3、instrument：



    Time Profiler：查看CPU时间开销；
        Separate by Thread：按线程将方法分组；
        Hide System Libraries：隐藏系统库；
        Show Obj-C Only：只显示OC方法调用；
    Core Animation：查看Core Animation性能；
        Color Blended Layers：标记出混合的图层，从绿到红表示严重程度，最好没有；
        Color Hits Green and Misses Red：标记出重复缓存的图层；
        Color Copied Images：标记出backing image，最好没有；
        Color Immediately：随时反馈；
        Color Misaligned Images：标记非正确缩放的图片；
        Color Offscreen-Rendered Yellow：标记出需要离屏绘制的layer，通过栅格化优化；
        Color OpenGL Fast Path Blue：标记出OpenGL绘图；
        Flash Updated Regions：标记出重绘的layer，最好没有；
    OpenGL ES Driver：查看GPU性能；
        Tiler Utilization
        Renderer Utilization
    组合使用效果更佳。

优化方法：  
cache offscreen render layer：



    layer.shouldRasterize = YES;
    layer.rasterizationScale = [UIScreen mainScreen].scale;

##Efficient Drawing

###Software Drawing
速度慢并且需要大量内存空间。  
少写drawRect方法。  


###Vector Graphics
多边形；  
对角线和曲线；  
文字；  
渐变。  
使用CAShapeLayer  

###Dirty Rectangles
需要重绘的区域  
调用-setNeedsDisplayInRect:方法指定重绘区域。  

###Asynchronous Drawing
在子线程绘图，将结果直接作为layer的contents。  
CATiledLayer。  
drawsAsynchronously属性。  

##Image IO
~~~
//  通过tag防止重复创建
UIImageView *imageView = (UIImageView *)[cell viewWithTag:imageTag];
if (!imageView){
  imageView = [UIImageView alloc] init];
  [cell.contentView addSubview:imageView];
}
~~~
###Loading and Latency
button的响应时间要保持在0.2秒以下。  

###Threaded Loading
gcd  



~~~
//  标记cell
cell.tag = indexPath.row;
imageView.image = nil;
//  异步加载图片
dispatch_aysnc(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_LOW),0),^{
  //  加载图片
  NSInteger index = indexPath.row;
  NSString *imagePath = self.imagePaths[index];
  UIImageView *image = [UIImage imageWithContentsOfFile:imagePath];

  //  在主线程显示图片
  dispatch_async(dispatch_get_main_queue(),^{
    if (index == cell.tag){
      imageView.image = image;
    }
  });
});
~~~

###Deferred Decompression
PNG图片大，但解压快；  
JPEG图片小，但解压慢。  
图片通常在需要绘制前才开始解压。  
[UIImage imageWithName:imageName] 该方法载入图片后即开始解压。  
layer的contents或UIImageView的image。  
ImageIO.framework  



~~~
NSInteger index = indexPath.row;
NSURL *imageURL = [NSURL fileURLWithPath:self.imagePaths[index]];
NSDictionary *options = @{(__bridge id)kCGImageSourceShouldCache:@YES};
CGImageSourceRef source = CGImageSourceCreateWithURL((__bridge CFURLRef)imageURL,NULL);
CGImageRef imageRef = CGImageSourceCreateImageAtIndex(source,0,(__bridge CFDictionaryRef)options);
UIImage *image = [UIImage imageWithCGImage:imageRef];
CGImageRelease(imageRef);
CFRelease(source);
~~~

NSCache  

##Layer Performance
