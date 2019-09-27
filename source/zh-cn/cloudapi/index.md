Yaokan 酒店网关云 API 接口文档
==============================

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
文件编号：YAOKANHOTELAPI-20190920
版本：v1.0

深圳遥看科技有限公司
（版权所有，切勿拷贝）
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


1. 概述
-------

使用遥控大师用户系统开发者，可通过本文档接口管理设备授权、获取设备上创建的遥控器列表、云语音控制设备功能。

2. 文档阅读对象
---------------

酒店系统开发者

3. 接口规范
-----------

### 3.1. 请求头

| 名称         | 类型   | 是否必须 | 示例                              | 说明                 |
|--------------|--------|----------|-----------------------------------|----------------------|
| appId        | string | yes      | appid                             | 平台分配的APPID      |
| timestamp    | int    | yes      | timestamp                         | 时间戳               |
| signature    | string | yes      | signature                         | 签名                 |

### 3.2. 签名生成算法

将appId、timestamp拼接成字符串A，然后对A做MD5加密获得字符串B，然后取B的第1，3，7，15，31位置上的字符，拼接成C，C即为签名的值

PHP示例:

```php
$appId="94d3b83bd9f00589acac31520664993e"
$timestamp=1567482133

//拼接签名参数字符串

$signStr=($appId.$timestamp)="94d3b83bd9f00589acac31520664993e1567482275"

//签名字符串做MD5

$B=md5($signStr)="dd46a0efcbb5b125e96065d651ecf86c"

//取MD5后指定位上的内容

$C=$B{1}+$B{3}+$B{7}+$B{15}+$B{31}=d6f5c
```

### 3.3. 请求方式:POST

### 3.4. 返回格式:JSON，并且data数据做加密

### 3.5. 请求参数data的加密：

3.5.1.AES method:"AES-128-CBC"

3.5.2.处理步骤:
data体做json编码后，再做AES加密，最后做BASE64编码

其中，AES的key为appSecret的高16位，AES的偏移量为appSecret的低16位；

3.5.3.AES加密示例:
待加密文本:"{"token":"oaudd5Xk70stFxWAXglGEgLrUaHI","macs":"DC4F22529F13"}";
AES key:"bf356292227b87ea";
AES偏移量:"06470ebdd52088cc";
加密结果:"sefyig6EkyYup1yNHcol30is2qtopnyGogMa9c/3DprN0bSJqUdNOWdIVGvSEZCZT8U3eBv+zZKDcw8lbIarTg=="


### 3.6. 响应参数data的解密：

服务端将对data数据做json编码，再做AES加密，最后做BASE64编码

其中，AES的key为appSecret的高16位，AES的偏移量为appSecret的低16位；


### 3.7. 请求地址：

测试环境：https://demo.yaokantv.com:8215/hotel/v1

正式环境：https://hotelapi.yaokantv.com/hotel/v1

4. API 列表
-----------

### 4.1. 设备绑定用户(小程序)

请求参数:

| 名称    | 类型   | 是否必须 | 示例        | 说明               |
|---------|--------|----------|-------------|--------------------|
| reqType | int    | yes      | 1001        | 请求类型，固定值   |
| data    | string | yes      | data string | 加密后的data字符串 |

请求参数data说明:

| 名称  | 类型   | 是否必须 | 示例       | 说明            |
|-------|--------|----------|------------|-----------------|
| token | string | yes      | user token | 用户授权码      |
| macs  | string | yes      | mac1,mac2  | 用户授权码的mac，多个以英文逗号分隔 |

响应数据:

| 名称      | 类型   | 是否必须 | 说明                                         |
|-----------|--------|----------|----------------------------------------------|
| errorCode | int    | yes      | 状态码，0-表示成功，其它值表示失败           |
| message   | string | yes      | 错误信息，成功时为空字符串；失败时为错误原因 |
| data      | string  | yes      | 空字符串                                       |

响应数据示例:

```json
{"errorCode":0,"message":"Operate Success","data":''}
```


### 4.2. 设备解绑用户(小程序)

请求参数:

| 名称    | 类型   | 是否必须 | 示例        | 说明               |
|---------|--------|----------|-------------|--------------------|
| reqType | int    | yes      | 1002        | 请求类型，固定值   |
| data    | string | yes      | data string | 加密后的data字符串 |

请求参数data说明:

| 名称  | 类型   | 是否必须 | 示例       | 说明            |
|-------|--------|----------|------------|-----------------|
| token | string | yes      | user token | 用户授权码       |

响应数据:

| 名称      | 类型   | 是否必须 | 说明                                         |
|-----------|--------|----------|----------------------------------------------|
| errorCode | int    | yes      | 状态码，0-表示成功，其它值表示失败           |
| message   | string | yes      | 错误信息，成功时为空字符串；失败时为错误原因 |
| data      | string  | yes      | 空字符串                                       |

响应数据示例:

```json
{"errorCode":0,"message":"Operate Success","data":''}
```

### 4.3. 获取遥控器列表(云语音控制)

请求参数:

| 名称    | 类型   | 是否必须 | 示例        | 说明               |
|---------|--------|----------|-------------|--------------------|
| reqType | int    | yes      | 1003        | 请求类型，固定值   |
| data    | string | yes      | data string | 加密后的data字符串 |

请求参数data说明:

| 名称 | 类型   | 是否必须 | 示例 | 说明                                     |
|------|--------|----------|------|------------------------------------------|
| macs  | string | yes      | mac1,mac2  | 授权码的mac，多个以英文逗号分隔 |

响应数据:

| 名称      | 类型   | 是否必须 |   | 说明                                         |
|-----------|--------|----------|---|----------------------------------------------|
| errorCode | int    | yes      |   | 状态码，0-表示成功，其它值表示失败           |
| message   | string | yes      |   | 错误信息，成功时为空字符串；失败时为错误原因 |
| data      | string  | yes      |   | AES加密后的响应数据                             |

data解密后为数组，包含多个遥控器对象，遥控器对象说明如下：

| 名称     | 类型   | 是否必须 | 说明                                     |
|----------|--------|----------|------------------------------------------|
| deviceId | string | yes      | 遥控器ID                                 |
| name     | string | yes      | 遥控器名称                               |

响应数据示例:

```json
[{"deviceId":"deviceId1","name":"客厅空调","rcType":7,'mac':'mac1'},{"deviceId":"deviceId2","name":"客厅风扇","rcType":6,'mac':'mac2'}]
```

### 4.4. 语音设备控制(云语音控制)

请求参数:

| 名称    | 类型   | 是否必须 | 示例        | 说明               |
|---------|--------|----------|-------------|--------------------|
| reqType | int    | yes      | 1004        | 请求类型，固定值   |
| data    | string | yes      | data string | 加密后的data字符串 |

请求参数data说明:

| 名称  | 类型   | 是否必须 | 示例       | 说明            |
|-------|--------|----------|------------|-----------------|
| deviceId | string | yes   | deviceId   | 遥控器ID，通过遥控器列表接口获得 |
| cmdName | string | yes   | ChannelNo  | 标准控制指令名称 |
| cmdValue| string | yes   | 26  | 部分指令需要取值，如机顶盒26频道控制 |

响应数据:

| 名称      | 类型   | 是否必须 | 说明                                         |
|-----------|--------|----------|----------------------------------------------|
| errorCode | int    | yes      | 状态码，0-表示成功，其它值表示失败           |
| message   | string | yes      | 错误信息，成功时为空字符串；失败时为错误原因 |
| data      | string | yes      | 空字符串                                    |

响应数据示例:

```json
{"errorCode":0,"message":"Operate Success","data":''}
```

### 4.5. 调状态查询(未开放)

请求参数:

| 名称    | 类型   | 是否必须 | 示例        | 说明               |
|---------|--------|----------|-------------|--------------------|
| reqType | int    | yes      | 1005        | 请求类型，固定值   |
| data    | string | yes      | data string | 加密后的data字符串 |

请求参数data说明:

| 名称 | 类型   | 是否必须 | 示例 | 说明                  |
|------|--------|----------|------|-----------------------|
| deviceId  | string | yes      | deviceId值  | 遥控器ID |

响应数据:

| 名称      | 类型   | 是否必须 | 说明                                         |
|-----------|--------|----------|----------------------------------------------|
| errorCode | int    | yes      | 状态码，0-表示成功，其它值表示失败           |
| message   | string | yes      | 错误信息，成功时为空字符串；失败时为错误原因 |
| data      | string  | yes      | AES加密后的响应数据                             |

data解密后为一个状态对象，说明如下：

| 名称   | 类型   | 是否必须 | 说明                                                   |
|--------|--------|----------|--------------------------------------------------------|
| mode   | string | yes      | 模式：cold-制冷;heat-制热;auto-自动;dry-除湿;wind-送风 |
| speed  | string | yes      | 风速：s0-自动；s1-1档；s2-2档；s3-3档                  |
| temp   | int    | yes      | 温度值                                                 |
| windUd | string | yes      | 上下扫风状态：on-开；off-关；none-不支持               |
| windLr | string | yes      | 左右扫风状态：on-开；off-关；none-不支持               |
| power  | string | yes      | 电源状态：on-开；off-关；                              |

响应数据示例:

```json
[{"deviceId":"deviceId","name":"空调","rcType":7,"state":{"mode":"cold","speed":"auto","temp":"25","windLeft":"on","windUp":"off"}}]
```

### 4.6. 设备用电量查询(部分设备支持,未开放)

请求参数:

| 名称    | 类型   | 是否必须 | 示例        | 说明               |
|---------|--------|----------|-------------|--------------------|
| reqType | int    | yes      | 1006        | 请求类型，固定值   |
| data    | string | yes      | data string | 加密后的data字符串 |

请求参数data说明:

| 名称 | 类型   | 是否必须 | 示例 | 说明                                     |
|------|--------|----------|------|------------------------------------------|
| mac  | string | yes      | mac  | 设备的mac |
| unit | string | yes      | hour | 查询的统计单位：hour,day,week,month,year |
| timeBegin | int | yes      | hour | 查询的开始时间戳 |
| timeEnd | int | yes      | hour | 查询的结束时间戳 |

响应数据:

| 名称      | 类型   | 是否必须 |   | 说明                                         |
|-----------|--------|----------|---|----------------------------------------------|
| errorCode | int    | yes      |   | 状态码，0-表示成功，其它值表示失败           |
| message   | string | yes      |   | 错误信息，成功时为空字符串；失败时为错误原因 |
| data      | string  | yes      |   | AES加密后的响应数据                             |

data说明:

| 名称  | 类型   | 是否必须 |   | 说明                                     |
|-------|--------|----------|---|------------------------------------------|
| tag   | int | yes      |   | 统计单位序号，如1天的1                      |
| value | float  | yes      |   | 单位时间内的用电量，精度为0.0001 |
| createdAt | datetime  | yes      |   | 统计时间 |

响应数据示例:

```json
{"errorCode":0,"message":"Operate Success","data":[{"tag":1,"value":12.0000},{"tag":2,"value":23.0000},{"tag":3,"value":45.0000}]}
```


5. 异常错误码描述
-----------------

### 5.1. 错误码规范： 0表示成功，其它值表示失败。

| 错误码 | 说明         |
|--------|--------------|
| 0      | 操作成功     |
| 100001 | 参数相关错误 |
| 100002 | 资源不存在   |
| 100201 | 操作失败     |
| 100300 | 权限相关信息 |
| 100301 | 无效请求     |
| 100302 | 无效请求     |
| 100500 | 服务异常信息 |

### 6. 指令说明
-----------------

| 指令名称  | 说明         |指令值取值范围|
|-----------|--------------|--------------|
| TurnOn    | 电源开     |  无  |
| TurnOff   | 电源关 |  无  |
| VolumeUp | 音量加   |  无  |
| VolumeDown | 音量减     |  无  |
| ChannelUp | 频道加     |  无  |
| ChannelDown | 频道减 |  无  |
| SetMute | 静音 |  无  |
| CancelMute | 取消静音     |  无  |
| Previous | 上一页     |  无  |
| Next | 下一页 |  无  |
| Charge | 充电     |  无  |
| Pause | 暂停 |  无  |
| OpenSwing | 摆风开     |  无  |
| CloseSwing | 摆风关 |  无  |
| ChannelNo | 频道号     |  具体频道号数字，如“12”  |
| ChannelName | 频道名称 |  具体的频道名称，如“浙江卫视”  |
| WindSpeedUp     | 风速加     |  无  |
| WindSpeedDown   | 风速减 |  无  |
| SetMode | 模式调节    | sterilize,sleep  |

| 空调指令名称  | 说明         |取值范围|
|---------------|--------------|--------------|
| SetMode | 模式调节    | auto,cold,heat,airsupply,dehumidification  |
| SetWindSpeed          | 风速调节     |  auto,low,medium,high  |
| SetTemperature        | 温度调节 |  16-30  |
| TemperatureUp   | 温度加     |  无  |
| TemperatureDown | 温度减 |  无  |
| OpenLeftAndRightSwing | 左右扫风开 |  无  |
| CloseLeftAndRightSwing | 左右扫风关     |  无  |
| OpenUpAndDownSwing     | 上下扫风开 |  无  |
| CloseUpAndDownSwing    | 上下扫风关     |  无  |


#更新历史

| 版本 | 说明 | 备注 | 日期     |
|------|------|------|----------|
| v1.0 | 新建 | Mark | 20190920 |
|      |      |      |          |
