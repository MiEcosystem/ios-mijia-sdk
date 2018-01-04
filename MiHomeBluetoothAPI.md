# MiBluetoothFramework 使用文档

## 1 . Framework 构成

蓝牙设备根据绑定类型,分为不绑定,弱绑定,强绑定三种类型.

不绑定类型的framework构成:MiBluetoothFramework.framework

弱绑定,强绑定类型的framework构成: MiBluetoothFramework.framework ,

MiPassport.framework,MiPassport.bundle

## 2 .工程配置
如果是弱绑定,强绑定设备,那么需要网络操作,进行绑定,所以需要工程关联网络模块.本Framework

采用AFNetworking作为网络基础库.统一采用pod管理.

1 . podfile

```
project 'MiSmartHomeBluetoothDemo.xcodeproj'
target :MiSmartHomeBluetoothDemo do
	pod 'AFNetworking','~>3.1.0'
end
```

2 . 工程配置
工程需要按照以下图进行配置
![bitcode](http://7xrfz8.com1.z0.glb.clouddn.com/QQ20161021-0.png)


![other linker Flags](http://7xrfz8.com1.z0.glb.clouddn.com/QQ20161021-1.png)

![compile source as](http://7xrfz8.com1.z0.glb.clouddn.com/QQ20161021-2.png)

## 3 .接口说明

1 . 扫描设备

  MHBluetoothDiscovery 这个类主要的功能就是扫描普通的蓝牙设备.只是打开蓝牙的设备,都可以
  扫描出来.

```objc
[_bluetoothDiscovery startSearch:^(NSArray<MHBluetoothDevice *> *bluetoothDevices,NSError* error) {
    if (error.code == 0) {
        weakSelf.bluetoothDevice = [bluetoothDevices mutableCopy];
        [weakSelf.tableView reloadData];
    }else if(error.code == CBManagerStatePoweredOff){
    	   NSLog(@"请打开蓝牙");
  	}
}];
```


2 . 连接设备

MHXiaoMiConnectManager 这个类的主要功能是实现符合小米协议的蓝牙设备的连接操作.如果需要反复链接的话，可以保存MHXiaoMiConnectManager的实例。

```objc
MHBluetoothDevice* device = _bluetoothDevice[indexPath.row];

NSData* token = [[NSUserDefaults standardUserDefaults] valueForKey:@"loginToken"];
if ([token length]) {
	if (device) {
    	MHXiaoMiConnectManager* connectManager = [[MHXiaoMiConnectManager alloc] init];
    	[connectManager loginXiaoMiBluetoothDeviceWith:device loginToken:token
                          miDevice:^(MHXiaoMiBluetoothDevice * _Nullable bleDevice) {
      		if (bleDevice) {
        		NSLog(@"成功login符合小米协议的设备");
      		}else{
        		NSLog(@"login 失败");
      		}
    	}];
	}
}else{
  	if (device) {
    	MHXiaoMiConnectManager* connectManager = [[MHXiaoMiConnectManager alloc] init];
    	[connectManager registerXiaoMiBluetoothDeviceWith:device miDevice:^(MHXiaoMiBluetoothDevice *bleDevice) {
      		if (bleDevice) {
        		NSLog(@"成功注册一个符合小米协议的设备");
        		//存储一下logintoken  300c84ca 3a261223 446376c0
        		NSLog(@"loginToken = %@",bleDevice.loginToken);
        		[[NSUserDefaults standardUserDefaults] setValue:bleDevice.loginToken forKey:@"loginToken"];
        		[[NSUserDefaults standardUserDefaults] synchronize];
      		}else{
        		NSLog(@"register 失败");
      		}
    	}];
  	}
}
```
当不在需要MHXiaoMiConnectManager 连接后，请调用 MHXiaoMiConnectManager的disconnect的方法，释放对象。

如果想了解蓝牙连接设备的连接情况，可以调用 MHXiaoMiConnectManager 的 registerCentralManagerDelegate方法，并且实现CBCentralManagerDelegate 定义的方法，就可以知道在蓝牙连接过程中 连接是否断开。

3 .蓝牙设备生成

MHBluetoothDeviceFactory 可以通过传入package生成一个MHBluetoothDevice .

MHBluetoothBroadcastPackage 通过传入的最原始的蓝牙数据生成一个package.
原始数据是通过NSDictionary 传入,对应的key-value

```objc
data[@"centralManager"] = centralManager;
data[@"peripheral"] = peripheral;
data[@"advertisementData"] = advertisementData;
data[@"RSSI"] = RSSI
```

4 .私人定制

如果不需要我们的sdk提你扫描蓝牙设备,可以通过MHBluetoothDeviceFactory传入package生成一个MHBluetoothDevice,然后传给MHXiaoMiConnectManager,让我们的sdk替你实现连接即可.

##Demo
MiSmartHomeBluetoothDemo 包括两种连接，一种是批量链接设备，一种是单点链接某个设备。
1、批量链接设备的代码见bluetoothController.m的beginLoop 方法。

2、单点链接的代码见bluetoothController.m的tableview:didSelectRowAtIndexPath: 方法

