---
layout: post
title: "iOS知识小集-201901"
date: 2020-01-17 10:44:28 +0800
comments: true
categories: iOS
keywords: sxgfxm, Swift, RxSwift, RTCRoom
description: Swift, RxSwift, RTCRoom
---

## 2019.01.02

### Swift 进阶
Swift 语言特性  
- Swift 既是高级语言，又是低级语言。既可以使用 **map** 、 **reduce** 这样的方法，编写 **高阶函数**，又可以直接编译出原生的二进制可执行文件，具有和 C语言 媲美的性能。  （如何理解第二点？）  
- Swift 是多范式语言。既可以 **面向对象编程** ，又可以 **函数式编程** 。  
- Swift 鼓励自下而上编程。可以自己构建组件。  
- Swift 代码紧凑、精确，同时保持清晰。  
- Swift 在实践中是相对安全的。（如何检查变量在被使用前是赋值的？）  
- Swift 在不断的发展中。  

专业术语  
- 值语义：  
- 值类型：具有值语义的类型为值类型，**struct**、 **enum** 都是值类型。  
- 引用类型：  
- 浅拷贝：  
- 深拷贝：  
- 闭包：持有外部变量的函数成为 **闭包（closure）** 。  
- 高阶函数：  
- 静态派发：  
- 动态派发：  

### Atom 快捷键
打开快捷键列表：cmd + shift + P；  
模糊搜索标题：cmd + P；  

### adb 取 Android 系统日志
1. `brew cask install android-platform-tools`；
2. `adb bugreport`；

### 字节跳动组织核心
技术、用户增长、商业化。  
留存、拉新、变现。

## 2019.01.03

### Swift 进阶
使用函数将行为参数化，复用模板代码，调用者提供变换函数。  
将重复的代码抽象为函数，将函数行为抽象为模板，作为扩展重复使用。  
<T>表示声明 T 为某种类型的占位符。  

内建集合类型  
- 标准库中的集合类型都具有值语义，且都是用了“写时复制”技术；

数组变形  
- 尽量避免直接使用索引值访问数组元素；  
- sort：  
- map：对数组中每个元素进行变换，返回新数组；  

```   swift
extension Array {
  func map<T>(_ transform: (Element) -> T) -> [T] {
    var result: [T] = []
    result.reserveCapacity(count)
    for x in self {
      result.append(transform(x))
    }
    return result
  }
}

[1, 2, 3].map{ x in
  return x + 1
}

[1, 2, 3].map{$0 + 1}
```

- filter：筛选出符合条件的元素，返回新数组；  

```  swift
extension Array {
  func filter(_ predicate:(Element) -> Bool) -> [Element] {
    var result: [Element] = []
    for x in self where predicate(x) {
      result.append(x)
    }
    return result
  }
}

[1, 2, 3].filter{ x in
  return x > 0
}

[1, 2, 3].filter{$0 > 1}
```

- reduce：合并数组元素，返回合并值；  

```  swift
extension Array {
  func reduce<T>(_ initialValue: T, _ transform: (T, Element)-> T) - > T {
    var value = initialValue
    for x in Self {
      value = transform(value, x)
    }
    return value
  }
}

[1, 2, 3].reduce(0){value, x in value + x}
[1, 2, 3].reduce(0, +)
```

- acummulate：迭代操作元素，返回新数组；  

```  swift
extension Array {
  func accumulate<Result>(_ initialResult: Result, _ nextParitialResult:(Result, Element) -> Result) -> [Result]{
    var running = initialResult
    return map{ next in
      running = nextParitialResult(running, next)
      return running
    }
  }
}

[1, 2, 3].accumulate(0, +)
```

- all：判断是否所有元素都符合条件；  

```swift
extension Array {
  func all(_ predicate: (Element) -> Bool) -> Bool {
    return !contains{!predicate($0)}
  }
}

[1, 2, 3].all{$0 > 0}
```

- map2：用reduce实现map；

```swift
extension Array {
  func map2<T>(_ transform:(Element) -> T) -> [T] {
    return reduce([]){
      $0 + [transform($1)]
    }
  }
}
```

- flatMap：先map再joined，当变换函数返回值为数组，且需要合并时使用；  

```swift
extension Array {
  func flatMap<T>(_ transform:(Element) -> [T]) -> [T] {
    var result: [T] = []
    for x in self {
      result.append(contentsOf: transform(x))
    }
    return result
  }
}
```

- forEach：无返回值迭代数组元素；  

## 2019.01.03

### Swift 进阶
数组类型  
- 切片：切片只是数组的另一种表示方法，使用ptr、startIndex、endIndex表示，并不会创建新数组；
（如果改变切片中元素，原数组中元素会改变吗？）  
- 桥接：Swift数组可以桥接到OC中，编译器会自动把不兼容的值用一个不透明的box包起来；

### Swfit 基础
- Type Parameters
- Type Parameters Constraints
- Generic Type
- Generic Founction
- Associated Type
- Associated Type Constraints
- Generic where Clause

## 2019.01.07

### Swift 进阶
字典
- removeValue(forKey:)  
- updateValue(\_:forKey:)  
- mapValue  
- frequency  
- merge(\_:uniqueKeysWith:)  

集合
- IndexSet：整数集合，支持范围操作；  
- CharacterSet：Unicode字符集合；  

## 2019.01.08

### Swift 进阶
范围
- Range  
- CloseRange  
- Comparable 协议；  
- Strideable 协议；  

## 2019.01.09

### Swift 进阶
- **Sequence** 协议是集合类型结构的基础，一个序列代表一系列具有相同类型的值，你可以对这些值进行迭代；

```swift
protocol Sequence {
  associated Iterator : IteratorProtocol
  func makeIterator() -> Iterator
}
```
- **IteratorProtocol** 协议是对迭代器的描述，通过`next()`方法返回序列中下一个值；

```swift
protocol InteratorProtocol {
  associated Element
  mutating func next() -> Element?
}
```

## 2019.01.10

### APP发布
- 封板之后不要再往发版分支合代码；  
- 修改一定要验证，尤其是跨分支 **cherry-pick** 代码；  
- APP发布之前一定要看一下 **TestFlight** 版本是否正常；  

### Principles
- What do you want ？  
- What is true?  
- What are you going to do about it ?  

### 微信公开课
- 我们更多倡导的是利用微信做出好产品分享用户。  
- 因为遵循原则，很多东西我们又必须坚持去改变。  
- 第一我们没有批量导入某一批好友，而是通过用户手动一个一个挑选。第二，在一个产品还没有被验证只能够产生自然增长的时候，我们没有去推广它。  
- 因为好的产品需要一定的独裁，否则它将包含很多不同意见以至于产品性格走向四分五裂。  
- 第一，坚持做一个好的，与时俱进的工具。  
- 微信是一个生活方式。  
- 第二个原动力是，“让创造者体现价值”。  
- 一个好的产品是有自己的使命的。  
- 最主要的是，技术的使命应该是帮助人类提高效率。  
- 一切盈利都是做好产品做好服务后的自然而来的副产品。  
- 小游戏的原动力是，它应该是一个关于创意的平台。并且让产生创意的人体现价值。  

### Swift 开发
- 使用 **Kingfiser** 异步加载网络图片 `imageView.kf.setImage(with:URL(string:"http://xxx.png"))`；  
- 使用 **Material** 自动布局 `view.layout().top().left().width().height()`；  
- 使用 **SnapKit** 自动布局 `view.snp.makeConstrains{(make) in // layout}`；  
- 使用 **Extension** 按功能模块分隔代码，相对清晰；  
- 使用 **Moya** 封装网络模块请求；  
- 使用 **IGListKit** 实现CollectionView，TableView；  
- 使用 **SwiftyUserDefaults** 实现 UserDefault 存取；  
- 使用 **Realm** 实现 数据库 存取；  
- 使用 **Codable** 实现 JSON 解析；  

## 2019.01.11

### RxSwift
- Every **Observable** sequence is just a sequence. The key advantage for an Observable vs Swift's Sequence is that it can also receive elements asynchronously.  
- **Observable**(**ObservableType**) is equivalent to **Sequence**.  
- **ObservableType.subscribe** method is equivalent to **Sequence.makeIterator** method.  
- Observer (callback) needs to be passed to **ObservableType.subscribe** method to receive sequence elements instead of calling **next()** on the returned iterator.  
- When an observable is created, it doesn't perform any work simply because it has been created.  
- However, if you just call a method that returns an **Observable**, no sequence generation is performed and there are no side effects. **Observable** just defines how the sequence is generated and what parameters are used for element generation. **Sequence** generation starts when **subscribe** method is called.  

## 2019.01.16

### RxSwift 使用场景
- 使用 **RxSwift** 实现 **Target Action**；
- 使用 **RxSwift** 实现 **Delegate**；
- 使用 **RxSwift** 实现 **闭包回调**；
- 使用 **RxSwift** 实现 **Notification**；
- 使用 **RxSwift** 实现 **KVO**；
- 使用 **RxSwift** 实现 **依赖任务**；
- 使用 **RxSwift** 实现 **多异步任务同步**；

### RxSwift 核心概念
- **Obervable**：Single，Completable，Maybe，Driver，ControlEvent
- **Observer**：AnyObserver，Binder
- **Obervable & Observer**：AsyncSubject、PublishSubject、ReplaySubject、BehaviorSubject、Variable、ControlProperty
- **Operator**：创建新序列 或 变换、组合原有序列
- **Disposable**：DisposeBag、takeUntil
- **Schedulers**：subscribeOn、observeOn、MainScheduler、SerialDispatchQueueScheduler、ConcurrentDispatchQueueScheduler、OperationQueueScheduler
- **Error Handling**：retry、retryWhen、catchError

## 2019.01.17

### RxSwift 常用操作符

Observable：描述可观察序列值是如何产生的，或者说观察者是如何接收该序列的，当subscribe时，真正触发序列产生。

创建 Observable  
- **create**：通过构建函数，完整创建一个自定义的 Observable。各种操作符都是通过create实现的。  
- **just**：创建只发出一个元素的 Observable。当只产生一个元素时，可以使用just快速创建，比如网络请求结果？  
- **timer**：创建一定延时后，只发出一个元素的 Observable。类似于 dispatch_after ？  
- **from**：将其他类型转换为 Observable。比如数组、可选值等。不必自己实现这个转化过程了。  
- **repeatElement**：创建重复发出某个元素的 Observable。  
- **deferred**：直到订阅发生，才创建 Observable，并且为每位订阅者创建全新的 Observable。订阅者获取独立序列。  
- **interval**：创建每隔一段时间就发出一个索引数的 Observable。相当于一个repeated timer。  
- **empty**：创建只发出一个完成事件的 Observable。什么时候使用呢？  
- **never**：创建一个不会发出任何元素的 Observable。什么时候使用呢？  
- **startWith**：创建一个在头部插入一些元素的 Observable。  
- **error**：创建一个只有 error 的 Observable。  

创建 可组合的Observable  
- **merger**：创建一个合并多个 Observable 的 Observable，当某个 Observable 发出元素时，他就发出元素。类似多条生产线合并到一个出口。可交替发出元素。  
- **concat**：创建一个合并多个 Observable 的 Observable，当前一个 Observable 发出完毕时，下一个 Observable 才开始发出元素。按顺序发出元素。  
- **zip**：通过组合函数，创建一个将多个 Observable 的元素组合之后发出的 Observable。严格按照索引数组合发出。  
- **combineLatest**：通过组合函数，创建一个将多个 Observable 的最新元素组合之后发出的 Observable。用最新值替换对应值后组合发出。  

转换 Observable 元素  
- **map**：通过转换函数，将 Observable 的每个元素转换后发出。  
- **scan/accumulator**：通过转换函数，将 Observable 的每个元素累积之前的结果转换后发出。  
- **reduce**：通过转换函数，将 Observable 的所有元素累积的结果发出。  

转换 Observable 元素 为 Observable  
- **flatMap**：通过转换函数，将 Observable 的每个元素转换成其他的 Observable ，然后将这些 Observables 合并发出；  
- **flatMapLatest**：通过转换函数，将 Observable 的每个元素转换成其他的 Observable，然后取最新的发出；  
- **concatMap**：通过转换函数，将 Observable 的每个元素转换成其他的 Observable 后，按顺序发出；  
- **publish**：将 Observable 转换为可被连接的 Observable。直到 connect 才开始发出元素。  
- **replay**：将 Observable 转换为可被连接的 Observable。直到 connect 时开始发出缓存的最新n个元素。  
- **refCount**：将 Observable 转换为可自动连接和断开的的 Observable。  
- **connect**：通知 ConnectableObservable 可以开始发出元素了。  

如何发送 Observable 元素  
- **delay**：将 Observable 的所有元素延迟一段设定好的时间发出；  
- **materialize**：将 Observable 产生的时间全部转换成元素发出；  
- **dematerialize**：将 materialize 转换后的元素还原；  
- **ignoreElements**：将 Observable 产生的 next 事件忽略，只发出 completed 和 error 事件；只关心终止时使用  
- **buffer**：将 Observable 元素周期性的以 元素集合 发出来。  
- **window**：将 元素集合 周期性的以 Observable 形态发出来。  
- **groupBy**：将 Observable 以键值分组为 子Observable 发出来。  
- **single**：将 Observable 限制为只发出一个元素。  

- **filter**：只发出通过判定的元素。  
- **take**：只发出头 n 个元素。  
- **takeWhile**：从头发出通过判定的元素。  
- **takeUntil**：从头发出直到另一个 Observable 发出元素。  
- **takeLast**：只发出尾 n 个元素。 
- **elementAt**：只发出第 n 个元素。  
- **skip**：跳过头 n 个元素。  
- **skipWhile**：从头跳过通过判定的元素。  
- **skipUntil**：从头跳过直到另一个 Observable 发出元素。  
- **sample**：按第二个 Observable 对 第一个 Observable 采样发出元素。  
- **debounce**：过滤掉高频产生的元素。  
- **distinctUntilChanged**：只发出与上个元素不同的元素。  
- **delaySubscription**：将延迟一段时间后才订阅 Observable。  

- **amb**：在多个源 Observable 中，取第一个发出元素的 Observable 只发出其元素。  

在哪个线程发出和接收元素  
- **subscribeOn**：指定 Observable 在哪个 Scheduler 执行。  
- **observeOn**：指定 Observable 在哪个 Scheduler 发出通知。  

当发生某些事件时如何操作  
- **do**：当 Observable 产生某些事件时，执行某个操作。可以来注册一些回调操作，单独回调。  
- **timeout**：如果 Observable 在规定时间内么有产生任何元素，产生一个超时的 error 事件。  

如何处理 Observable error 事件  
- **catchError**：拦截一个 error 事件，将它替换成其他 Observable。  
- **catchErrorJustReturn**：拦截一个 error 事件，将它替换成其他元素并结束。  
- **retry**：拦截一个 error 事件，并重新订阅该 Observable。  

- **using**：创建一个可被清除的资源，它和 Observable 具有相同的生命周期。  

### RxSwift 常用架构
- **MVVM**：View --> Observable --> ViewModel --> Observable --> Controller  
- **RxFeedback**：State --> Event --> StateChanged --> Event --> State  
- **ReactorKit**：View --> Action --> Reactor --> State  

## 2019.01.18

### IGListKit
- [ListDiffable] --> ListAdapter --> ListSectionController --> Cell  
- Data： needs conform ListDiffable  
- ListAdapter：init with UICollectionView and ListAdapterUpdater，set datasource delegate，use `performUpdates(animated:)` to tell data has changed
- UIViewController：needs conform ListAdapterDataSource，implement `objects(for:)`，`listAdapter(_:sectionControllerFor:)`，`emptyView(for:)`
- ListSectionController：needs save data, state, and override `numberOfItems`，`cellForItem(at:)`，`sizeForItem(at:)`，`didUpdate(to:)`，`didSelectItem(at:)`
- UICollectionViewCell：needs implement `cellSize(for:)`
- 布局的关键在于如何划分Cell，采用分层组合的方式，类似于 ComponentKit ，提高复用性
- `UUID().uuidString` 来生成 唯一标识
- 一种 model 类型 对应一种 sectionController，sectionController 负责如何展示 model，可以用一个cell展示，也可以分多个cell展示
- 避免数据与UI不一致导致的crash
- 分离dataSource；

## 2019.01.24

### SwiftyUserDefaults
- add keys

```
extension DefaultsKeys {
  static let firstInFlag = DefaultsKey<Bool>("firstInFlag")
  static let username = DefaultsKey<String?>("username")
  static let password = DefaultsKey<String?>("password")
}
```

- use keys

```
func storeDataWithUserDefault (){
    if !Defaults[.firstInFlag] {
      print("Is first in")
      Defaults[.firstInFlag] = true
      Defaults[.username] = "Light"
      Defaults[.password] = "123456"
    } else {
      print("Not first in")
      print("username \(Defaults[.username] ?? "not set")")
      print("password \(Defaults[.password] ?? "not set")")
    }
  }
```

### RealmSwift
- Declare Model

```
class Person: Object {
  @objc dynamic var name = ""
  @objc dynamic var age = 0
}
```

- Get Realm `let realm = try! Realm()`
- Query `let studentOver20 = realm.objects(Person.self).filter("age > 20").first`
- Update `try! realm.write { realm.add(mySelf) realm.add(other) }`

## 2019.01.25

### Swift 5.0 新特性
- 应用瘦身：Swfit 应用不再包含标准库的动态链接库；
- Swift 语言
  - `@dynamicCallable` 简化方法调用，增强与动态语言的协作性；
  - Key paths 现在支持指向自身的 key path `\.self`；
  - enum case 不定参数 需转换为 数组传入；
  - `try?` 表达式不再返回嵌套的optional；
  - 字面量初始化
  - String 插值更高效，`_ExpressibleByStringInterpolation`方法需要替换；
- Swfit 标准库
  - `DictionaryLiteral` 重命名为 `KeyValuePairs`；
  - `Sequence` 不再有关联类型 `SubSequence`；
- Swift Package Manager
- Swift Compiler

## 2019.01.28

### RxSwift 原理解析

## 2019.01.30

### RTCRoom
- Login
  - 初始化 `RTCRoom`；
  - 获取 `LoginInfo`；
  - 登录 `-login:loginInfo:withCompletion:`；
- Get Room List
  - 获取聊天室列表 `-getRoomList:cnt:withCompletion:`；
- Create Room
  - 创建聊天室 `-creatRoom:roomInfo:withCompletion:`；
- Enter Room
  - 进入聊天室 `-enterRoom:withCompletion:`；
- Show Local Video
  - 展示本地视频 `-startLocalPreview:`；
  - 停止本地视频 `-stopLocalPreview`；
  - 设置音频清晰度 `-setHDAudio:`；
  - 设置视频码率 `-setBitrateRange:max:`；
  - 切换前后摄像头 `-switchCamera`；
  - 静音推流 `-setMute:`；
  - 设置美颜效果 `-setBeautyStyle:beaultyLevel:whitenessLevel:ruddinessLevel:`；
  - 进入前台 `-switchToForeground`；
  - 进入后台 `-switchToBackground:`；
- Show Remote Video
  - 展示远程视频 `-addRemoteView:withUserID:playBegin:playError:`；
- On Get Pusher List
  - 记录 `PusherInfo`；
  - 记录 `playerView`；
  - 展示远程视频，出错时 `-onPusherQuit:`；
  - 重新布局；
- On Pusher Join
  - 记录 `playerView`；
  - 记录 `PusherInfo`；
  - 展示远程视频，出错时 `-onPusherQuit:`；
  - 重新布局；
- On Pusher Quit
  - 删除 `playerView`；
  - 删除 `PusherInfo`；
  - 重新布局；
- On Room Close
  - 提示聊天室关闭；