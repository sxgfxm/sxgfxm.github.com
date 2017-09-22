---
layout: post
title: "iOS知识小集-170612"
date: 2017-06-16 14:08:12 +0800
comments: true
categories: iOS
keywords: sxgfxm, MBProgressHUD,NSNetServiceBrowser,Zip,gdrive,AFHTTPSessionManager
description: sxgfxm, MBProgressHUD,NSNetServiceBrowser,Zip,gdrive,AFHTTPSessionManager
---

## MBProgressHUD需要在主线程调用
UI在主线程操作。

<!-- more -->
## 引入库
`#import <>`: global include  
` #import ""`: local include  
`#import`: only include once  
`#include`: may recursive include  
`@import`: done automatically  

## Edit Scheme
定义不同的宏

## NSAssert
多用断言

## 打包和解包

打包：头 + 长度 + 内容。  

```objective-c
- (NSData *)packageData:(NSData *)data {
  NSAssert(data && data.length > 0, @"Data cannot be empty");

  NSMutableData *payload = [NSMutableData data];
  int length = (int)data.length;
  [payload appendBytes:kMagicHeader length:4];
  [payload appendBytes:&length length:4];
  [payload appendData:data];

  return payload;
}
```

解包：取头 + 取长度 + 取内容。  

```objective-c
- (void)parseData:(NSData *)data {
  @synchronized(self) {
    [self.writeBuffer appendData:data];
    while (self.writeBuffer.length - self.writeBufferOffset > 8) {
      // Parse header
      char header[4];
      [self.writeBuffer getBytes:header range:NSMakeRange(self.writeBufferOffset, 4)];
      if (header[0] != kMagicHeader[0] || header[1] != kMagicHeader[1] || header[2] != kMagicHeader[2] ||
          header[3] != kMagicHeader[3]) {
        NSLog(@"Invalid magic header");
        self.writeBufferOffset += 1;
        continue;
      }

      // Parse length
      int length;
      [self.writeBuffer getBytes:&length range:NSMakeRange(self.writeBufferOffset + 4, 4)];

      // Parse payload
      if (self.writeBuffer.length - self.writeBufferOffset - 8 < length) {
        break;
      }
      NSData *payload = [NSData dataWithBytes:[self.writeBuffer bytes] + self.writeBufferOffset + 8 length:length];
      if (self.delegate) {
        [self.delegate onIncomingData:payload];
      }
      self.writeBufferOffset = self.writeBufferOffset + 8 + length;
      if (self.writeBufferOffset >= self.writeBuffer.length) {
        self.writeBuffer = [[NSMutableData alloc] init];
        self.writeBufferOffset = 0;
      }
    }
  }
}
```

## NSNetServiceBrowser
扫描service。  

```objective-c
-(void)start{
  NSNetServiceBrowser *browser = [[NSNetServiceBrowser alloc] init];
  browser.delegate = self;
  [browser searchForServicesOfType:@"_http._tcp." inDomain:@""];
}

- (void)netServiceBrowser:(NSNetServiceBrowser *)aNetServiceBrowser
           didFindService:(NSNetService *)aNetService
               moreComing:(BOOL)moreComing {
  if ([self.serviceTypes containsObject:aNetService.name]) {
    NSLog(@"Found a service: %@", aNetService);
    aNetService.delegate = self;
    [aNetService resolveWithTimeout:5];
  }
}

- (void)netServiceBrowser:(NSNetServiceBrowser *)aNetServiceBrowser
         didRemoveService:(NSNetService *)aNetService
               moreComing:(BOOL)moreComing {
}

- (void)netServiceDidResolveAddress:(NSNetService *)netService {
  if ([self.serviceTypes containsObject:netService.name]) {
    [self.delegate serviceAdded:netService];
  }
  NSURL *serviceURL = [NSURL URLWithString:[NSString stringWithFormat:@"http://%@:%li", netService.hostName, (long)netService.port]];
  NSLog(@"Resolved address for service %@: %@", netService, serviceURL);
}

- (void)netService:(NSNetService *)sender didNotResolve:(NSDictionary *)errorDict {
  NSLog(@"Couldn't resolve address for service %@: %@", sender, errorDict);
}
```

## Compress files and folder
**Zip**  

```c
zip -r -X archive_name.zip folder_to_compress
unzip archive_name.zip
```

## Upload a file to Google Drive from the command line
Install [gdrive](https://github.com/prasmussen/gdrive), a command line utility for interacting with Google Drive.    

```c
brew install gdrive
```

```c
gdrive list
gdrive download file_name
gdrive upload file_name
```

## Open a certain folder in Atom
```objective-c
cd folder_path
atom .
```

## AFHTTPSessionManager
```objective-c
AFHTTPSessionManager *manager = [AFHTTPSessionManager manager];
  manager.responseSerializer = [AFHTTPResponseSerializer serializer];
  [manager GET:kXGFeedbackURL
      parameters:nil
      success:^(NSURLSessionDataTask *_Nonnull task, id _Nonnull responseObject) {
        NSError *error = nil;
        self.dataSource = [[XGFeedbackResponse alloc] initWithData:responseObject error:&error];
        if (error) {
          DDLogDebug(@"XG - Feedback response error %@", error.localizedDescription);
        }
        DDLogDebug(@"XG - Feedback response %@", self.dataSource);
      }
      failure:^(NSURLSessionDataTask *_Nullable task, NSError *_Nonnull error) {
        DDLogDebug(@"XG - Feedback fail %@", error.localizedDescription);
      }];
```

## JSONModel
```objective-c
#import <JSONModel/JSONModel.h>

@protocol XGFeedbackItem;
@protocol XGFeedbackCategory;

@interface XGFeedbackItem : JSONModel

@property(nonatomic, strong) NSString *cn;
@property(nonatomic, strong) NSString *en;
@property(nonatomic, strong) NSString *tw;

@end

@interface XGFeedbackCategory : JSONModel

@property(nonatomic, strong) XGFeedbackItem *title;
@property(nonatomic, strong) NSArray<XGFeedbackItem *><XGFeedbackItem> *detail;
@property(nonatomic, assign) BOOL multiple;

@end

@interface XGFeedbackResponse : JSONModel

@property(nonatomic, strong) NSString *product;
@property(nonatomic, strong) NSArray<XGFeedbackCategory *><XGFeedbackCategory> *content;

@end
```

## 获取系统语言
```objective-c
NSString *language = [[NSLocale preferredLanguages] firstObject];
  if ([language hasPrefix:@"zh-Hans"]) {
    return self.cn;
  } else if ([language hasPrefix:@"zh-Hant"]) {
    return self.tw;
  } else {
    return self.en;
  }
```

