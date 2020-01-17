---
layout: post
title: "iOS知识小集-201909"
date: 2020-01-17 11:49:59 +0800
comments: true
categories: iOS
keywords: sxgfxm
description: 适配iOS 13, Gaia
---

## 2019.09.03
### 适配 iOS 13
1、需要适配 dark mode。现强制设为非 dark mode；  



```
if (@available(iOS 13.0, *)) {
    self.window.overrideUserInterfaceStyle = UIUserInterfaceStyleLight;
}
```


2、present 默认状态变为 卡片弹出，可下滑退出，可设置 `self.modalPresentationStyle = UIModalPresentationFullScreen; `   
3、device token 解析方法改变：    



```
#include <arpa/inet.h>
- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
{
    if (![deviceToken isKindOfClass:[NSData class]]) return;
    const unsigned *tokenBytes = [deviceToken bytes];
    NSString *hexToken = [NSString stringWithFormat:@"%08x%08x%08x%08x%08x%08x%08x%08x",
                          ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
                          ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
                          ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
    NSLog(@"deviceToken:%@",hexToken);
}
```


4、导航栏 label 需要设置frame，否则颜色会异常；  
5、label add layer 会遮盖文字；  
6、获取 keyWindow；  
7、`Privacy - Bluetooth Always Usage Description`：增加该权限配置；  
8、UITextfield placeholder 颜色；  
9、NSData to HexString    



```
@implementation NSData (HexRepresentation)

- (NSString *)hexString {
    const unsigned char *bytes = (const unsigned char *)self.bytes;
    NSMutableString *hex = [NSMutableString new];
    for (NSInteger i = 0; i < self.length; i++) {
        [hex appendFormat:@"%02x", bytes[i]];
    }
    return [hex copy];
}

@end
```

## 2019.09.04
Wireless debug   



```
@startuml
WWLoginController -> WWThirdPartyLoginManager : press apple sign in button
WWThirdPartyLoginManager --> WWLoginController : apple sigin and send (userId, accessToken) back
WWLoginController -> WWThirdPartyAccountManager : send (loginType, userId, accessToken) to third party account manager
WWThirdPartyAccountManager --> WWLoginController : send (loginType, userId, accessToken) to server and send login status back
WWLoginController -> WWAccountRelatedManager : login succeeded, save (loginType, userId, responseObject)
WWLoginController -> WWRegisterInputNumberController : login failed, register phone number
@enduml
```

## 2019.09.05

### Apple Sign In
**操作未完成**：需要删除APP重新安装才生效；


## 2019.09.06

### Unit Test
添加 Unit Test：创建工程时添加；添加新的 Target，选择 Test；  
添加 测试文件：新建文件时选择测试文件；  
can't find import files：运行一次就好了；  

**如何测试代理**  
**如何测试block**  

## 2019.09.09
**Failed to connect to github.com port 443: Operation timed out**：`https://www.githubstatus.com/`

## 2019.09.11
**生产者 - 消费者问题**：cache 缓存数据，queue 读取数据，当cache为空时，queue应该等待，不能结束。
mark down mathjax：写数学公式。  
[音频流播放](https://stackoverflow.com/questions/30794479/the-sound-muted-after-playing-audio-with-audio-queue-on-ios-for-a-while?rq=1)  


## 2019.09.16
**TTS中断问题**：先缓存0.5秒数据再开始播放。  
**AVAudioSessionErrorCodeCannotStartRecording（561145187）**：无法后台语音识别。  
**AVAudioSessionErrorCodeCannotStartPlaying（561015905）**：无法后台语音识别。  

## 2019.09.17
**Error: Multiple commands produce**：切换为 legacy build system。  
**(Enterprise) Sign In with Apple**：去掉企业版capability中 sign in with apple 选项。  



```
@startwbs
* OTA
** BLE Connection
*** CSRConnectionManager \n -connectPeripheral
*** CSRConnectionManager \n -addDelegate:
*** CSRConnectionManager \n -stopScan
** Gaia Connection(-discoveredPripheralDetails)
*** CSRGaia \n -connectPeripheral:
*** CSRGaiaManager \n -setDataEndPointMode:
** BLE Disconnection
*** CSRConnectionManager \n -removeDelegate: \n disconnectPeripheral
@end
```

**Error Domain=NSOSStatusErrorDomain Code=1954115647 "(null)"**：音频数据不完整导致初始化音频播放器出错。  

## 2019.09.18
Gaia VA

### 封装、解析 Gaia 数据包  Done
`CSRGaiaGattCommand`  
  `- initWithLength:`  
  `- initWithNSData: ` 

封装 command + vendor_id + data：    



```
- (NSData *)gaiaPacketWithCommand:(GaiaCommandType)command
                           vendor:(uint16_t)vendor_id
                             data:(NSData *)params{
  CSRGaiaGattCommand *cmd = [[CSRGaiaGattCommand alloc] initWithLength:GAIA_GATT_HEADER_SIZE];        
  if (cmd) {
    [cmd setCommandId:command];
    [cmd setVendorId:vendor_id];

    if (params) {
      [cmd addPayload:params];
    }

    NSData *packet = [cmd getPacket];

    DLog(@"Outgoing packet: %@", packet);
    return packet;
  }
  return nil;
}
```



解析  



```
- (CSRGaiaGattCommand *)gaiaGattCommandWithGaiaPacket:(NSData *)packet{
  return [[CSRGaiaGattCommand alloc] initWithNSData: packet];
}

- (NSData *)payloadWithGaiaGattCommand:(CSRGaiaGattCommand *)command{
  //  command type
  GaiaCommandType cmdType = [command getCommandId];
  //  payload
  NSData *payload = [command getPayload];
  return payload;
}

```

### 接收 Gaia 数据包  Done
`CSRGaiaManager`
  `CSRUpdateManagerDelegate`
    `- didReceiveGaiaGattResponse:`  
目前在`WWTicPodBTManager`中有监听。  

### 发送 Gaia 数据包  Todo
`CSRGaiaManager`
  `- getBattery`
    `CSRGaia`
      `- getBattery`
        `- sendCommand:vendor:data:`  



```
- (void)getBattery {    
    [self sendCommand:GaiaCommand_GetCurrentBatteryLevel
               vendor:CSR_GAIA_VENDOR_ID
                 data:nil];
}

- (void)setVolume:(NSInteger)value {
    NSMutableData *payload = [[NSMutableData alloc] init];
    uint8_t payload_event = (uint8_t)value;

    [payload appendBytes:&payload_event length:1];
    [self sendCommand:GaiaCommand_Volume
               vendor:CSR_GAIA_VENDOR_ID
                 data:payload];
}
```


Todo：  
1、在 `CSRGaiaManager` 中添加 VA 相关命令；  
2、在 `CSRGaia` 中添加 VA 相关命令；  

### VA 状态管理  Todo
消息确认，发送、接收、解析等。

## 2019.09.19
测定 BLE 传输 速率；

## 2019.09.23
运行时间  



```
CFAbsoluteTime startTime =CFAbsoluteTimeGetCurrent();

//在这写入要计算时间的代码

CFAbsoluteTime linkTime = (CFAbsoluteTimeGetCurrent() - startTime);

NSLog(@"Linked in %f ms", linkTime *1000.0);
```



音频解码是连续的，解码过程中初始化条件不能被重置。

## 2019.09.24

Voice Assistant State  



```
@startuml
[*] --> VA.CheckVersion

VA.CheckVersion --> VA.Idle : GaiaCheckVersion

VA.Idle --> VA.VoiceRequesting : GaiaStart
VA.Idle -left-> VA.MobvoiPaused : GaiaMobvoiPaused
VA.Idle -right-> VA.Idle : GaiaMobvoiFeatureEvent

VA.MobvoiPaused --> VA.Idle : GaiaMobvoiResumed

state VA.Idle {
  [*] --> VA.Session.Normal
  [*] --> VA.Session.PushToTalk
  [*] --> VA.Session.Secretary
  [*] --> VA.Session.Joke

  VA.Session.Normal : normal voice query, auto finish
  VA.Session.PushToTalk : longpress voice query, ticpods finish
  VA.Session.Secretary : voice query with special params
  VA.Session.Joke : text query joke
}

VA.VoiceRequesting --> VA.VoiceRequested : GaiaVoiceData

VA.VoiceRequested --> VA.VoiceRequested : GaiaVoiceDataRequest ack
VA.VoiceRequested --> VA.VoiceRequested : GaiaVoiceData
VA.VoiceRequested --> VA.Idle : GaiaVoiceEnd
VA.VoiceRequested --> VA.Idle : GaiaCancel
VA.VoiceRequested --> VA.HostCancelOrEnd : GaiaMobvoiHostVoiceEnd
VA.VoiceRequested --> VA.HostCancelOrEnd : GaiaMobvoiHostCancel

VA.HostCancelOrEnd --> VA.Idle : GaiaVoiceEnd ack
VA.HostCancelOrEnd --> VA.Idle : GaiaCancel ack
VA.HostCancelOrEnd --> VA.Idle : GaiaStart

VA.CheckVersion : GaiaCheckVersion:
VA.CheckVersion : 1. ack GaiaCheckVersion
VA.CheckVersion : 2. check protocol version
VA.CheckVersion : 3. get G722 decoder parameters
VA.CheckVersion : 4. initialize G722 decoder with parameters

VA.Idle : GaiaStart:
VA.Idle : 1. ack GaiaStart
VA.Idle : 2. get session type\n

VA.Idle : GaiaMobvoiPaused:
VA.Idle : 1. no GaiaMobvoiPaused ack
VA.Idle : 2. no GaiaMobvoiFeatureEvent ack\n

VA.Idle : GaiaMobvoiFeatureEvent:
VA.Idle : 1. no GaiaMobvoiFeatureEvent ack
VA.Idle : 2. do special action

VA.MobvoiPaused : GaiaMobvoiResumed:
VA.MobvoiPaused : 1. no GaiaMobvoiResumed ack
VA.MobvoiPaused : 2. ignore voice assistant command

VA.VoiceRequesting : GaiaVoiceData:
VA.VoiceRequesting : 1. no GaiaVoiceData ack
VA.VoiceRequesting : 2. send GaiaVoiceDataRequest

VA.VoiceRequested : GaiaVoiceDataRequest ack:
VA.VoiceRequested : 1. do nothing\n

VA.VoiceRequested : GaiaData:
VA.VoiceRequested : 1. no GaiaData ack
VA.VoiceRequested : 2. decode G722 packet
VA.VoiceRequested : 3. send decoded voice data to speech sdk\n

VA.VoiceRequested : GaiaVoiceEnd:
VA.VoiceRequested : 1. ack GaiaVoiceEnd
VA.VoiceRequested : 2. finish recognition\n

VA.VoiceRequested : GaiaCancel:
VA.VoiceRequested : 1. ack GaiaCancel
VA.VoiceRequested : 2. cancel recognition\n

VA.VoiceRequested : GaiaMobvoiHostVoiceEnd:
VA.VoiceRequested : 1. send GaiaVoiceEnd
VA.VoiceRequested : 2. finish recognition\n

VA.VoiceRequested : GaiaMobvoiHostCancel:
VA.VoiceRequested : 1. send GaiaCancel
VA.VoiceRequested : 2. cancel recognition

VA.HostCancelOrEnd : GaiaVoiceEnd ack:
VA.HostCancelOrEnd : 1. do nothing\n

VA.HostCancelOrEnd : GaiaCancel ack:
VA.HostCancelOrEnd : 1. do nothing\n

VA.HostCancelOrEnd : GaiaStart:
VA.HostCancelOrEnd : 1. VA.Idle -> VA.VoiceRequesting
@enduml
```