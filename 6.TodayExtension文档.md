<!-- beta -->
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


