设备类的说明及常用API说明。

常用设备类
### 1. MHDevice.h

设备的基本信息。简单的理解为设备的基础数据结构

### 2. MHDevices.h

设备的一个集合。

### 3 MHDeviceManager.h

设备操作类。包含了拉取设备列表，获取新接入的设备，绑定设备，远程操作设备等接口。常见的API如下：

#### 3.1 获取设备列表 （WiFi和蓝牙设备都可以获取）

```objc
-(void)fetchDeviceListWithFilters:(NSArray<NSString*>*)dids
DeviceListBlock:(void(^)(MHDevices* deviceList))deviceList
failure:(void(^)(NSError* error))failure
```

param： dids，需要获取的设备属性的列表。如果想要获取指定设备的属性，
需要传入。如果默认拉取账户下所有的设备（包括蓝牙设备和WiFi设备），只需要传入nil。

deviceListBlock：请求成功之后返回的设备列表。

failure：请求失败返回的信息。


#### 3.2 获取快连新接入的设备（WiFi设备或者Combo设备）
```objc
-(void)fetchNewDeviceWith:(NSString*)ssid
                withBssid:(NSString*)bssid
                DeviceListBlock:(void(^)(MHDevices* deviceList))deviceList
                failure:(void(^)(NSError* error))failure;
```
param： ssid，路由器的ssid

bssid，路由器的mac地址。设备需要跟路由器绑定。
有时需要返回整个局域网的设备。

devicelistBlock：同上。

failure：同上。

#### 3.3 操作设备（只用于WiFi设备）
```objc
-(void)callDeviceMethod:(MHDevice*) device
				method:(NSString *)method
				params:(id)params
				sucess:(void(^)(id result))sucess
				failure:(void(^)(NSError* error))failure;
```

param： device，需要操作的设备。
method，具体的操作方法。
params，操作需要传入的参数。
sucess，操作成功的回调。
failure，操作失败的回调。



3.4 设备分享 

```objc
-(void)shareDevice:(NSArray<MHDevice*>*)devices 
    		toUser:(NSString*)userId 
          withType:(NSString*)type 
            sucess:(void(^)(NSError* error))sucessBlock 
           failure:(void(^)(NSError* error))failureBlock;
```

devices： 分享的设备

userId： 小米账户ID。小米账户ID可以通过getUserProfile 来获得

type: 分享类型:@""为可控制 @"readonly"为只读 

 

