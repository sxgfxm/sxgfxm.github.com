---
layout: post
title: "Export Music from Itunes to Local App's Sandbox on iPhone"
date: 2017-02-10 15:31:20 +0800
comments: true
categories: iOS
keywords: sxgfxm, MPMediaQuery, MPMediaItem,AVAssetExportSession
description: Export Music from Itunes to Local App's Sandbox on iPhone
---

## Introduction

本文主要介绍app如何获取iTunes中音乐列表，并把iTunes中音乐文件导出至app的沙盒目录下。

<!--more-->

##import

导入所需的头文件。



~~~c
#import <AVFoundation/AVAssetExportSession.h>
#import <MediaPlayer/MediaPlayer.h>
~~~

##注册通知

注册**MPMediaLibraryDidChangeNotification**通知，当iTunes音乐库文件发生变化时，做出响应。



~~~c
[[NSNotificationCenter defaultCenter]
    addObserver:self
    selector:@selector(mediaLibraryDidChange:)
    name:MPMediaLibraryDidChangeNotification
    object:nil];
~~~

##开启通知

开启**MPMediaLibrary**通知。



~~~c
[[MPMediaLibrary defaultMediaLibrary] beginGeneratingLibraryChangeNotifications];
~~~

##关闭通知

关闭**MPMediaLibrary**通知。



~~~c
[[MPMediaLibrary defaultMediaLibrary] endGeneratingLibraryChangeNotifications];
~~~

##MPMediaQuery

通过**MPMediaQuery**获取iTunes中音乐列表，可以自定义列表类型，调用`-collection`方法，返回对应列表的数组。数组中元素为**MPMediaItemCollection**类型。



~~~c
//  create
MPMediaQuery *mediaQuery = [MPMediaQuery playlistQuery];
//  groupType
mediaQuery.groupingType = MPMediaGroupingAlbumArtist;
//  query
NSArray<MPMediaItemCollection *> *mediaCollections = [mediaQuery collections];
~~~

##MPMediaItem

通过**MPMediaItemCollection**获取对应的**MPMediaItem**，对应多媒体文件。通过**MPMediaItem**获取文件相关信息。导出时需要文件地址**assetURL**。



~~~c
NSMutableArray<MPMediaItem *> mediaItems = [NSMutableArray array];
for (MPMediaItemCollection *collection in mediaCollections) {
  //  mediaItem
  MPMediaItem *mediaItem = [collection representativeItem];
  //  歌曲名称
  NSString *title = [mediaItem valueForProperty:MPMediaItemPropertyTitle];
  //  演唱者
  NSString *artist = [mediaItem valueForProperty:MPMediaItemPropertyArtist];
  //  歌曲封面
  MPMediaItemArtwork *artwork = [mediaItem valueForProperty:MPMediaItemPropertyArtwork];
  //  歌曲格式
  NSString *form = self.assetURL.pathExtension;
  //  歌曲地址，本地iTunes中地址，可用于导出歌曲
  NSURL *assetURL = [mediaItem valueForProperty:MPMediaItemPropertyAssetURL];
  //  add
  [mediaItems addObject:mediaItem];
}
~~~

##Play

获取到**MPMediaItemCollection**后，可以选择使用iTunes直接播放该文件。



~~~c
MPMediaItemCollection *mediaItemCollection = [mediaCollections firstObject];
MPMediaItem *selectedItem = [collection representativeItem];
[[MPMusicPlayerController iPodMusicPlayer] setQueueWithItemCollection:mediaItemCollection];
[[MPMusicPlayerController iPodMusicPlayer] setNowPlayingItem:selectedItem];
[[MPMusicPlayerController iPodMusicPlayer] play];
~~~

##Export Music to Local Sandbox

导出音乐文件至本地沙盒目录下：

1、获取音乐文件名（title）及input地址（**assetURL**）；

2、获取沙盒目录，并创建output地址（**outputURL**）；

3、获取**AVURLAsset**；

4、使用**AVAssetExportSession**导出AVURLAsset。



~~~c
for (MPMediaItem *musicItem in musicItems) {
    //  获取文件名及地址
    NSString *title = [mediaItem valueForProperty:MPMediaItemPropertyTitle];
    NSURL *assetURL = [mediaItem valueForProperty:MPMediaItemPropertyAssetURL];
    if (assetURL && [self validIpodLibraryURL:assetURL]) {
      //  创建 output URL，存入沙盒目录下，以文件名为标识
      NSString *pathExtension = assetURL.pathExtension;
      NSArray *paths = NSSearchPathForDirectoriesInDomains(
          NSDocumentDirectory, NSUserDomainMask, YES);
      NSString *documentsDirectory = [paths firstObject];
      NSURL *outputURL =
          [[NSURL fileURLWithPath:[documentsDirectory
                                      stringByAppendingPathComponent:title]]
              URLByAppendingPathExtension:pathExtension];
      //  保证无重复路径
      [[NSFileManager defaultManager] removeItemAtURL:outputURL error:nil];
      //  获取Asset
      NSDictionary *options = [[NSDictionary alloc] init];
      AVURLAsset *asset = [AVURLAsset URLAssetWithURL:assetURL options:options];
      if (asset) {
        //  创建export session
        AVAssetExportSession *exportSession = [[AVAssetExportSession alloc]
            initWithAsset:asset
               presetName:AVAssetExportPresetPassthrough];
        if (exportSession) {
            //  导出类型
            if ([pathExtension compare:@"m4a"] == NSOrderedSame) {
              exportSession.outputFileType = AVFileTypeAppleM4A;
            } else if ([pathExtension compare:@"wav"] == NSOrderedSame) {
              exportSession.outputFileType = AVFileTypeWAVE;
            } else if ([pathExtension compare:@"aif"] == NSOrderedSame) {
              exportSession.outputFileType = AVFileTypeAIFF;
            } else if ([pathExtension compare:@"m4v"] == NSOrderedSame) {
              exportSession.outputFileType = AVFileTypeAppleM4V;
            }
            //  导出地址
            exportSession.outputURL = outputURL;
            //  导出
            [exportSession exportAsynchronouslyWithCompletionHandler:^{
              //  状态回调
              if (completion) {
                switch (exportSession.status) {
                case AVAssetExportSessionStatusFailed:
                  NSLog(@"Failed");
                  break;
                case AVAssetExportSessionStatusCancelled:
                  NSLog(@"Cancelled");
                  break;
                case AVAssetExportSessionStatusCompleted:
                  NSLog(@"Completed");
                  break;
                default:
                  break;
                }
              }
            }];
        }
      }
    }
  }
~~~
~~~c
- (BOOL)validIpodLibraryURL:(NSURL *)url {
  NSString *IPOD_SCHEME = @"ipod-library";
  if (nil == url)
    return NO;
  if (nil == url.scheme)
    return NO;
  if ([url.scheme compare:IPOD_SCHEME] != NSOrderedSame)
    return NO;
  if ([url.pathExtension compare:@"aif"] != NSOrderedSame &&
      [url.pathExtension compare:@"m4a"] != NSOrderedSame &&
      [url.pathExtension compare:@"wav"] != NSOrderedSame &&
      [url.pathExtension compare:@"m4v"] != NSOrderedSame) {
    return NO;
  }
  return YES;
}
~~~
