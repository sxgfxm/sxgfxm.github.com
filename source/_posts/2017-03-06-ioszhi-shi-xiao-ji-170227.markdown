---
layout: post
title: "iOS知识小集-170227"
date: 2017-03-06 16:15:46 +0800
comments: true
categories: iOS, lock, circular image, RxJava, socket
keywords: sxgfxm
description: iOS, lock, circular image, RxJava, socket
---

## OC中的锁
~~~
@implementation TestObj

-(void)method1{
  NSLog(@"Method1");
}
-(void)method2{
  NSLog(@"Method2");
}

@end
~~~
<!--more-->

### NSLock    

~~~
NSLock *lock = [[NSLock alloc] init];
//线程1
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    [lock lock];
    [obj method1];
    sleep(10);
    [lock unlock];
});
//线程2
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    sleep(1);//以保证让线程2的代码后执行
    [lock lock];
    [obj method2];
    [lock unlock];
});
~~~
### synchronized关键字。  

~~~
//主线程中
TestObj *obj = [[TestObj alloc] init];
//线程1
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    @synchronized(obj){
        [obj method1];
        sleep(10);
    }
});
//线程2
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    sleep(1);
    @synchronized(obj){
        [obj method2];
    }
});
~~~
### phread_mutex_t。  

~~~
//主线程中
TestObj *obj = [[TestObj alloc] init];
__block pthread_mutex_t mutex;
pthread_mutex_init(&mutex, NULL);
//线程1
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    pthread_mutex_lock(&mutex);
    [obj method1];
    sleep(5);
    pthread_mutex_unlock(&mutex);
});
//线程2
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    sleep(1);
    pthread_mutex_lock(&mutex);
    [obj method2];
    pthread_mutex_unlock(&mutex);
});
~~~
### GCD。  

~~~
//主线程中
TestObj *obj = [[TestObj alloc] init];
dispatch_semaphore_t semaphore = dispatch_semaphore_create(1);
//线程1
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    dispatch_semaphore_wait(semaphore, DISPATCH_TIME_FOREVER);
    [obj method1];
    sleep(10);
    dispatch_semaphore_signal(semaphore);
});
//线程2
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    sleep(1);
    dispatch_semaphore_wait(semaphore, DISPATCH_TIME_FOREVER);
    [obj method2];
    dispatch_semaphore_signal(semaphore);
});
~~~

## 生成圆形透明背景图片
~~~
+ (UIImage *)circularScaleAndCropImage:(UIImage *)image {
  // Create the bitmap graphics context
  UIGraphicsBeginImageContextWithOptions(CGSizeMake(image.size.width, image.size.height), NO, 0.0);
  CGContextRef context = UIGraphicsGetCurrentContext();

  // Get the width and heights
  CGFloat imageWidth = image.size.width;
  CGFloat imageHeight = image.size.height;

  // Calculate the centre of the circle
  CGFloat imageCentreX = imageWidth / 2;
  CGFloat imageCentreY = imageHeight / 2;

  // Create and CLIP to a CIRCULAR Path
  CGFloat radius = imageWidth / 2;
  CGContextBeginPath(context);
  CGContextAddArc(context, imageCentreX, imageCentreY, radius, 0, 2 * M_PI, 0);
  CGContextClosePath(context);
  CGContextClip(context);

  // Draw the IMAGE
  CGRect myRect = CGRectMake(0, 0, imageWidth, imageHeight);
  [image drawInRect:myRect];

  UIImage *newImage = UIGraphicsGetImageFromCurrentImageContext();
  UIGraphicsEndImageContext();

  return newImage;
}
~~~

## RxJava
响应式函数编程  
订阅的思想  
多层异步回调逻辑更清晰  

## Socket
**Packet-based communication**  
**Stream-based clients ** 
