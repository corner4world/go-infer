
## API文档

### 1. 全局接口定义

输入参数

| 参数      | 类型   | 说明                          | 示例        |
| --------- | ------ | ----------------------------- | ----------- |
| appId     | string | 应用渠道编号                  |             |
| version   | string | 版本号                        |             |
| signType  | string | 签名算法，国密SM2或SHA256 | SM2或SHA256 |
| signData  | string | 签名数据，具体算法见下文      |             |
| encType   | string | 接口数据加密算法，目前不加密  | plain       |
| timestamp | int    | unix时间戳（秒）              |             |
| data      | json   | 接口数据，详见各接口定义      |             |

> 签名/验签算法：
>
> 1. 筛选，获取参数键值对，剔除signData、encData、extra三个参数。data参数按key升序排列进行json序列化。
> 2. 排序，按key升序排序。
> 3. 拼接，按排序好的顺序拼接请求参数
>
> ```key1=value1&key2=value2&...&key=appSecret```，key=appSecret固定拼接在参数串末尾，appSecret需替换成应用渠道所分配的appSecret。
>
> 4. 签名，使用指定的算法进行加签获取二进制字节，使用16进制进行编码得到签名字符串，然后base64编码。
> 5. 验签，对收到的参数按1-4步骤签名，比对得到的签名串与提交的签名串是否一致。

签名示例：

```
请求参数：
{
    "appId":"3EA25569454745D01219080B779F021F",
    "version": "1",
    "signType": "SM2",
    "signData": "...",
    "encType": "plain",
    "timestamp":1658716494,
    "data": {
        "text":"测试测试",
        "image":""
    }
}

密钥：
appSecret="41DF0E6AE27B5282C07EF5124642A352"
SM2_privateKey="JShsBOJL0RgPAoPttEB1hgtPAvCikOl0V1oTOYL7k5U="

待加签串：
appId=3EA25569454745D01219080B779F021F&data={"image":"","text":"测试测试"}&encType=plain&signType=SHA256&timestamp=1658716494&version=1&key=41DF0E6AE27B5282C07EF5124642A352

SHA256加签结果：
"a68c1b852a650314afaad684f3652c336c9b969e943825a29380b516de746ece"

base64后结果：
"YTY4YzFiODUyYTY1MDMxNGFmYWFkNjg0ZjM2NTJjMzM2YzliOTY5ZTk0MzgyNWEyOTM4MGI1MTZkZTc0NmVjZQ=="

SM2加签结果（每次不同）：
"ILSOY5A0/sfW5Y9T6rIjl1AEPlDtQeqtwAxLibNbnajlj2fY/DxvTuSok+sqxy2St4pvvs4/rdaNOCNpwBuJ6A=="
```

返回结果

| 参数      | 类型    | 说明                                                         | 示例  |
| --------- | ------- | ------------------------------------------------------------ | ----- |
| appId     | string  | 应用渠道编号                                                 |       |
| code      | string  | 接口返回状态代码                                             |       |
| signType  | string  | 签名算法，plain： 不用签名，                                | plain |
| encType   | string  | 接口数据加密算法，目前不加密                                 | plain |
| success   | boolean | 成功与否                                                     |       |
| timestamp | int     | unix时间戳                                                   |       |
| requestId | string  | 当次请求的标识id                                              |       |
| data      | json    | 成功时返回结果数据；出错时，data.msg返回错误说明。详见具体接口 |       |

> 成功时：code为0， success为true，data内容见各接口定义；
> 出错时：code返回错误代码，具体定义见各接口说明

返回示例

```json
{
    "appId": "3EA25569454745D01219080B779F021F", 
    "code": 0, 
    "signType": "plain",
    "encType": "plain",
    "success": true,
    "timestamp": 1658716495,
    "data": {
       "msg": "success", 
       ...
       "requestId": "20220727e5b9bbfe0e33c01e3c8ccb9c7382d512"
    }
}
```

全局出错代码

| 编码 | 说明                               |
| ---- | ---------------------------------- |
| 9800 | 无效签名                           |
| 9801 | 签名参数有错误                     |
| 9802 | 调用时间错误，unixtime超出接受范围 |



### 2. example中获取文本特征

> 获取文本embeddings
>
> 注意：
>
> 1. 只是演示，不保证结果准确
> 2. 模型权重导出自官方中文Bert，具体见export目录

请求URL

> http://127.0.0.1:5000/api/embedding

请求方式

> POST

输入参数

| 参数  | 必选 | 类型   | 说明               |
| ----- | ---- | ------ | ------------------ |
| text | 是   | string | 输入的文本 |

请求示例

```json
{
    "text" : "测试测试"
}
```

返回结果

| 参数       | 必选 | 类型   | 说明                 |
| ---------- | ---- | ------ | -------------------- |
| embeddings     | 是   | float数组 | 输入文本的embeddings |


返回示例

```json
{
    "appId": "", 
    "code": 0, 
    "data": {
        "embeddings": [
            0.21856225, 0.22315553, 
            ...
            , -0.5392351, -0.14255117
        ], 
        "msg": "success",
        "requestId": "2022072792c2e34ae170db21066849f015dd3133"
    }, 
    "encType": "plain", 
    "signType": "plain", 
    "success": true, 
    "timestamp": 1658716495
}
```

### 3. example中获取图片特征

> 使用Mobilenet获取图片embeddings
>
> 注意：
>
> 1. 只是演示，不保证结果准确
> 2. 模型权重导出自Keras官方权重，具体见export目录

请求URL

> http://127.0.0.1:5000/api/mobile

请求方式

> POST

输入参数

| 参数  | 必选 | 类型   | 说明               |
| ----- | ---- | ------ | ------------------ |
| image | 是   | string | base64编码图片数据 |
> 图片尺寸 224\*224

请求示例

```json
{
    "image" : "..."
}
```

返回结果

| 参数       | 必选 | 类型   | 说明                 |
| ---------- | ---- | ------ | -------------------- |
| embeddings     | 是   | float数组 | 输入图片的embeddings |


返回示例

```json
{
    "appId": "", 
    "code": 0, 
    "data": {
        "embeddings": [[[
            1.802082e-11, 2.1090724e-10, 
            ..., 
            1.6219401e-10, 1.038099e-10
        ]]], 
        "msg": "success",
        "requestId": "20220727b796f2356b94bcbd47679b2606a8b117"
    }, 
    "encType": "plain", 
    "signType": "plain", 
    "success": true, 
    "timestamp": 1658900086
}
```
