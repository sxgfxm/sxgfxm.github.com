---
layout: post
title: "iOS知识小集-201907"
date: 2020-01-17 11:41:32 +0800
comments: true
categories: iOS
keywords: sxgfxm, Core Bluetooth
description: Core Bluetooth
---

## 2019.07.01
一般情况下，APP退到后台 30秒 后会被挂起。  
申请后台任务，可以有至多 3分钟 时间处理任务，之后会被挂起。  
由APP 触发timer 改为 蓝牙设备定期触发 timer，唤醒APP，执行操作。  

## 2019.07.02
颈部操后台操作采用同样的逻辑。  

## 2019.07.05
颈部操音乐处理，改为耳机状态下mix & duck。  
结束后需要setActive = NO。恢复其他音乐正常播放。  

## 2019.07.10
监听`AVAudioSessionInterruptionNotification`获取打断开始和结束的通知，做相应的处理。  

pod update `error: RPC failed; curl 56 LibreSSL SSL_read: SSL_ERROR_SYSCALL, errno 60`，
增加buffer大小，`git config --global http.postBuffer 114288000`  

## 2019.07.11
`#import 'file'`，预处理后会递归复制头文件至当前文件；  
`@import Module`，优化文件体积和编译速度；  
`[[UIApplication sharedApplication] setIdleTimerDisabled:YES]; `，防止灭屏；  
`gem sources -l`，查看ruby gem镜像地址；  
`gem source -r url`，删除ruby镜像地址；  
`gem source -a url`，添加ruby镜像地址；  

## 2019.07.12
检索特定字符串  
1. 在工程目录下，`grep -r string .`；  
2. 在ipa包内，`strings - -a -arch armv7 "AppName" | grep string`，`strings - -a -arch armv7 "AppName" > AppName.txt`；  

企业版打包，普通Team member只能安装developer版本，需要由管理员导出企业证书并安装，才可以导出distribution版本。  

## 2019.07.23
无耳机条件下，A2DP不能少。  
setActive:NO 可以恢复其他APP音乐。  
开启 background task 来执行后台操作。  

## 2019.07.24
Invalid virtual filesystem overlay file，clean，删除diriveddata，重启xcode。  

制作git patch：`git format-patch commitID`  
应用git patch：`git am patchName`  
查看修改：`git apply patchName --reject`  
对照修改解决问题删除rej文件之后：`git add .` `git am --continue`  

## 2019.07.29

### Core Bluetooth
Devices that implement the central role in Bluetooth low energy communication perform a number of common tasks—for example,  
**discovering and connecting to available peripherals, and exploring and interacting with the data that peripherals have to offer. ** 
Devices that implement the peripheral role also perform a number of common, but different, tasks—for example,  
**publishing and advertising services, and responding to read, write, and subscription requests from connected centrals.**  

A peripheral typically has data that is needed by other devices.  
A central typically uses the information served up by a peripheral to accomplish some task.  

Peripherals make their presence known by advertising the data(services、characteristics) they have over the air.  
Centrals scan for nearby peripherals that might have data they’re interested in, or interact with a peripheral’s service by reading or writing the value of that service’s characteristic.  

When a central discovers such a peripheral,
the central requests to connect to the peripheral and begins exploring and interacting with the peripheral’s data.  
The peripheral is responsible for responding to the central in appropriate ways.  
You can retrieve the value of a characteristic by reading it directly or by subscribing to it.  

#### As Centrals

`CBCentralManager`  

`CBPeripheral`  
    `CBService`  
        `CBCharacteristic ` 
        `CBCharacteristic ` 
    `CBService`  
        `CBCharacteristic ` 
        `CBCharacteristic ` 

#### As Peripherals  

`CBPeripheralManager ` 

`CBMutableService ` 
    `CBMutableCharacteristic ` 
    `CBMutableCharacteristic  `

`CBCentral`  

#### Centrals  
Start up a central manager object  
```objective-c
myCentralManager = [[CBCentralManager alloc] initWithDelegate:self queue:nil options:nil];
```
Discover and connect to peripheral devices that are advertising  
```objective-c
//  specify services CBUUID to discover peripherials you are interested in
[myCentralManager scanForPeripheralsWithServices:nil options:nil];

- (void)centralManager:(CBCentralManager *)central
 didDiscoverPeripheral:(CBPeripheral *)peripheral
     advertisementData:(NSDictionary *)advertisementData
                  RSSI:(NSNumber *)RSSI {
    //  If you plan to connect to the discovered peripheral, keep a strong reference to it so the system does not deallocate it.
    NSLog(@"Discovered %@", peripheral.name);
    self.discoveredPeripheral = peripheral;
    ...
}

//  save power
[myCentralManager stopScan];
```
Connecting to a Peripheral Device After You’ve Discovered It  
```objective-c
[myCentralManager connectPeripheral:peripheral options:nil];

- (void)centralManager:(CBCentralManager *)central
  didConnectPeripheral:(CBPeripheral *)peripheral {

    NSLog(@"Peripheral connected");

    //  set peripheral delegate
    peripheral.delegate = self;

    //  specify services CBUUID to discover services you are interested in
    [peripheral discoverServices:nil];

    ...
}
```
Discovering the Services of a Peripheral That You’re Connected To    
```objective-c
- (void)peripheral:(CBPeripheral *)peripheral
didDiscoverServices:(NSError *)error {

    for (CBService *service in peripheral.services) {
        NSLog(@"Discovered service %@", service);

        NSLog(@"Discovering characteristics for service %@", service);
        [peripheral discoverCharacteristics:nil forService:service];

        ...
    }
    ...
}
```
Discovering the Characteristics of a Service  
```objective-c
- (void)peripheral:(CBPeripheral *)peripheral
didDiscoverCharacteristicsForService:(CBService *)service
             error:(NSError *)error {

    for (CBCharacteristic *characteristic in service.characteristics) {
        NSLog(@"Discovered characteristic %@", characteristic);

        //  read characteristic
        [peripheral readValueForCharacteristic:characteristic];

        //  subscribe characteristic
        [peripheral setNotifyValue:YES forCharacteristic:interestingCharacteristic];
        ...
    }
    ...
}
```
Retrieving the Value of a Characteristic  
```objective-c
- (void)peripheral:(CBPeripheral *)peripheral
didUpdateValueForCharacteristic:(CBCharacteristic *)characteristic
             error:(NSError *)error {

    NSData *data = characteristic.value;
    // parse the data as needed
    ...
}

- (void)peripheral:(CBPeripheral *)peripheral
didUpdateNotificationStateForCharacteristic:(CBCharacteristic *)characteristic
             error:(NSError *)error {

    if (error) {
        NSLog(@"Error changing notification state: %@",
           [error localizedDescription]);
    }
    ...
}
```
Writing the Value of a Characteristic  
```objective-c
NSLog(@"Writing value for characteristic %@", interestingCharacteristic);
[peripheral writeValue:dataToWrite forCharacteristic:interestingCharacteristic type:CBCharacteristicWriteWithResponse];

- (void)peripheral:(CBPeripheral *)peripheral
didWriteValueForCharacteristic:(CBCharacteristic *)characteristic
             error:(NSError *)error {

    if (error) {
        NSLog(@"Error writing characteristic value: %@",
            [error localizedDescription]);
    }
    ...
}
```

#### Peripherals
Starting Up a Peripheral Manager  
```objective-c
myPeripheralManager = [[CBPeripheralManager alloc] initWithDelegate:self queue:nil options:nil];
```
Setting Up Your Services and Characteristics  
```objective-c
//  value != nil 只可读，value == nil 可写
myCharacteristic = [[CBMutableCharacteristic alloc] initWithType:myCharacteristicUUID
                                                      properties:CBCharacteristicPropertyRead
                                                           value:myValue
                                                     permissions:CBAttributePermissionsReadable];
//  primary service && secondary service
myService = [[CBMutableService alloc] initWithType:myServiceUUID primary:YES];

//  add characteristics
myService.characteristics = @[myCharacteristic];
```
Publishing Your Services and Characteristics  
```objective-c
//  add service
[myPeripheralManager addService:myService];

- (void)peripheralManager:(CBPeripheralManager *)peripheral
            didAddService:(CBService *)service
                    error:(NSError *)error {

    if (error) {
        NSLog(@"Error publishing service: %@", [error localizedDescription]);
    }
    ...
}
```
Advertising Your Services  
```objective-c
[myPeripheralManager startAdvertising:@{ CBAdvertisementDataServiceUUIDsKey : @[myFirstService.UUID, mySecondService.UUID] }];

- (void)peripheralManagerDidStartAdvertising:(CBPeripheralManager *)peripheral
                                       error:(NSError *)error {

    if (error) {
        NSLog(@"Error advertising: %@", [error localizedDescription]);
    }
    ...
}
```
Responding to Read and Write Requests from a Central  
```objective-c
- (void)peripheralManager:(CBPeripheralManager *)peripheral didReceiveReadRequest:(CBATTRequest *)request {
    if ([request.characteristic.UUID isEqual:myCharacteristic.UUID]) {
        if (request.offset > myCharacteristic.value.length) {
            [myPeripheralManager respondToRequest:request
                withResult:CBATTErrorInvalidOffset];
            return;
        }

        request.value = [myCharacteristic.value subdataWithRange:NSMakeRange(request.offset, myCharacteristic.value.length - request.offset)];

        [myPeripheralManager respondToRequest:request withResult:CBATTErrorSuccess];
    }
}

- (void)peripheralManager:(CBPeripheralManager *)peripheral didReceiveWriteRequests:(NSArray<CBATTRequest *> *)requests{
    if ([request.characteristic.UUID isEqual:myCharacteristic.UUID]) {
        myCharacteristic.value = request.value;

        [myPeripheralManager respondToRequest:[requests objectAtIndex:0] withResult:CBATTErrorSuccess];
    }
  }
```
Sending Updated Characteristic Values to Subscribed Centrals  
```objective-c
- (void)peripheralManager:(CBPeripheralManager *)peripheral central:(CBCentral *)central didSubscribeToCharacteristic:(CBCharacteristic *)characteristic {
    NSLog(@"Central subscribed to characteristic %@", characteristic);

    NSData *updatedValue = // fetch the characteristic's new value
    BOOL didSendValue = [myPeripheralManager updateValue:updatedValue forCharacteristic:characteristic onSubscribedCentrals:nil];
}

- (void)peripherialManagerIsReadyToUpdateSubscribers:(CBPeripheralManager *)peripheral {

}
```

#### Background Processing
That said, you can declare your app to support the Core Bluetooth background execution modes to allow your app to be woken up from a suspended state to process certain Bluetooth-related events. Even if your app doesn’t need the full range of background processing support, it can still ask to be alerted by the system when important events occur.  

All Bluetooth-related events that occur while a foreground-only app is in the suspended state are queued by the system and delivered to the app only when it resumes to the foreground. That said, Core Bluetooth provides a way to alert the user when certain central role events occur. The user can then use these alerts to decide whether a particular event warrants bringing the app back to the foreground.  

`CBConnectPeripheralOptionNotifyOnConnectionKey`  
`CBConnectPeripheralOptionNotifyOnDisconnectionKey ` 
`CBConnectPeripheralOptionNotifyOnNotificationKey  `

`bluetooth-central`  
`bluetooth-peripheral `   
While your app is in the background you can still discover and connect to peripherals, and explore and interact with peripheral data. In addition, the system wakes up your app when any of the CBCentralManagerDelegate or CBPeripheralDelegate delegate methods are invoked, allowing your app to handle important central role events, such as when a connection is established or torn down, when a peripheral sends updated characteristic values, and when a central manager’s state changes.  

Upon being woken up, an app has around **10 seconds** to complete a task. Ideally, it should complete the task as fast as possible and allow itself to be suspended again. Apps that spend too much time executing in the background can be throttled back by the system or killed.  

Adding Support for State Preservation and Restoration  

Opt In to State Preservation and Restoration  
```objective-c
myCentralManager = [[CBCentralManager alloc] initWithDelegate:self
                                                        queue:nil
                                                      options:@{ CBCentralManagerOptionRestoreIdentifierKey: @"myCentralManagerIdentifier" }];
```
Reinstantiate Your Central and Peripheral Managers  
```objective-c
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    NSArray *centralManagerIdentifiers =
        launchOptions[UIApplicationLaunchOptionsBluetoothCentralsKey];
    ...
}
```
Implement the Appropriate Restoration Delegate Method  
Update Your Initialization Process  

```objective-c
- (void)centralManager:(CBCentralManager *)central
      willRestoreState:(NSDictionary *)state {

    NSArray *peripherals =
        state[CBCentralManagerRestoredStatePeripheralsKey];
    ...

    NSUInteger serviceUUIDIndex =
        [peripheral.services indexOfObjectPassingTest:^BOOL(CBService *obj,
        NSUInteger index, BOOL *stop) {
            return [obj.UUID isEqual:myServiceUUIDString];
        }];

    if (serviceUUIDIndex == NSNotFound) {
        [peripheral discoverServices:@[myServiceUUIDString]];
        ...
    }
}
```

#### Best Practices as Central
Scan for Devices Only When You Need To
Unless you need to discover more devices, stop scanning for other devices after you have found one you want to connect to.  

Explore a Peripheral’s Data Wisely
Therefore, you should look for and discover only the services and associated characteristics your app needs.  

Subscribe to Characteristic Values That Change Often
It is best practice to subscribe to a characteristic’s value when possible, especially for characteristic values that change often.  

Disconnect from a Device When You Have All the Data You Need  

Reconnecting to Peripherals  
Retrieving a List of Known Peripherals    

```
knownPeripherals = [myCentralManager retrievePeripheralsWithIdentifiers:savedIdentifiers];
```
Retrieving a List of Connected Peripherals  

#### Best Practices as Peripheral
Respect the Limits of Advertising Data
When your app is in the foreground, it can use up to **28 bytes** of space in the initial advertisement data for any combination of the two supported advertising data keys.  

Configure Your Characteristics to Support Notifications  
```objective-c
myCharacteristic = [[CBMutableCharacteristic alloc] initWithType:myCharacteristicUUID
                                                      properties:CBCharacteristicPropertyRead | CBCharacteristicPropertyNotify
                                                           value:nil
                                                     permissions:CBAttributePermissionsReadable];
```
Require a Paired Connection to Access Sensitive Data  
```objective-c
emailCharacteristic = [[CBMutableCharacteristic alloc] initWithType:emailCharacteristicUUID
                                                         properties:CBCharacteristicPropertyRead | CBCharacteristicPropertyNotifyEncryptionRequired
                                                              value:nil
                                                        permissions:CBAttributePermissionsReadEncryptionRequired];
```