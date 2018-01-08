# SDK 入门指南
## SDK的文件构成

1. MiDeviceFramework: MiHome独立SDK的主体.封装了与设备快连,远程操作设备,拉取设备列表,帐号持久化等接口。

2. MiPassport: 小米官方给第三方开发者的基于OAuth2.0的账户登录SDK。

## SDK需要的条件

1. 注册

在[小米开放平台](http://dev.xiaomi.com/docs/passport/user_guide/)
取得appid，以及redirectURL。


# SDK 集成步骤

1. 本SDK统一使用CocoaPods管理。使用此SDK，请安装[CocoaPods](http://code4app.com/article/cocoapods-install-usage)。
本SDK需要关联的第三方库为：
```
pod 'AFNetworking','3.1.0'
pod 'YYModel'
```
2. 建立自己的工程，把两个framework（MiDeviceFramework，MiPassport）导入。
![导入framework](http://7xrfz8.com1.z0.glb.clouddn.com/%E6%AD%A5%E9%AA%A4%E4%B8%80.png)

3. 改变工程的编译环境
![编译环境设置](http://7xrfz8.com1.z0.glb.clouddn.com/%E6%AD%A5%E9%AA%A4%E4%BA%8C.png)

4. 完成上述步骤就可以正式开发了。

# API 简介

1. 设备操作相关接口。

1.1 MHDevice.h

设备的基本信息。简单的理解为设备的基础数据结构

1.2 MHDevices.h

设备的一个集合。

1.3 MHDeviceManager.h

设备操作类。包含了拉取设备列表，获取新接入的设备，绑定设备，远程操作设备等接口。

1.3.1 获取设备列表


-(void)fetchDeviceListWithFilters:(NSArray<NSString*>*)dids
DeviceListBlock:(void(^)(MHDevices* deviceList))deviceList
failure:(void(^)(NSError* error))failure

param： dids，需要获取的设备属性的列表。如果想要获取指定设备的属性，
需要传入。如果默认拉取账户下所有的设备，只需要传入nil。

deviceListBlock：请求成功之后返回的设备列表。

failure：请求失败返回的信息。




1.3.2 获取快连新接入的设备

-(void)fetchNewDeviceWith:(NSString*)ssid
withBssid:(NSString*)bssid
DeviceListBlock:(void(^)(MHDevices* deviceList))deviceList
failure:(void(^)(NSError* error))failure;

param： ssid，路由器的ssid

bssid，路由器的mac地址。设备需要跟路由器绑定。
有时需要返回整个局域网的设备。

devicelistBlock：同上。

failure：同上。



1.3.3 操作设备

-(void)callDeviceMethod:(MHDevice*) device
method:(NSString *)method
params:(id)params
sucess:(void(^)(id result))sucess
failure:(void(^)(NSError* error))failure;


param： device，需要操作的设备。
method，具体的操作方法。
params，操作需要传入的参数。
sucess，操作成功的回调。
failure，操作失败的回调。

1.4 MHDeviceSmartConfig.h 设备快连的类

-(void)startAPSmartConfigDeviceIp:(NSString*)deviceIp
WithSSID:(NSString*)ssid
WithBSSID:(NSString*)bssid
password:(NSString*)password
userId:(NSString*)userId
domain:(NSString*)domain
progressBlock:(void(^)(kSmartConfigState state,
kSartConfigResult result,BOOL* stop))progressBlock;

param： deviceip，设备的ip地址。(demo中有获取设备ip的code)
ssid：  路由器的ssid。
bssid： 路由器的mac地址。（demo中有获取路由器mac地址的code）
password:路由器的密码。
userid：小米帐号的id。
domain：用户所在的区域。
progressBlock:每步快连后的回调，每一步快连后会通知快练当前的过程
以及成功的状态，以及是否进行下一步。
默认走完四步的话，整个快连就是一个完整的过程。

2. 帐号网络相关接口

2.1 MHAccount.h
在MiPassport的基础上再次封装了一层，包括数据的持久化。
具体接口见接口API。

2.2 MHRequestSerializer.h

2.2.1 @protocol MHRequestConfig <NSObject>
这个protocol的作用是，如果一些需要自定义的参数需要在网络请求的时候加入的，需要一个comform这个
protocol的实例。在这个里面你可以做任何事情，比如说把参数加密，或者在原有的参数的基础上增加某些
参数。

2.2.2 MHRequestBodySerializer
一个具体的实例。是把一些帐号信息加入到参数中

2.2.3 MHRequestCookieSerializer
一个具体的实例。把帐号信息设置成cookie。

2.3 MHNetworkEngine.h
SDK的网络请求。

+(void)callRemoteApi:(MHBaseRequest*)request
httpMethod:(NSString*)method
sucess:(void(^)(MHBaseRequest* request,id result))sucess
failure:(void(^)(NSError* error))failure;

自己继承MHBaseRequest,写一个自己的网络请求。然后发给小米的后台。前提
是你自己知道自己该调用哪个接口。

param：request,自己重写的request。
method,现在只支持GET。
sucess，成功的返回。
failure，失败后的返回。

# API 使用
## App开发
App开发分四部分：
1. 初始化帐号系统
2. 快联设备
3. 获取设备列表
4. 操作设备

### 初始化帐号系统
SDK需要登陆后才能操作设备，所以APP开发必须申请API和取得appid，以及redirectURL。当申请得到appid和redirectUrl后，在AppDelegate的application:didFinishLaunchingWithOptions: 方法中初始化账户

```objc
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
_account = [[MHAccount alloc] initWithAppId:
@"179887661252608"
redirectUrl:@"http://xiaomi.com"];
return YES;
}
```


### 快连设备（如果已经快连，此步略过）
APP要控制设备，需要先进行快联设备，告诉设备wifi 和对应的密码。设备快联成功后就可以操作设备了。(具体代码可以见Demo 程序中的 MHScanViewController.m 文件)
```objc
- (void)authButtonClick:(id)sender{
_smartConfig = [[MHDeviceSmartConfig alloc] init];
[_smartConfig startAPSmartConfigDeviceIp:[self getRouterIp]
WithSSID:@"Banana"
WithBSSID:_bssid
password:@"helloworld"
userId:_profile.userId domain:@"cn"
progressBlock:^(kSmartConfigState state,
kSartConfigResult result, BOOL *stop) {
NSLog(@"state = %d,result = %d",state,result);
}];
}
```

### 获取设备列表
快联成后，就可以拉取设备列表，得到对应的Device。
```objc
MHDeviceManager* manager = [MHDeviceManager new];
[manager fetchDeviceListWithFilters:nil
DeviceListBlock:^(NSArray<MHDevice *> *devices) {
NSLog(@"%@",devices);
} failure:^(NSError *error) {
NSLog(@"%@",error);
}];
```

### 操作设备
获得设备之后，可以发对应的指令来操作，假设操作设备为一个插座，代码类似如下：

```objc
NSString* method = @"set_power";
id params = nil;
if (_isOn) {
[self.oprationBtn setTitle:@"ON"
forState:UIControlStateNormal];
params = @[@"off"];
}else{
[self.oprationBtn setTitle:@"OFF"
forState:UIControlStateNormal];
params = @[@"on"];
}
_isOn = !_isOn;
[_deviceManager callDeviceMethod:_device
method:method params:params
sucess:^(id result) {
NSLog(@"%@",result);
} failure:^(NSError *error) {
NSLog(@"%@",error);
}];
```
更多的操作见 MiOprationController.m

# Today Extension 开发
如果想开发iOS的today Extension。 设备访问的 API跟App 一样，但是widget没有登陆界面，所以watch和app共享账户信息。不然网络请求会发送失败

## 主app 登陆后，需要保存共享信息，代码类似如下（具体代码参见 ViewController.m 的 fecthDatas 方法）
```objc
- (void)setWidgetShareData{
NSUserDefaults *shared = [[NSUserDefaults alloc] initWithSuiteName:@"group.mismarthome.1"];
NSDictionary* dict = [MHPassport accountLoginParameters];
[shared setObject:dict forKey:@"group.mismarthome.1.dict"];
[shared synchronize];
}
```

## Today Extension 发送网络请求

首先自定义MHRequestSerializer 的子类。
```objc
@interface MHPGHPassportSerializer : MHRequestSerializer
@property (nonatomic, copy) NSString *locale;
@end

@implementation MHPGHPassportSerializer
-(NSDictionary*)configHttpRequest:(NSDictionary*)parameters{
NSUserDefaults *shared = [[NSUserDefaults alloc] initWithSuiteName:@"group.mismarthome.1"];

NSDictionary* dict = [shared objectForKey:@"group.mismarthome.1.dict"];
NSMutableDictionary* mDict = [[NSMutableDictionary alloc] initWithCapacity:10];
if(dict){
[mDict addEntriesFromDictionary:dict];
}
if(parameters){
[mDict addEntriesFromDictionary:parameters];
}

return mDict;
}
```
当today Extension 发送请求的时候，设置自定义的Serializer

```objc
_deviceManager = [MHDeviceManager new];
_deviceManager.serializer = [MHPGHPassportSerializer serializer];
[_deviceManager fetchDeviceListWithFilters:nil DeviceListBlock:^(MHDevices* deviceList) {
_devices = [deviceList.devices copy];
[self sendRPC];
} failure:^(NSError *error) {
NSLog(@"%@",error);
}];
```


# 国际化
如果要做国际化的请求，需要自己设置调用MHBaseRequest的 setupBaseRequestUrl:的方法设置对应的请求服务器。

默认为中国的地址（https://openapp.io.mi.com/openapp），不需要设置

如果要请求美国的地址，徐需要在openapp前面加us。请求url变为
https://us.openapp.io.mi.com/openapp

```objc
[MHBaseRequest setupBaseRequestUrl:@"https://us.openapp.io.mi.com/openapp"]
```





