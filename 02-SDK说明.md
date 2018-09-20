<!-- beta -->
# SDK的文件构成

1. MiCombo_APConnectFramework: MiHome独立SDK的主体.封装了与设备快连,远程操作设备,拉取设备列表,帐号持久化等接口。

2. MiPassport: 小米官方给第三方开发者的基于OAuth2.0的账户登录SDK。

## SDK需要的条件

1. 注册

	在[小米开放平台](http://dev.xiaomi.com/)注册小米账户服务。 取得appid，以及redirectURL。

2. 开通权限

	1、在 “开放接口” 中 开启 “使用您的智能家庭服务“ 权限。
	2、如果是在2018年6月1日后才申请的app，可能没有获取userId的权限，请使用公司邮件发送appid给负责对接的产品经理，开通获取userId的权限。
	

# SDK 集成步骤

1. 本SDK统一使用CocoaPods管理。使用此SDK，请安装[CocoaPods](http://code4app.com/article/cocoapods-install-usage)。
本SDK需要关联的第三方库为：
```
pod 'AFNetworking','3.1.0'
pod 'YYModel'
```
2. 建立自己的工程，把两个framework（MiDeviceFramework，MiPassport）导入。
![导入framework](http://cdn.cnbj0.fds.api.mi-img.com/miio.files/commonfile_png_7b79a19d398bc7062b9947cd97fdcc81.png)

3. 改变工程的编译环境
![编译环境设置](http://cdn.cnbj0.fds.api.mi-img.com/miio.files/commonfile_png_f10d81ab5f1fd060aea93c9d927d1c65.png)

4. 完成上述步骤就可以正式开发了。
