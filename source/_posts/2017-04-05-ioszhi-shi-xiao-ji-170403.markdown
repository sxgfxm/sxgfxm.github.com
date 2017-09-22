---
layout: post
title: "iOS知识小集-170327"
date: 2017-04-05 09:07:16 +0800
comments: true
categories: iOS
keywords: sxgfxm, NSURL, NSString, ID3v1, Metadata, MPMediaItem, GCDWebServer
description: sxgfxm, NSURL, NSString, ID3v1, Metadata, MPMediaItem, GCDWebServer
---

## 转换
NSURL -> NSString：`NSString *path = [url path];`  
NSString -> NSURL：`NSURL *url = [NSURL fileURLWithPath:path]; `   
UIImage -> NSData：`NSData *data = UIImagePNGRepresentation(image);`  
NSData -> UIImage：`UIImage *image = [UIImage imageWithData:data];`    

<!-- more -->

## 向歌曲中写入metadata信息
导入第三方库[id3v2lib](https://github.com/larsbs/id3v2lib)。   

```c
  //  文件路径
  const char *cPath = [[url path] cStringUsingEncoding:NSUTF8StringEncoding];
  //  创建tag
  ID3v2_tag *tag = load_tag(cPath);
  if (tag == NULL) {
    tag = new_tag();
  }
  //  设置tag
  char *title = (char *)[item.title cStringUsingEncoding:NSUTF8StringEncoding];
  tag_set_title(title, 0, tag);
  char *artist = (char *)[item.artist cStringUsingEncoding:NSUTF8StringEncoding];
  tag_set_artist(artist, 0, tag);
  UIImage *image = [item.artwork imageWithSize:CGSizeMake(400, 400)];
  NSData *data = UIImagePNGRepresentation(image);
  NSString *coverPath = [[NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
      stringByAppendingPathComponent:[NSString stringWithFormat:@"%@.png", item.title]];
  [[NSFileManager defaultManager] removeItemAtPath:coverPath error:nil];
  [[NSFileManager defaultManager] createFileAtPath:coverPath contents:data attributes:nil];
  tag_set_album_cover([coverPath cStringUsingEncoding:NSUTF8StringEncoding], tag);
  [[NSFileManager defaultManager] removeItemAtPath:coverPath error:nil];
  //  写入tag
  set_tag(cPath, tag);
```

## MPMediaItem
isCloudItem：是否为云端文件；  
hasProtectedAsset：是否含有被保护文件；  
以上两个原因可能导致assertURL为nil。  

## NSOperation内存泄露
operation.completionBlock会引起循环引用。  
在block中创建对象注意生命周期。  

## GCDWebServer监听传输状态
继承`GCDWebServerConnection`，重写`- (void)didWriteBytes:(const void *)bytes length:(NSUInteger)length`方法，以通知的形式传递值。  


## c string in OC
`char *cString = (char *)[string cStringUsingEncoding:NSUTF8StringEncoding]`，
无需手动释放，当receiver被释放或者内存不足时，会自动释放cString。

## WiFi ap隔离

## 观察者模式

## 工厂模式
