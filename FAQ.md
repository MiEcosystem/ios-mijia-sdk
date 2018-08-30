# 常见问题
1. 分享是通过 -(void)shareDevice:(NSArray<MHDevice*>*)devices toUser:(NSString *)userId withType:(NSString *)type sucess:(void (^)(NSError *))sucessBlock failure:(void (^)(NSError *))failureBlock 来分享的，但是只接受小米id的方式，如果拿到用户小米id，可以通过 getUserProfile 方法来获取用户信息再分享。

2. combo快联是集合了蓝牙和WiFi。所以它有两个mac地址。所以combo 快联设备不能使用mac地址来做key，因为快联前拿到的是蓝牙的mac地址，但是成功后获取的是WiFi 模块的mac地址。两者不一样。

3. 如果无法快联设备，可以尝试一下办法
```
3.1 如果设备还没有发布，检查账户是否在白名单内，不在白名单内的用户无法快联。

3.2 如果账户在白名单内，使用Demo 程序快联。看是否成功。 如果能成功，检查一下代码与Demo中有什么不一样的地方。

3.3 如果Demo 快联失败，使用米家App 快联一下。如果米家也不能成功，检查一下固件的实现。应该是固件实现的有问题。
```


4. SDK只支持小米账户登录的方式，不支持第三方的账户登录方式。使用第三方登录方式会导致SDK一些功能无法使用。比如设备无法拉取，无法控制设备。