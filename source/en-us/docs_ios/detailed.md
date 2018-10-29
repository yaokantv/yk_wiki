title: SDK流程详解
---
# 1. SDK初始化
## 1.1 申请 AppId
开发者需要先向我公司为自己的App申请一个AppID，SDK启动时需要指定应用程序的AppID。

## 1.2 引用头文件
```
#import <YKCenterSDK/YKCenterSDK.h>
```

## 1.3 初始化SDK
请在应用的AppDelegate中调用该方法指定应用的AppID，该方法只需要调用一次。

注意：只有在初始化完成之后，App才能完成后续的操作。

【示例代码】
```objectivec
[YKCenterSDK registApp:YK_APP_ID completion:^(NSError * _Nonnull error) {
    NSLog(@"YKCenterSDK 初始化成功：%@", error);
}];
[YKCenterSDK disableSDKLog:NO];
```

# 2. 用户管理
用户系统包含了用户的注册、登录、重置密码等功能，以APPID区分用户系统，不同APPID的用户系统相互独立。更换APPID后，需要重新注册用户。



## 2.1.	用户注册
SDK提供三种用户注册方式：手机注册、普通用户注册、邮箱注册。

### 2.1.1 手机注册
通过手机注册账号，需要一个有效的手机号。注册时需要两步操作：获取短信验证码、用短信验证码注册用户。

第一步：APP获取短信验证码时，SDK向云端发送短信验证码请求，如果请求成功，云端会给手机发送短信验证码。

【示例代码】
```objectivec
/**
 发送手机验证码
 */
[YKCenterSDK sendPhoneSMSCode:self.textCell.textInput.text
                           completion:^(NSError * _Nonnull result, NSString * _Nonnull token) {
    // 结果回调处理
}];
```
第二步：用短信验证码注册时，APP把手机收到的短信验证码传给SDK，填上手机号和密码就可以注册了。
【示例代码】
```objectivec
- (void)reg:(NSString *)username // 手机号码
   password:(NSString *)password // 密码
 verifyCode:(NSString *)code     // 第一步获取到的验证码
accountType:(YKUserAccountType)accountType // 值为 YKUserAccountTypePhone
    handler:(void (^__nullable)(NSError *result, NSString *uid, NSString *token))completion;
```
### 2.1.2 普通注册
注册普通用户，使用用户名、密码即可创建一个账号。

【示例代码】
```objectivec
- (void)reg:(NSString *)username
   password:(NSString *)password
    handler:(void (^__nullable)(NSError *result, NSString *uid, NSString *token))completion;
```

### 2.1.3 邮箱注册
通过有效的电子邮箱地址，注册一个账号。注册成功后，云端会给指定邮箱发送注册成功的邮件。

【示例代码】
```objectivec
- (void)reg:(NSString *)username // 邮箱地址
   password:(NSString *)password // 密码
 verifyCode:(NSString *)code     // 为空
accountType:(YKUserAccountType)accountType // 值为 YKUserAccountTypeEmail
    handler:(void (^__nullable)(NSError *result, NSString *uid, NSString *token))completion;
```

## 2.2 用户登录
SDK 提供三种登录方式：普通用户登录、手机/邮箱（手机邮箱用同一种登录方式）登录、匿名登录。

### 2.2.1 普通用户登录
通过普通注册成功的用户，可以通过普通用户登录来登录我们SDK。

【示例代码】
```objectivec
[[YKCenterSDK sharedInstance] login:username
                                   password:password
                                    handler:^(NSError * _Nonnull result, NSString * _Nonnull uid, NSString * _Nonnull token)
         {
    // 结果回调处理
}
```
### 2.2.2 手机/邮箱登录
通过手机/邮箱注册成功的用户，可以通过手机/邮箱登录来登录我们SDK。

【示例代码】
```objectivec
[[YKCenterSDK sharedInstance] login:username
                                   password:password
         accountType:(YKUserAccountTypePhone)
                                    handler:^(NSError * _Nonnull result, NSString * _Nonnull uid, NSString * _Nonnull token)
         {
    // 结果回调处理
}
```

### 2.2.3 匿名登录
用户每次匿名登录时，获取到的uid是相同的。SDK使用设备UUID生成登录账号。每台设备系统都有一个独立的UUID，系统刷机或重置后将改变。因此匿名登录的用户信息有可能因无法保留而丢失。

【示例代码】
```objectivec
[[YKCenterSDK sharedInstance] anonymousLogin:^(NSError * _Nonnull result, NSString * _Nonnull uid, NSString * _Nonnull token) {
    // 结果回调处理
}
```

## 2.3 重置密码
如果忘记了用户密码，可以通过手机验证码或邮箱设置新的密码。SDK支持手机号重置密码和邮箱重置密码两种，手机号重置需要接收验证码，邮箱重置需要进⼊邮箱，根据链接提示进行重置。（注意：暂时不支持普通用户重置密码！）
### 2.3.1 手机号重置密码
手机号重置密码时，需要先获取短信验证码再重置。获取短信验证码方式与手机注册时相同。

第一步：获取短信验证码（与注册时相同，这里不再赘述）

第二步：用短信验证码重置密码

【示例代码】
```objectivec
[YKCenterSDK resetPassword:self.phoneCell.textInput.text
                    verifyCode:self.verfiyCell.textInput.text
                   newPassword:self.passwordCell.textPassword.text
                   accountType:YKUserAccountTypePhone
                    completion:^(NSError * _Nullable result)
     {
    // 结果回调处理
}
```
### 2.3.2 邮箱重置密码
邮箱重置密码时，云端会给指定邮箱发送安全链接。用户需要到邮箱中查收邮件，并按邮件指示执行重置操作。重置密码邮件有可能进入用户的邮箱的垃圾箱中，需提醒用户。

邮件发送成功回调与密码修改成功回调一致，因此需要注意在回调的时候区分。

【示例代码】
```objectivec
[YKCenterSDK resetPassword:self.phoneCell.textInput.text
                    verifyCode:nil
                   newPassword:self.passwordCell.textPassword.text
                   accountType:YKUserAccountTypeEmail
                    completion:^(NSError * _Nullable result)
     {
    // 结果回调处理
}
```

# 3. 遥控中心设备管理
控制设备前，需要先让设备连到路由器上。配置遥控中心入网时，路由器必须接入外网，设备会自动注册到SDK。

在开始配置前，设备要先进入配置模式，然后APP调用配置接口发送要配置的路由器ssid和密码。设备配置成功后，SDK给APP返回已配置成功的设备mac地址和产品类型标识，便于APP做下一步的操作。如果设备是重置后进入的配置模式，如果配置成功时设备还来不及从云端获取到DID，则APP得到的DID为空。

SDK的设备配置接口如果超时时间还未结束，无法进行下一次配置。此外，因为设备配置成功的广播包只有APP连到同一路由上才能收取，因此这个超时时间应该预留出APP连接路由器的时间。

需要注意的是，如果配置上线的设备不是APP要获取的产品类型，该设备就不会出现在设备列表中。

配置使⽤UDP广播方式，由手机端发出含有目标路由器名称和密码的广播，设备上的Wifi模块接收到广播包后自动连接目标路由器，连上路由器后发出配置成功广播，通知手机配置已完成。

注意：目前仅支持 **非 5G 无线路由器** 配置。

【示例代码】
```objectivec
[YKCenterSDK bindYKCWithSSID:dataCommon.ssid
                            password:key
                          completion:^(NSError * _Nonnull result)
         {
    // 结果回调处理
}
```
## 3.1 遥控中心设备发现
APP设置好委托，启动SDK后，就可以收到SDK的设备列表推送。每次局域网设备或者用户绑定设备发生变化时，SDK都会主动上报最新的设备列表。设备断电再上电、有新设备上线等都会触发设备列表发生变化。用户登录后，SDK会主动把用户已绑定的设备列表上报给APP，绑定设备在不同的手机上登录帐号都可获取到。

如果APP想要刷新绑定设备列表，可以调用绑定设备列表接口，同时可以指定自己关心的产品类型标识，SDK会把筛选后的设备列表返回给APP。

SDK提供设备列表缓存，设备列表中的设备对象在整个APP生命周期中一直有效。缓存的设备列表会与当前最新的已发现设备同步更新。

【示例代码】

```objectivec
[YKCenterSDK fetchBoundYKC:^(NSArray<GizWifiDevice *> * _Nonnull devices, NSError *error) {
}
```

## 3.2 订阅和绑定
APP得到设备列表后，给设备设置委托后，可以订阅设备。已订阅的设备将被自动绑定和自动登录，设备登录成功后会主动上报最新状态。

自动绑定仅限于局域网设备。对于无法在局域网内发现的设备，APP可以通过手动绑定的方式完成绑定。绑定成功的设备，需要订阅后才能使用。

无论是手动绑定还是自动绑定，设备的remark和alias信息，都需要在设备绑定成功后再设置。

解除订阅的设备，连接会被断开，不能再继续下发控制指令了。

### 3.2.1 订阅
所有通过SDK得到的设备，都可以订阅，订阅结果通过回调返回。订阅成功的设备，要在其网络状态变为可控时才能查询状态和下发控制指令。

【示例代码】

```objectivec
[YKCenterSDK subscribeDevice:dev completion:^(GizWifiDevice * _Nonnull device) {
        // 直接订阅会自动绑定
        if (device.isSubscribed) {
            [[YKCenterCommon sharedInstance] setCurrentYKCId:dev.macAddress];
            [weakSelf performSegueWithIdentifier:@"YKRemoteList" sender:nil];
        }
    }];
```
# 3.2.2 绑定
APP可以通过手动绑定的方式完成绑定。绑定成功的设备，需要订阅后才能使用。

【示例代码】

```objectivec
[YKCenterSDK bindDevice:dev completion:^(NSString * _Nonnull did) {
    // 绑定
    if (did) {
        [YKCenterSDK subscribeDevice:dev completion:^(GizWifiDevice * _Nonnull device) {
            // 订阅
            if (device.isSubscribed) {
                [[YKCenterCommon sharedInstance] setCurrentYKCId:dev.macAddress];
                [weakSelf performSegueWithIdentifier:@"YKRemoteList" sender:nil];
            }
        }];
    }
}];
```

## 3.3 解绑
已绑定的设备可以解绑，解绑需要APP调用接口完成操作，SDK不支持自动解绑。对于已订阅的设备，解绑成功时会被解除订阅，同时断开设备连接，设备状态也不会再主动上报了。设备解绑后，APP刷新绑定设备列表时就得不到该设备了。

【示例代码】

```objectivec
[YKCenterSDK unbindYKC:dev completion:^(NSError * _Nonnull error) {
    // 结果回调处理
}];
```

# 4. 遥控器管理
遥控器创建流程：

选择遥控器类型->选择品牌->获取匹配数据进行遥控匹配->创建遥控器

## 4.1 获取遥控器类型
获取所有遥控器类型，比如电视机，空调，机顶盒，电风扇等。

【示例代码】

```objc
__weak __typeof(self)weakSelf = self;
[YKCenterSDK fetchRemoteDeviceTypeWithYKCId:[[YKCenterCommon sharedInstance] currentYKCId]
                                 completion:^(NSArray<YKRemoteDeviceType *> * _Nonnull types,
                                              NSError * _Nonnull error)
 {
     __strong __typeof(weakSelf)strongSelf = weakSelf;
     strongSelf.typeList = types;
     [strongSelf.tableView reloadData];
 }];
```

## 4.2 获取品牌
根据遥控器类型ID去获取该分类下的遥控器品牌列表。

【示例代码】

```objc
__weak __typeof(self)weakSelf = self;
[YKCenterSDK fetchRemoteDeviceBrandWithYKCId:[[YKCenterCommon sharedInstance] currentYKCId]
                          remoteDeviceTypeId:self.deviceType.tid.integerValue
                                 completion:^(NSArray<YKRemoteDeviceBrand *> * _Nonnull brands,
                                              NSError * _Nonnull error)
{
     __strong __typeof(weakSelf)strongSelf = weakSelf;
     strongSelf.brandList = brands;
     [strongSelf.tableView reloadData];
}];
```

## 4.3 获取匹配数据
匹配数据是由服务端定义好的遥控器按键，方便进行快速测试匹配。

【示例代码】
```objc
[YKCenterSDK fetchMatchRemoteDeviceWithYKCId:[[YKCenterCommon sharedInstance] currentYKCId]
                          remoteDeviceTypeId:self.deviceType.tid.integerValue
                         remoteDeviceBrandId:self.deviceBrand.bid
                                  completion:^(NSArray<YKRemoteMatchDevice *> * _Nonnull mathes, NSError * _Nonnull error)
 {
     __strong __typeof(weakSelf)strongSelf = weakSelf;
     strongSelf.matchList = mathes;
     [strongSelf.tableView reloadData];
 }];
```

## 4.4 创建遥控器
匹配成功后，通过遥控器ID下载整套遥控码创建遥控器。

【示例代码】
```objc
__weak __typeof(self)weakSelf = self;
[YKCenterSDK fetchRemoteDeivceWithYKCId:[[YKCenterCommon sharedInstance] currentYKCId]
                         remoteDeviceId:self.matchDevice.rid
                             completion:^(YKRemoteDevice * _Nonnull remote, NSError * _Nonnull error)
 {
     if (error == nil) {

     }
 }];
```

## 4.5 删除遥控器

【示例代码】
```objc
YKRemoteDevice *device = self.remotes[indexPath.row];
if ([device remove]) {
    NSLog(@"删除成功");
} else {
    NSLog(@"删除失败");
}
```

# 5. 遥控码库管理

## 5.1 发码遥控

【示例代码】
```objc
YKRemoteDeviceKey *key = self.remote.keys[indexPath.row];

[YKCenterSDK sendRemoteWithYKCId:[[YKCenterCommon sharedInstance] currentYKCId]
                           datas:@[key]
                      completion:^(id  _Nonnull result, NSError * _Nonnull error) {
    // 发码结果回调
}];
```

## 5.2 红外码学习
调用学码方法后，遥控中心设备会进入快闪的学习模式，在超时自动退出之前学到码会通过回调 block 返回学习到的码，如果失败则为空。

【示例代码】
```objc
- (IBAction)learnCode:(id)sender {
    __weak __typeof(self)weakSelf = self;
    [YKCenterSDK learnCodeWithYKCId:[[YKCenterCommon sharedInstance] currentYKCId]
                         completion:^(BOOL result, NSString * _Nullable code)
     {
         NSLog(@"result:%ld, code:%@", (long)result, code);
     }];
}
```

## 5.3 退出学习
发送此命令后，小苹果会马上退出学习状态（LED灯退出快闪状态）。

【示例代码】
```objc
[YKCenterSDK stopLearnCode:[[YKCenterCommon sharedInstance] currentYKCId]];
```

## 5.4 数据透传（小苹果2代）
### 数据发送
数据长度不能大于844。

【示例代码】
```objc
- (IBAction)sendTrunk:(id)sender {
    [YKCenterSDK sendTrunkWithYKCId:[[YKCenterCommon sharedInstance] currentYKCId]
                               data:[self.inputField.text dataUsingEncoding:NSUTF8StringEncoding]
                         completion:^(id  _Nonnull result, NSError * _Nonnull error)
     {
         NSLog(@"result:%@, error:%@", result, error);
     }];
}
```

### 数据接收
在接收数据之前需要设置一个接收数据的回调接口。

【示例代码】
```objc
- (void)viewDidAppear:(BOOL)animated {
    [super viewDidAppear:animated];
    
    __weak __typeof(self)weakSelf = self;
    [YKCenterSDK registerReceiveTrunkDataHandler:^(NSData * _Nonnull trunkData) {
        [weakSelf updateTextViewWithData:trunkData];
    }];
}

- (void)viewDidDisappear:(BOOL)animated {
    [super viewDidDisappear:animated];
    
    // unregister
    [YKCenterSDK registerReceiveTrunkDataHandler:nil];
}

```