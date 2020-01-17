---
layout: post
title: "iOS知识小集-201903"
date: 2020-01-17 11:18:45 +0800
comments: true
categories: iOS
keywords: sxgfxm, Dart, Flutter
description: Dart, Flutter
---

## 2019.03.08

### Flutter开发流程搭建

目标：  
    实现Flutter对原来Native工程的非侵入式集成 —— 通过pod集成；  
    保证Flutter编译环境的唯一性 —— 通过服务器编译产生构建产物；  
    实现了Flutter的持续集成 —— 自动编译flutter代码并同步flutter构建产物；  

 

1.  在打包机器安装 flutter 开发环境；√  

下载 [Flutter SDK](https://flutter.dev/docs/development/tools/sdk/archive?tab=macos#macos)；

配置环境变量：`export PATH=[FLUTTER_INSTALL_PATH]/flutter/bin:$PATH`；  

安装Xcode、Android Studio；  

`flutter doctor`  
  iOS：  
    brew update  
    brew install --HEAD usbmuxd  
    brew link usbmuxd  
    brew install --HEAD libimobiledevice  
    brew install ideviceinstaller  
    brew install ios-deploy  
  Android：  
    update Android SDK 28  
  Plugins：  
    Flutter  
    Dart  

2. 新建 gerrit 测试项目 flutter_test、flutter_sdk_test、flutter_pod_test；√  

3. 新建 jenkins 打包项目 flutter_test；√  

4. 添加 Flutter 自动编译脚本；√  

    **jenkins源码配置：Refspec：refs/changes/*:refs/changes/***  
    **添加环境变量：export PATH=/Users/Mobvoi/Library/flutter/bin:$PATH**  
    **自动使用company签名：修改源工程配置**  
    修改项目 Bundle ID，使用公司证书签名，需要区分证书；  
    `flutter build ios` 编译 release 版本；  
    `flutter build ios --debug` 编译 debug 版本；  
    编译产物在 `project_dir/ios/Flutter` 目录下；  
    将编译产物导出至 `project_dir/FlutterSDK` 目录下；  
    build.sh  

```sh
#!/bin/sh

# keychain 访问权限
security unlock-keychain -p mobvoi login.keychain

# 添加环境变量
export PATH=/Users/Mobvoi/Library/flutter/bin:$PATH

//  获取当前目录
project_dir=$(cd "$(dirname "$0")";pwd)
echo ${project_dir}
//  release产物路径
release_sdk_dir=${project_dir}/FlutterSDK/Release
echo ${release_sdk_dir}
//  debug产物路径
debug_sdk_dir=${project_dir}/FlutterSDK/Debug
echo ${debug_sdk_dir}

//  清空上次编译产物
rm -rf ${release_sdk_dir}
rm -rf ${debug_sdk_dir}

//  创建产物目录
mkdir -p ${release_sdk_dir}
mkdir -p ${debug_sdk_dir}

//  使用release证书编译
#flutter build ios

//  导出release编译产物
echo "Start exporting flutter release sdk done!"
cp -rf ${project_dir}/ios/Flutter/*.framework ${release_sdk_dir}
cp -rf ${project_dir}/ios/Flutter/flutter_assets ${release_sdk_dir}
echo "Export flutter release sdk done!"

//  使用debug证书编译
#flutter build ios --debug

//  导出debug编译产物
echo "Start exporting flutter release sdk done!"
cp -rf ${project_dir}/ios/Flutter/*.framework ${debug_sdk_dir}
cp -rf ${project_dir}/ios/Flutter/flutter_assets ${debug_sdk_dir}
echo "Export flutter debug sdk done!"

```

6. 创建 pod 包含 flutter 工程 √  
创建pod：`pod spec create flutter_sdk`  
修改配置文件：  
```sh
s.prepare_command = "build.sh"
s.vendored_frameworks = 'Framework/Debug/*.framework'
s.resources = 'Framework/Debug/flutter_assets'
```

server 编译生成产物  
pod 下载产物  

检查依赖 `pod lib lint --verbose --no-clean --allow-warnings --sources=MobvoiPods,master`
推送到仓库 `pod repo push MobvoiPods flutter_test.podspec --allow-warnings --sources=MobvoiPods,master`  

5. flutter_test 编译成功后将编译产物上传至 flutter_sdk_test，区分 iOS 和 Android；
    export.sh  

6. 构建 flutter_pod_test 自动从 flutter_sdk_test 下载iOS编译产物，区分 release 和 debug；
    download.sh  

7. one 工程依赖 flutter_pod_test，打包时区分 release 和 debug；  

8. 时序图：  
```sh
@startuml
actor Light
participant local_FlutterSDK
participant gerrit_FlutterSDK #99FF99
participant jenkins_FlutterSDK #FF9999
participant local_One

== Flutter ==

Light -> local_FlutterSDK : Develop
local_FlutterSDK -> gerrit_FlutterSDK : Push to gerrit
gerrit_FlutterSDK -> jenkins_FlutterSDK : Trigger compiling
note right #99FF99
  build_debug.sh
  build flutter debug SDK
end note
jenkins_FlutterSDK --> gerrit_FlutterSDK : Success
gerrit_FlutterSDK --> Light : Success
Light -> gerrit_FlutterSDK : Merge code
gerrit_FlutterSDK -> jenkins_FlutterSDK : Trigger compiling
note right #99FF99
  export_debug.sh
  build flutter debug SDK
  export flutter debug SDK
  update pod version
end note
jenkins_FlutterSDK --> gerrit_FlutterSDK : Success
gerrit_FlutterSDK --> Light : Success

== iOS ==

Light -> local_One : Pod update
local_One -> jenkins_FlutterSDK : Fetch flutter SDK
note right #99FF99
  FlutterSDK.podspec
  s.vendored_frameworks = 'Framework/*.framework'
  s.resources = 'Framework/flutter_assets'
end note
jenkins_FlutterSDK --> local_One : Success
@enduml
```

学习资料：https://github.com/alibaba/flutter-go

## 2019.03.22
1. 运行flutter release 版本；  
2. 从网上下载的flutter工程需要配置dart 和 flutter SDK路径；  
3. podspec需要指定系统版本；  
4. pod lib lint 和 pod spec lint；  
5. flutter 版本需保持一致；  
6. flutter upgrade 升级；  
7. 将 App.framework 和 Flutter.framework 上传；  
8. 添加flutter编译时脚本；  
9. framework 和 static library 有什么区别；  

### PodFile

#### Root Options
`install!`：指定`pod install`时执行的方法和选项。  

#### Dependencies
`pod`：指定工程的依赖。  

- `:configurations => ['Debug','Release']`：根据编译配置选择性安装依赖。  
- `:source => 'https://github.com/CocoaPods/Specs.git'`：指定pod库的来源。  
- `:subspecs => ['Subspec1', 'Subspec2']`：指定需要安装的Subspec。  
- `:path => '~/Documents/LocalPod'`：指定安装本地Pod。  
- `:git => 'https://github.com/gowalla/AFNetworking.git', :branch => 'dev', :tag => '0.1.0'`：指定安装的库地址及分支或标签。  
- `:podspec => https://example.com/JSONKit.podspec`：指定podspec的来源。  
- `:inhibit_warnings => true`：屏蔽Pod中语法警告。  

`podspec`：指定podspec文件的来源。  

`target 'ProjectTargetName' do ... end`：指定Target的依赖。  

`script_phase :name => 'HelloWorldScript', :script => 'echo "Hello World"', :shell_path => '/usr/bin/ruby'`：指定运行脚本。  

`abstract_target 'AbstractTargetName' do ... end`：指定抽象Target的依赖，方便多个Target继承。  

`inherit!`：指定依赖继承。  

- `:complete`：完全继承父亲行为。  
- `:none`：不继承父亲行为。  
- `:search_paths`：只继承父亲库的搜索路径。  

#### Target Configuration
`platform :ios, '9.0'`：设置系统版本。  

`project 'TestProject', 'App Store' => :release, 'Test' => :debug`：根据工程build configuration选择debug或release版本。  

`inhibit_all_warnings!`：屏蔽所有Pod中语法警告。  

`use_modular_headers!`：指定静态库使用modular headers。  

`use_frameworks!`：指定使用frameworks而不是静态库。  

#### Workspace
`workspace 'MyWorkspace'`：指定workspace。  

`generate_bridge_support!`：指定生成BridgeSupport文件。  

`set_arc_compatibility_flag!`：指定需要添加`-fobjc-arc`flag。  

#### Source
`source 'https://github.com/CocoaPods/Specs.git'`：指定全局仓库地址。  

#### Hooks
`plugin 'cocoapods-keys', :keyring => 'Eidolon'`：指定安装时需要的插件。  

`pre_install do |installer| ... end`：Pod下载好，安装前，执行。  

`post_install od |installer| ... end`：Pod安装好，生成Xcode project时执行。  

`supports_swift_versions`：指定所需要的Swift版本支持。  

### Podspec

#### Root specification
`spec.source`：指定Pod库的源地址。  

- `:git => :tag, :branch, :commit, :submodules ` 
- `:svn => :folder, :tag, :revision`  
- `:hg => :revision ` 
- `:http => :flatten, :type, :sha256, :sha1`  

`spec.prepare_command`：当Pod下载好之后执行的脚本，可以用来创建、删除、修改下载好的文件。脚本会在Pod清除前和Pod工程创建前执行，目录是Pod的根目录。  

#### Platform
`spec.platform = :ios, '9.0'`：指定Pod支持的系统平台和版本。  
`spec.ios.deployment_target = '9.0'`：指定支持的系统平台的最低版本。  

#### Build Settings
`spec.dependency 'AFNetworking', '~> 1.0'`：指定依赖的第三方库。  
`spec.requires_arc = true`：指定需要支持ARC。  
`spec.framework = 'QuartzCore'`：指定Target需要链接的系统库。   
`spec.library = 'xml2'`：指定Target需要链接的系统库。  
`spec.weak_framework = 'Twitter'`：指定Target需要weak链接的库。  
`spec.compiler_flags = '-DOS_OBJECT_USE_OBJC=0'`：传递给编译器的标识列表。  
`spec.prefix_header_file = false`：指定是否引入公共头文件。  
`spec.script_phase = {}`：当Pod编译时执行的脚本。  

#### File patterns
`*`：匹配任何文件。  
`**`：递归的匹配目录。  
`?`：匹配任何一个字符。  
`[set]`：匹配set中的任何一个字符。  
`{p,q}`：匹配字符p或者字符q。  

`spec.source_files = 'Classes/**/*.{h,m}'`：指定Pod的源文件。  
`spec.public_header_files = 'Headers/Public/*.h'`：指定公共头文件。  
`spec.private_header_files = 'Headers/Private/*.h'`：指定私有头文件。  
`spec.vendored_frameworks = 'Frameworks/MyFramework.framework'`：与Pod一起绑定的framework。  
`spec.vendored_libraries = 'libProj4.a'`：与Pod一起绑定的静态库。  
`spec.resources = '[Images/*.png]'`：需要拷贝到Target中的资源文件。  
`spec.exclude_files = 'Classes/osx'`：需要忽略的文件。  
`spec.preserve_paths = 'Important.text'`：下载后不应该被删除的文件。  