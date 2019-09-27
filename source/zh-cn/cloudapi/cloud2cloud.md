# Yaokan Cloud API 接口文档


    文件编号：YAOKANCLOUDAPI-20190902
    版本：v1.0

    深圳遥看科技有限公司
    （版权所有，切勿拷贝）



| 版本 | 说明 | 备注 | 日期 |
| --- | --- | --- | --- |
| v1.0 | 新建 | Mark | 20190902 |
|   |   |   |   |   |



## 1. 概述
遥看云接口，提供通过HTTP协议查看设备状态、向设备发送控制指令功能。


## 2. 文档阅读对象
使用遥看云产品,想通过云云对接实现设备的控制的客户

## 3. 接口规范
### 3.1. 请求头

| 名称 | 类型 | 是否必须 | 示例 | 说明 |
| --- | --- | --- |--- | --- |
| Content-Type | string | yes | application/x-www-form-urlencoded | 设置请求体的MIME类型 |
| appId | string | yes | appid | 平台分配的APPID |
| timestamp | int | yes | timestamp | 时间戳 |
| signature | string | yes | signature | 签名 |

### 3.2. 签名生成算法

将`appSecret`、请求时间戳`timestamp`拼接成字符串A，然后对A做`MD5`加密获得字符串B，然后取B的第`1，3，7，15，31`位置上的字符，拼接成C，C即为签名的值

PHP示例:
```php
$appSecret='bf356292227b87ea06470ebdd52088cc'
$timestamp=1568801254

//拼接签名参数字符串

$signStr=($appSecret.$timestamp)='bf356292227b87ea06470ebdd52088cc1568801254'

//签名字符串做MD5

$B=md5($signStr)='d74872aefc98db97678e4f5f2dd865f8'

//取MD5后指定位上的内容

$C=$B{1}+$B{3}+$B{7}+$B{15}+$B{31}=78e78
```
### 3.3. 请求方式:POST

### 3.4. 返回格式:JSON

### 3.5. 请求地址：

1. 测试环境：`demo.yaokantv.com:8211/cloud/v1`

1. 正式环境：`ykc.yaokantv.com/cloud/v1`


## 4. API 列表
### 4.1. 查询设备状态

请求参数:

| 名称 | 类型 | 是否必须 | 示例 | 说明 |
| --- | --- | --- |--- | --- |
| reqType | int | yes | 1001 | 请求资源类型，固定值 |
| macs | string | yes | mac1,mac2 | 所查询状态的设备mac，多个以英文逗号分隔 |

响应数据:

| 名称      | 类型   | 是否必须 | 说明 |
| ---       | ---    | ---      | --- |
| errorCode | int    | yes      | 状态码，0-表示成功，其它值表示失败 |
| message   | string | yes      | 错误信息，成功时为空字符串；失败时为错误原因 |
| data      | array  | yes      | 成功时的响应数据 |

data说明:

| 名称 | 类型 | 是否必须 | 示例 | 说明 |
| --- | --- | --- |--- | --- |
| mac | string | yes | mac1 |  设备mac |
| state | int | yes | 1 |  设备在线状态：0-离线；1-在线 |

响应数据示例:
```json
{"errorCode":0,"message":"Operate Success","data":[{"did":"C975F459BCC1**C9","state":0},{"did":"7B4D145E7D63**DD","state":1}]}
```

### 4.2. 发送控制指令

请求参数:

| 名称 | 类型 | 是否必须 | 示例 | 说明 |
| --- | --- | --- |--- | --- |
| reqType | int | yes | 1002 | 请求资源类型，固定值 |
| mac | string | yes | did |  接收指令的设备mac |
| codeType | int | yes | 1 |  1:官方红外码,2.学习,3.官方红外码-STB,4:射频 |
| rid | string | yes | rid |  码库id |
| cmds | string | yes | cmdKey1,cmdKey2 |  指令的键名，同一码库ID的多个指令，以英文逗号分隔 |

响应数据:

| 名称 | 类型 | 是否必须 | 说明 |
| ---  | ---  | ---      |---   | 
| errorCode | int | yes | 状态码，0-表示成功，其它值表示失败 |
| message | string | yes | 错误信息，成功时为空字符串；失败时为错误原因 |
| data | array | yes | 空数组 |

响应数据示例:
```json
{"errorCode":0,"message":"Operate Success","data":[]}
```

## 5. 异常错误码描述
5.1. 错误码规范：
0表示成功，其它值表示失败。

| 错误码 | 说明 |
| --- | --- |
| 0 | 操作成功 |
| 100001  | 参数相关错误  |   |
| 100002  | 资源不存在  |   |
| 100201  | 操作失败  |   |
| 100300  | 权限相关信息  |   |
| 100301  | 无效请求  |   |
| 100302  | 无效请求  |   |
| 100500  | 服务异常信息  |   |