---
layout: post
title: "iOS知识小集-201906"
date: 2020-01-17 11:36:46 +0800
comments: true
categories: iOS
keywords: sxgfxm,AVAudioSession
description: AVAudioSession
---

## 2019.06.06

### 音频基础
**音频文件**：即声音的数字信号。由原声音信息采样、量化和编码产生。  
**奈奎塞特理论**：采样频率高于声音最高频率的两倍时，才能将数字信号还原为原来的声音。一般为40~50kHZ。  
**脉冲编码调制**：对声音进行采样、量化的过程，简称 **PCM**。PCM数据为原始音频数据，完全无损，体积庞大。  
**音频格式**：无损压缩（ALAC、APE、FLAC），有损压缩（MP3、AAC、OGG、WMA）。  
**MP3码率**：代表压缩质量，码率越高、质量越好。固定码率（CBR），可变码率（VBR）。  
**MP3数据结构**：ID3 + 音频数据。音频数据由帧（帧头4B + 附加信息32B + 声音数据）构成。  

### iOS音频播放
只播放音频文件：AVFoudation。  
播放并存储音频文件：AudioFileStreamer + AudioQueue。边下载音频数据边用NSFileHandler读取本地音频文件给AudioFileStreamer解析分离音频帧，之后给AudioQueue进行解码和播放。  
专业音乐播放器：使用AudioConverter把音频数据转换为PCM数据，再由AudioUnit + AUGraph进行音效处理和播放。  

### AudioSession
#### AudioSession作用：  

1、定义APP如何使用音频（是录音还是播放）；  
2、选择音频输入输出设备（手机麦克风、手机扬声器、耳机麦克风、耳机扬声器）；  
3、协调其他APP音频和系统音频（电话打断，是否混响）；  

#### AudioSession使用：  

1、设置category；  
2、设置options；  
3、setActive；  

### AudioFileStream
#### AudioFileStream作用：

1、读取采样率、码率、时长等基本信息；  
2、分离音频帧；  

### AudioQueue
#### AudioQueue作用：

1、播放PCM数据、MP3数据；  
2、录音；  

#### AudioQueue使用：

1、创建AudioQueue；  
2、创建AudioQueueBufferRef；  
3、将数据写入AudioQueueBufferRef，然后插入到AudioQueue中；  
4、调用AudioQueueStart开始播放；  
5、在回调中将使用过的AudioQueueBufferRef放回重复使用；  

## 20919.06.11

#### AVAudioSessionCategoryOptionAllowBluetooth
采用 HFP 协议将 **蓝牙设备作为音频输入设备，且同时自动变为输出设备**。通常使用蓝牙设备录音时使用，此协议下蓝牙设备既可录音又可播放音频，但 **音质较差**。  
只在 category 为 AVAudioSessionCategoryPlayAndRecord、AVAudioSessionCategoryRecord 时使用。  

### AVAudioSessionCategoryOptionAllowBluetoothA2DP
采用 A2DP 协议将 **蓝牙设备作为音频输出设备**。通常用来作为高质量音频蓝牙输出，比如播放音乐，此协议下蓝牙设备 **无法作为音频输入设备**，音频输入需要使用手机麦克风。  
category 为 AVAudioSessionCategoryAmbient、AVAudioSessionCategorySoloAmbient、AVAudioSessionCategoryPlayback 时，默认采用 A2DP。  
category 为 AVAudioSessionCategoryPlayAndRecord 时，如果需要使用 A2DP，需显示设置。  
category 为 AVAudioSessionCategoryMultiRoute、AVAudioSessionCategoryRecord 时，A2DP 默认无效。  
AVAudioSessionCategoryOptionAllowBluetooth 和 AVAudioSessionCategoryOptionAllowBluetoothA2DP 同时设置时，优先采用前者。  
使用原则：  
1、播放音频时，为了保证音频输出质量，采用 AVAudioSessionCategoryOptionAllowBluetoothA2DP；  
2、既需要录音又需要播放音频时，需采用 AVAudioSessionCategoryOptionAllowBluetooth；  

## 2019.06.12
后台开启AVAudioSession需要设置为MixWithOthers，否则无法激活。

## 2019.06.13
Could not find a `ios` simulator。  
`gem env` 找到ruby路径查看 `fourflusher` 中 的 `find.rb` 文件。

## 2019.06.14
打包机器company账号过期，需重新登录。  
Error：`CodeSign /Users/Library/Developer/Xcode/DerivedData/Build/Intermediates.noindex/ArchiveIntermediates`。  
解决办法：  
    unlock keychain `security unlock-keychain -p mobvoi login.keychain`，`security set-key-partition-list -S apple: -s -k mobvoi login.keychain`；  
    删除过期provisioningprofile。`~/Library/MobileDevice/Provisioning Profiles`；  
    清除`~/Library/Developer/Xcode/DerivedData/`；  
    修改证书信任。  
参考资料：  
    [Code signing is required for product type 'Application' in SDK 'iOS 10.0' ](https://stackoverflow.com/questions/37806538/code-signing-is-required-for-product-type-application-in-sdk-ios-10-0-stic)  
    [code sign failed](https://github.com/fastlane/fastlane/issues/14040)  
    [Sh 打包报错](https://juejin.im/post/5c1074c8e51d451f751bc01e)  

## 2019.06.17
打包机器无法拉取gerrit代码。  
Error：`gerrit_jenkins@gerrit: Permission denied (publickey)`。  

[参考解决方法](https://help.github.com/en/articles/error-permission-denied-publickey)  
生成秘钥：`ssh-keygen`  
查看秘钥：`cd ~/.ssh`  
查看公钥：`cat ~/.ssh/id_rsa.pub`  
查看私钥：`cat ~/.ssh/id_rsa`  
查看已发布公钥：`cat ~/.ssh/known_hosts`  

## 2019.06.20
PCM 转 WAV  
```objective-c
- (NSData*)stripAndAddWavHeader:(NSData*) wav {
  unsigned long wavDataSize = [wav length] - 44;
  NSData *WaveFile= [NSMutableData dataWithData:[wav subdataWithRange:NSMakeRange(44, wavDataSize)]];
  NSMutableData *newWavData;
  newWavData = [self addWavHeader:WaveFile];
  return newWavData;
}

- (NSMutableData *)addWavHeader:(NSData *)wavNoheader {
  int headerSize = 44;
  long totalAudioLen = [wavNoheader length];
  long totalDataLen = [wavNoheader length] + headerSize-8;
  long longSampleRate = 8000.0;
  int channels = 1;
  long byteRate = 8 * 16000.0 * channels/8;

  Byte *header = (Byte*)malloc(44);
  header[0] = 'R';  // RIFF/WAVE header
  header[1] = 'I';
  header[2] = 'F';
  header[3] = 'F';
  header[4] = (Byte) (totalDataLen & 0xff);
  header[5] = (Byte) ((totalDataLen >> 8) & 0xff);
  header[6] = (Byte) ((totalDataLen >> 16) & 0xff);
  header[7] = (Byte) ((totalDataLen >> 24) & 0xff);
  header[8] = 'W';
  header[9] = 'A';
  header[10] = 'V';
  header[11] = 'E';
  header[12] = 'f';  // 'fmt ' chunk
  header[13] = 'm';
  header[14] = 't';
  header[15] = ' ';
  header[16] = 16;  // 4 bytes: size of 'fmt ' chunk
  header[17] = 0;
  header[18] = 0;
  header[19] = 0;
  header[20] = 1;  // format = 1
  header[21] = 0;
  header[22] = (Byte) channels;
  header[23] = 0;
  header[24] = (Byte) (longSampleRate & 0xff);
  header[25] = (Byte) ((longSampleRate >> 8) & 0xff);
  header[26] = (Byte) ((longSampleRate >> 16) & 0xff);
  header[27] = (Byte) ((longSampleRate >> 24) & 0xff);
  header[28] = (Byte) (byteRate & 0xff);
  header[29] = (Byte) ((byteRate >> 8) & 0xff);
  header[30] = (Byte) ((byteRate >> 16) & 0xff);
  header[31] = (Byte) ((byteRate >> 24) & 0xff);
  header[32] = (Byte) (2 * 8 / 8);  // block align
  header[33] = 0;
  header[34] = 16;  // bits per sample
  header[35] = 0;
  header[36] = 'd';
  header[37] = 'a';
  header[38] = 't';
  header[39] = 'a';
  header[40] = (Byte) (totalAudioLen & 0xff);
  header[41] = (Byte) ((totalAudioLen >> 8) & 0xff);
  header[42] = (Byte) ((totalAudioLen >> 16) & 0xff);
  header[43] = (Byte) ((totalAudioLen >> 24) & 0xff);

  NSMutableData *newWavData = [NSMutableData dataWithBytes:header length:44];
  [newWavData appendBytes:[wavNoheader bytes] length:[wavNoheader length]];
  return newWavData;
}
```

multipart/form-data 上传 NSData  
[multipart/form-data upload](https://stackoverflow.com/questions/24250475/post-multipart-form-data-with-objective-c-swift)  

## 2019.06.21
NSFileManager、NSFileHandle  



```objective-c
- (void)writeFile:(NSData*)data toPath:(NSString*)path{
  //  创建文件
  NSFileManager *fileManager = [NSFileManager defaultManager];
  [fileManager removeItemAtPath:path error:nil];
  [fileManager createFileAtPath:path contents:nil attributes:nil];
  //  写入文件
  NSFileHandle *fileHandle = [NSFileHandle fileHandleForWritingAtPath:path];
  [fileHandle writeData:data];
  [fileHandle closeFile];
}
```



convert `CMSampleBufferRef` to `NSData`  



```objective-c
- (NSData*)dataFrom:(CMSampleBufferRef)sampleBuffer{
  CMBlockBufferRef blockBufferRef = CMSampleBufferGetDataBuffer(sampleBuffer);
  size_t length = CMBlockBufferGetDataLength(blockBufferRef);
  Byte buffer[length];
  CMBlockBufferCopyDataBytes(blockBufferRef, 0, length, buffer);
  NSData *data = [NSData dataWithBytes:buffer length:length];
  return data;
}
```

PBXCp Error: No such file or directory  

[bitcode_strip exited with 1](https://forums.developer.apple.com/thread/12443#33677)  
In additional to your answer, there is mush simpler method. As I see, XCode uses bitcode-strip only when enviroment variable STRIP_BITCODE_FROM_COPIED_FILES is set to YES. It seems that it's set to default when enable_bitcode is switched on.  
Add User-Defined Setting STRIP_BITCODE_FROM_COPIED_FILES=NO to your Target and it will compile fine, XCode will use standard strip.  

## 2019.06.27
however, it’s generally preferable to deactivate your audio session before changing the category or other session properties. Making these changes while the session is deactivated prevents unnecessary reconfigurations of the audio system.  

Ensure that the audio session for an app using a recording category is active only while recording. Before recording starts and when it stops, ensure that your session is inactive to allow other sounds, such as incoming message alerts, to play.  

If an app supports background audio playback or recording, deactivate its audio session when entering the background if the app is not actively using audio (or preparing to use audio). Doing so allows the system to free up audio resources so that they may be used by other processes. It also prevents the app's audio session from being deactivated when the app process is suspended by the operating system.  

An audio interruption is the deactivation of your app’s audio session—which immediately stops your audio. Interruptions happen when a competing audio session from an app is activated and that session is not categorized by the system to mix with yours. After your session goes inactive, the system sends a “you were interrupted” message that you can respond to by saving state, updating the user interface, and so on.  

## 2019.06.28
如何后台运行timer：通过蓝牙设备触发。  