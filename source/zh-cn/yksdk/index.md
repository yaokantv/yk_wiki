# 1. SDK初始化
## 1.1 申请 AppId
开发者需要先联系我们公司商务申请一个AppID，SDK启动时需要指定应用程序的AppID。

## 1.2 引用头文件
```
#import <YKSDK/YKSDK.h>
```

## 1.3 初始化SDK
请在应用的AppDelegate中调用该方法指定应用的AppID，该方法只需要调用一次。

注意：只有在初始化完成之后，App才能完成后续的操作。

【示例代码】
```objectivec
[YKSDK disableSDKLog:NO];
// 可以放到APP里面任何一个地方初始化，根据你们需求而定
[YKSDK registApp:YK_APP_ID completion:^(NSError * _Nonnull error) {
    NSLog(@"YKSDK 初始化结果：%@", error);
    // 此回调成功后，再去使用SDK里面的功能
}];
```

# 2. 遥控器管理
遥控器创建流程：

选择遥控器类型->选择品牌->获取匹配数据进行遥控匹配->创建遥控器

## 2.1 获取遥控器类型
获取所有遥控器类型，比如电视机，空调，机顶盒，电风扇等。

【示例代码】

```objc
__weak __typeof(self)weakSelf = self;
[YKSDK fetchRemoteDeviceTypeWithYKCId:[[YKCommon sharedInstance] currentYKCId]
                                 completion:^(NSArray<YKRemoteDeviceType *> * _Nonnull types,
                                              NSError * _Nonnull error)
 {
     __strong __typeof(weakSelf)strongSelf = weakSelf;
     strongSelf.typeList = types;
     [strongSelf.tableView reloadData];
 }];
```

## 2.2 获取品牌
根据遥控器类型ID去获取该分类下的遥控器品牌列表。

【示例代码】

```objc
__weak __typeof(self)weakSelf = self;
[YKSDK fetchRemoteDeviceBrandWithYKCId:[[YKCenterCommon sharedInstance] currentYKCId]
                          remoteDeviceTypeId:self.deviceType.tid.integerValue
                                 completion:^(NSArray<YKRemoteDeviceBrand *> * _Nonnull brands,
                                              NSError * _Nonnull error)
{
     __strong __typeof(weakSelf)strongSelf = weakSelf;
     strongSelf.brandList = brands;
     [strongSelf.tableView reloadData];
}];
```

## 2.3 获取匹配数据
匹配数据是由服务端定义好的遥控器按键，方便进行快速测试匹配。

【示例代码】
```objc
[YKSDK fetchMatchRemoteDeviceWithYKCId:[[YKCenterCommon sharedInstance] currentYKCId]
                          remoteDeviceTypeId:self.deviceType.tid.integerValue
                         remoteDeviceBrandId:self.deviceBrand.bid
                                  completion:^(NSArray<YKRemoteMatchDevice *> * _Nonnull mathes, NSError * _Nonnull error)
 {
     __strong __typeof(weakSelf)strongSelf = weakSelf;
     strongSelf.matchList = mathes;
     [strongSelf.tableView reloadData];
 }];
```

## 2.4 一键匹配
遥控器一键匹配的本质是一个将学习到的红外码上传到云端服务器匹配的过程，该过程分为一下几个步骤：

step1：根据遥控器的类型选择匹配的按键

【示例代码】
```objc
if (self.deviceType.tid.integerValue == kDeviceACType) {
        self.matchKey = @"on";
    }
    else if (self.deviceType.tid.integerValue == kDeviceCarAudioType) {
        self.matchKey = @"ok";
    }
    else {
        self.matchKey = @"power";
    }
```

step2：学习成功后，调用一键匹配接口将红外码值上传到云端

【示例代码】
```objc
[YKSDK oneKeyMatchWithYKCId:[[YKCommon sharedInstance] currentYKCId]
                   beRemoteType:self.deviceType.tid.integerValue
                        brandId:self.deviceBrand.bid
                        cmd_key:self.matchKey
                      cmd_value:cmd
                     completion:^(id _Nonnull result, NSError * _Nonnull error)
     {
         NSLog(@"result = %@, error=%@", result, error);
     }];
```

step3：解析回调，有以下几种情况需要分别处理：

  1. 如果结果包含`next_cmp_key`时，则说明要继续匹配下一个键，重复step2操作。

  2. 如果结果是数组并且数量大于1，则说明匹配到数据，则说明匹配回来的是空调遥控器数组(一般匹配空调都会以数组的形式返回)，数组里面包含遥控器的几个按键，这时你可以用来测试实体家电，测试成功之后再下载完整的遥控器数据。

  3. 如果结果是数组并且数量等于1，则说明匹配成功，将此遥控器保存起来即可。
  4. 除以上3中情况，其他情况均为匹配失败。

## 2.5 创建遥控器
匹配成功后，通过遥控器ID下载整套遥控码创建遥控器。

【示例代码】
```objc
__weak __typeof(self)weakSelf = self;
[YKSDK fetchRemoteDeivceWithYKCId:[[YKCenterCommon sharedInstance] currentYKCId]
                         remoteDeviceId:self.matchDevice.rid
                             completion:^(YKRemoteDevice * _Nonnull remote, NSError * _Nonnull error)
 {
     if (error == nil) {

     }
 }];
```

## 2.6 删除遥控器

【示例代码】
```objc
YKRemoteDevice *device = self.remotes[indexPath.row];
if ([device remove]) {
    NSLog(@"删除成功");
} else {
    NSLog(@"删除失败");
}
```
