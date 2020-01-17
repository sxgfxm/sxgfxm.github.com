---
layout: post
title: "iOS知识小集-201904"
date: 2020-01-17 11:24:54 +0800
comments: true
categories: iOS
keywords: sxgfxm, iOS 打包, Flutter, Dart
description: iOS 打包, Flutter, Dart
---

## 2019.04.01
企业版本打包流程：  
  1、code sign identity，创建、或者导出p12；  
  2、创建APP ID；  
  3、创建ProvisioningProfile，下载安装；  
  4、archive、inhouse；  

build can't find simulator, need to update cocoapod.  


## 2019.04.03
iOS 打包  

`xcodebuild archive -configuration $configuration VERBOSE_SCRIPT_LOGGING=YES -workspace ios/$scheme.xcworkspace -scheme $scheme -archivePath ./out/"$build_type".xcarchive`  

`xcodebuild -exportArchive \
-allowProvisioningUpdates \
-archivePath ./out/"$build_type".xcarchive \
-exportOptionsPlist ios/$ExportOptions.plist \
-exportPath ./out/`   

添加 **configuration** 并且创建对应的 **.xcconfig** 文件需要添加到 Flutter 目录下  
命令打包、导出成功；  
需要配置相应的编译配置；  
生成 **ExportOptions.plis **  
配置 **$scheme $configuration $build_type**  
注意 **archive路径问题**  
忽略已经在git中的文件 **.gitignore** 需要删除对应文件才生效 `git rm -r file`  
服务器安装 **ProvisioningProfile** 否则无法打包  
jenkins **workspace**  仿照 vpa_release 创建 jenkins 工程  
**Xcode Command /usr/bin/codesign failed with exit code 1 : errSecInternalComponent** 需要打开 **keychain**  

## 2019.04.11
Android 打包  

添加 **安卓模拟器 ** 

**initializing gradle** 巨慢无比  **替换为本地gradle版本**  
distributionUrl=https\://services.gradle.org/distributions/gradle-4.6-all.zip  

**Error Domain=DVTPortalServiceErrorDomain Code=1100 "Your session has expired.  Please log in."** 重新登录账号  

生成 **key.jks** 文件 `keytool -genkey -v -keystore ~/key.jks -keyalg RSA -keysize 2048 -validity 10000 -alias key`  
添加 **<app dir>/android/key.properties** *不要加入到git中*  

```
storePassword=<password from previous step>
keyPassword=<password from previous step>
keyAlias=key
storeFile=<location of the key store file, e.g. /Users/<user name>/key.jks>
```
修改 **<app dir>/android/app/build.gradle**  
```java
def keystorePropertiesFile = rootProject.file("key.properties")
def keystoreProperties = new Properties()
keystoreProperties.load(new FileInputStream(keystorePropertiesFile))

signingConfigs {
    release {
        keyAlias keystoreProperties['keyAlias']
        keyPassword keystoreProperties['keyPassword']
        storeFile file(keystoreProperties['storeFile'])
        storePassword keystoreProperties['storePassword']
    }
}
buildTypes {
    release {
        signingConfig signingConfigs.release
    }
}
```

运行 **flutter build apk** 生成 apk 文件，运行 **flutter install** 安装至手机。  

## 2019.04.09
耳机颈部操  
耳机配对：先配对经典蓝牙，再配对对应BLE  
耳机通信：**先注册服务，再接收耳机通知**  

## 2019.04.22

flutter   
    1、stateful widget、stateless widget；  
    2、animation；  
    3、paint；  
    4、opacity；  
    5、layout；  
    6、navigator & route；  
    7、network request；  
    8、ProgressIndicator；  
    9、asset、AssetBundle；  
    10、localizable、flutter_localizations（itl）；  
    11、life circle；  
    12、listview、ListView.Builder；  
    13、gesture、GestureDetector；  
    14、theme、font；  
    15、text filed；  
    16、GPS（location）、camera（image_picker）、userdefault（SharePreferences）、database（SQFlite）、notification（firebase_messaging）；  
    17、SharePreferences；  
    18、[平台 UI 定制](https://medium.com/flutter-io/do-flutter-apps-dream-of-platform-aware-widgets-7d7ed7b4624d)；  
    19、[json](https://github.com/flutter/samples/tree/master/jsonexample/lib)  

## 2019.04.24
flutter_go  

#### Application：  

​    router：路由  
​    tabbarController：tab bar  
​    sharedPeference：共享存储  

#### Router：  

​    路由管理：[fluro](https://pub.dartlang.org/packages/fluro)  
​    定义路由名称：routers.dart  
​    定义路由事件：routers_handler.dart  

#### TabBarController：  

​    创建TabController：`controll = TabController(initialIndex: 0, vsync: this, length: 4)`；  
​    绑定TabController：`TabBarView(controller: controller, children: <Widget>[Page1(), Page2(), Page3()])`；  
​    创建bottomNavigationBar：`Material( child: TabBar(controller: controller, tabs: myTabs))`；  

#### Widgets：  

#####     ThemeData：  

​        颜色：primaryColor，backgroundColor，accentColor；  
​        字体：textTheme；  
​        图标：iconTheme；  

#####     Color：  

​        定义全局 ThemeColor：`const int ThemeColor = 0xFFC91B3A`；  
​        通过颜色值创建颜色：`Color(ThemeColor)`；  
​        获取ThemeColor：`Theme.of(context).primaryColor`；  

#####     PageController：  

​        创建controller：`controller = PageController(initialPage: index)`；  
​        销毁controller：`controller.dispose()`；  
​        操作controller：`controller.animateToPage(1, duration: Duration(milliseconds: 300), curve: Curves.linear)`；  
​        绑定controller：`PageView(controller: controller, onPageChanged: _onPageChanged, children: _buildItems)`  
​        indicator：Row of circles；  

#####     ListRefresh：  

​        异步方法，当`mounted = true`时，才能调用`setState`；  
​        分页数据请求：requestApi  
​        cell：renderItem  
​        headerView：headerView  

#### NetUtils  

​    网络请求：[dio](https://pub.dartlang.org/packages/dio) 
​    get  
​    post  
​    json  
​    protobuf  

#### Utils  

​    时间、日期  

#### Timer：  

​    创建Timer：`timer = Timer.periodic(Duration(seconds: 5), (timer) { /// do something })`；  
​    销毁Timer：`timer.cancel()`；  