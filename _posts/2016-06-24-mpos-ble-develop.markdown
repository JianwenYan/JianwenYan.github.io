---
layout: post
title: MPOS BLE 开发记录
date: 2016-06-24 11:26:24.000000000 +09:00
tags: Develop 
---

#### 
![](/assets/images/2016/mpos-ble-sdk/mpos-ble-sdk-head.jpg)
#### 

整体框架:

#### 
![](/assets/images/2016/mpos-ble-sdk/mpos-sdk-frame.jpeg)
#### 

蓝牙操作－连接设备:

#### 
![](/assets/images/2016/mpos-ble-sdk/mpos-ble-operation-part1.jpeg)
#### 

蓝牙操作－获取特征值进行数据传输：

#### 
![](/assets/images/2016/mpos-ble-sdk/mpos-ble-operation-part2.jpeg)
#### 

Tip 1.蓝牙状态 CBCentralManagerState


```
typedef NS_ENUM(NSInteger, CBCentralManagerState) {
	CBCentralManagerStateUnknown = 0,
	CBCentralManagerStateResetting,
	CBCentralManagerStateUnsupported,
	CBCentralManagerStateUnauthorized,
	CBCentralManagerStatePoweredOff,
	CBCentralManagerStatePoweredOn,
};

//CBCentralManagerDelegate
- (void)centralManagerDidUpdateState:(CBCentralManager *)central {

}
```

CBCentralManagerStateUnauthorized 

标识APP没有蓝牙的使用权限，根据这个状态可以在操作前提示用户开启蓝牙权限

```
注：在调用 [[CBCentralManager alloc] initWithDelegate:self queue:managerQueue] 初始化时，
若app没有蓝牙使用权限系统会给出开启蓝牙使用权限的提示，若用户不允许App使用蓝牙，在之后需
要进行蓝牙操作的时候，App可以根据这个状态主动提示用户开启App的蓝牙权限。
```

CBCentralManagerStatePoweredOff 

标识手机蓝牙未打开，根据这个状态可以根据需要提示用户开启蓝牙

```
注：在调用 [[CBCentralManager alloc] initWithDelegate:self queue:managerQueue] 初始化时，
会自动检测蓝牙的打开状态并给出开启蓝牙的提示，若用户未开启蓝牙，在之后需要进行蓝牙操作的
时候，App可以根据这个状态主动提示用户开启蓝牙。

```

CBCentralManagerStatePoweredOn 

标识蓝牙已开启可以进行蓝牙操作，如果我们需要在蓝牙开启后马上进行搜索，就可以在返回这个状态时，添加搜索蓝牙相关代码

Tip2. 自动连接已连接过的蓝牙设备

原理：蓝牙首次连接成功后记录CBPeripheral实例的identifier，再次连接的时候通过调用 CBCentralManager 的 retrievePeripheralsWithIdentifiers 方法检索，获取对应的CBPeripheral实例进行连接（iOS7+）

Tip3. 数据分包发送

按每包不大于20字节进行分包发送，与[BLE协议栈](http://www.sjsjw.com/kf_other/article/36_18763_5959.asp)的定义有关。
