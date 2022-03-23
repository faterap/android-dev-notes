# TWS耳机业务协议 v0.0.1（内部）

## 0. 名词解释

本文介绍 QQ音乐 BLE基础规范，包括蓝牙广播规范和蓝牙接入服务规范，鉴权方案等。

| 名词            | 描述                                                         |
| :-------------- | :----------------------------------------------------------- |
| BLE             | Bluetooth Low Energy：蓝牙低功耗技术                         |
| GATT            | Generic Attribute Profile：蓝牙通用属性协议                  |
| MAC             | Media AccessControl：媒介访问地址，6 bytes                   |
| AD              | Advertisement：广播                                          |
| BID             | Brand ID：2 bytes，每个品牌唯一，用于BLE广播                 |
| PID             | Product ID：4 bytes，每款产品唯一，用于BLE广播与握手协议     |
| UUID            | Universally Unique Identifier：全局唯一标识                  |
| Service         | 把数据分成一个个的独立逻辑项，它包含一个或者多个 Characteristic |
| Characteristics | 定义了数值和操作，包含一个Characteristic声明、Characteristic属性、值、值的Descriptor |
| Descriptor      | 对Characterisctic的描述，如范围、单位等                      |

## 1. UUID

### 1.1 Service UUID

使用[Bluetooth SIG](https://btprodspecificationrefs.blob.core.windows.net/assigned-values/16-bit%20UUID%20Numbers%20Document.pdf)中Tencent Holdings Limited注册的UUID：

```
0000FEBA-0000-1000-8000-00805f9b34fb
```

#### 1.2 Characteristics UUID

Service中包含3个Characteristics，如图所示

| Characteristics名称      | Characteristics UUID | 是否必选 | 属性           | 许可权限 |
| :----------------------- | :------------------- | :------- | :------------- | :------- |
| Read Characteristics     | 0xFEDB               | 是       | Read           | Read     |
| Write Characteristics    | 0xFEDC               | 是       | Read或Write    | Write    |
| Indicate Characteristics | 0xFEDD               | 是       | Read或Indicate | None     |

## 2. 报文规范

### 2.1 广播包

蓝牙广播包格式遵循BLE 4.0规范，由若干AD Structure组成（[参见Bluetooth 4.2 Core Specification, Volume 3, Part C, Chapter 11](https://www.google.com.hk/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&ved=2ahUKEwj71I74w4fxAhWByIsBHadyB6gQFjACegQIBRAD&url=https%3A%2F%2Fwww.bluetooth.org%2FDocMan%2Fhandlers%2FDownloadDoc.ashx%3Fdoc_id%3D286439&usg=AOvVaw2615ehHQjzs73cTQISeHxh)），每一个AD Structure结构由Length、AD Type、AD Data组成。如下图所示：

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/3394359951/p128952.png)



### 2.2 广播报文

| 字节序 | 名称    | 示例值         | 说明               |
| :----- | :------ | :------------- | :----------------- |
| 0      | LENGTH  | 0x17           | 广播自定义数据长度 |
| 1      | TYPE    | 0xFF           | 广播自定义数据类型 |
| 2-3    | BID     | 0x0100         | 品牌id             |
| 4-7    | PID     | 0x04030201     | 产品id             |
| 9-14   | MAC     | 0xBEF60F39D088 | 蓝牙设备MAC地址    |
| 15-16  | VERSION | 0x0001         | 私有协议版本号     |
| ...    | ...     | ...            | ...                |

### 2.3 数据报文

#### 2.3.1 数据传输规范

由于BLE 4.0一次只能发送20字节的有效数据，当一包数据超过20字节时，蓝牙无法一次完成数据的传送，所以在发送长数据包时，需要发送时拆包，接收时组包。发送数据报文时需要考虑以下几点：

- 单包最大数据长度

- 不同的蓝牙版本下，蓝牙在应用层可以传输长度和本规范应用数据长度如下表所示，下文用N代表规范数据长度。

  | BLE版本 | 应用层数据长度 | 规范数据长度（N） |
  | :------ | :------------- | :---------------- |
  | BLE 4.0 | 20             | 17                |
  | BLE 4.2 | 244            | 241               |
  | BLE 5.0 | 244            | 241               |

由于本文讨论的蓝牙设备为TWS耳机，目前市面上TWS耳机绝大多数为BLE 5.0以上，因此N默认为241。



#### 2.3.2 报文格式

- 每一包数据由Header，Payload组成，Header长度3字节，Payload 长度0~N字节；
- 消息长度超过N字节时，分成多帧发送，接收到后按照Header描述重新组包；
- Header占3字节，Payload数据长度为0~N字节，整包数据长度在3~3+N字节。

| 字节序 | 名称    | 示例值   | 说明                                                         |
| :----- | :------ | :------- | :----------------------------------------------------------- |
| 0-2    | HEADER  | 0xFA1010 | 包含指令类型，帧数据长度，协议版本号                         |
| 3      | SEQ     | 0x01     | 用于app和蓝牙设备各自做命令计数，从0开始递增，分包结束后重置为0 |
| 4-N    | PAYLOAD | ...      | 有效数据，**使用小端传输**                                   |

#### 2.3.3 HEADER

##### 2.3.3.1 指令类型

蓝牙设备应用层和手机App交互的指令定义示例如下:

| 分类                 | 指令 | 说明                                            | Characteristics 属性 |
| :------------------- | :--- | :---------------------------------------------- | -------------------- |
| 设备主动命令         | 0x01 | 蓝牙设备单向发送命令给手机App                   | Indicate             |
| Request-Response模型 | 0x02 | 手机App发出请求指令，需要设备回复，与0x03对应。 | Read/write           |
| Request-Response模型 | 0x03 | 蓝牙设备回复请求指令，与0x02对应。              | Read/write           |
| Request-Response模型 | 0x04 | 蓝牙设备发出请求指令，需要手机App，与0x05对应。 | Read/write           |
| Request-Response模型 | 0x05 | 手机App回复请求指令，与0x04对应。               | Read/write           |
| 连接建立指令         | 0x0A | 下发时间T                                       | Write                |
| 连接建立指令         | 0x0B | 上传PID, PubkeyB, 密文P                         | Indicate             |
| 连接建立指令         | 0x0C | 回传服务端返回随机数R                           | Write                |
| 连接建立指令         | 0x0D | 设备与手机端握手结果                            | Indicate             |

##### 2.3.3.2 帧数据长度

数据长度为0-N字节，如0x10

##### 2.3.3.3 协议版本号

协议版本号，如0x10

#### 2.3.4 Payload

设备主动命令：

| 分类     | 描述                  | 指令 |
| :------- | :-------------------- | :--- |
| 快速进入 | 快速打开Q音           | 0x01 |
| 发起播放 | 播放我喜欢的歌单      | 0x0A |
| 发起播放 | 播放已购买的专辑      | 0x0B |
| 发起播放 | 播放自建歌单          | 0x0C |
| 发起播放 | 播放收藏歌单          | 0x0D |
| 发起播放 | 播放每日30首          | 0x0E |
| 发起播放 | 播放个性电台          | 0x0F |
| 发起播放 | 播放百万收藏          | 0x10 |
| 发起播放 | 播放新歌推荐          | 0x11 |
| 发起播放 | 播放热歌榜            | 0x12 |
| 发起播放 | 播放首页快听          | 0x13 |
| 发起播放 | 自动播放播客tab长音频 | 0x14 |
| 发起播放 | 自动播放推荐视频      | 0x15 |
| 基础操作 | 切换上一首            | 0x1E |
| 基础操作 | 切换下一首            | 0x1F |
| 基础操作 | 播放/暂停             | 0x20 |
| 基础操作 | 收藏至我喜欢          | 0x21 |



Request-Response模型：

暂无



连接建立指令：

请参考下文 3.6 连接建立指令集。



## 3 鉴权方案

### 3.1 Qmii方案比较

### 3.1.1 应用场景

Qmii：硬件发出声波--->App端解析鉴权--->鉴权成功，合法设备--->展示专辑

TWS耳机：耳机发出扫描/回复报文--->手机端连接并进行鉴权--->鉴权成功，合法设备--->开始进行数据交换--->循环交换数据



### 3.1.2 方案通用性分析

#### Qmii：

- 传输数据单一。每次只需要传输固定的SN和MusicSalt，不同的SN和MusicSalt代表不同数字专辑；
- 每次数据传输过程会进行加解密，摘要计算。Qmii会对SN进行加盐，非对称加密和计算摘要处理，最终服务端进行解密，得到数字专辑对应SN。



#### TWS耳机：

- 私有协议内容丰富，可传输数据量众多
- 只需要在鉴权阶段确认身份，后续传输数据阶段不需要进行复杂的加解密过程。



### 3.1.3 总结：

- 蓝牙设备通信大多使用私有协议，可传输数据量种类很多，并且对实时性要求很高。Qmii方案只能满足单一数据传输，并且每次和手机端通信都要通过服务端解密，验证，时间周期会很长，显然不满足需求；
- BLE协议已经有比较完善的鉴权方案，下面会给出方案具体流程。

### 3.2 名词解析

| 名词    | 描述                                                         |
| :------ | :----------------------------------------------------------- |
| ECC     | Elliptic Curve Cryptography, 椭圆曲线密码                    |
| ECDH    | 椭圆曲线 Diffie-Hellman 密钥交换                             |
| AES     | 对称加密算法                                                 |
| RSA     | 非对称加密算法                                               |
| DSA     | Digital Signature Algorithm，数字签名算法                    |
| ECDSA   | Elliptic Curve Digital Signature Algorithm，椭圆曲线数字签名算法 |
| PriKeyA | ECC算法私钥，预先分配，24 bytes                              |
| PubKeyA | ECC算法公钥，预先分配，48 bytes                              |
| PriKeyB | ECC算法临时私钥，耳机端生成，24 bytes                        |
| PubKeyB | ECC算法临时公钥，耳机端生成，48 bytes                        |
| DHKey   | 共享密钥，以PubKeyA和PriKeyB生成24 Bytes密钥                 |
| T       | 时间戳，4 bytes 无符号整型                                   |
| R       | 随机数，8 bytes 无符号整型                                   |
| M       | 明文，32 bytes                                               |
| P       | 密文，32 bytes                                               |

### 3.3 加密算法

#### 3.3.1 ECDH介绍

加密算法采用椭圆曲线 Diffie-Hellman 密钥交换 (ECDH)。

椭圆曲线密码(Elliptic Curve Cryptography, ECC)是近期备受关注的公钥密码算法。它的特点是比 RSA 所需的密钥长度短。**椭圆曲线密码密钥短但强度高**。密钥长度 224 - 255 比特的椭圆曲线密码，与密钥长度为 2048 比特的 RSA 具备相同的长度。

由于 ECC 密钥具有很短的长度，所以运算速度比较快。到目前为止，对于 ECC 进行逆操作还是很难的，数学上证明不可破解，ECC 算法的优势就是性能和安全性高。实际应用可以结合其他的公开密钥算法形成更快、更安全的公开密钥算法，比如结合 DH 密钥形成 ECDH 密钥协商算法，结合数字签名 DSA 算法组成 ECDSA 数字签名算法。

#### 3.3.2 比较

优点：

- 使用椭圆曲线 Diffie-Hellman 密钥交换算法，可保证数据的加密性，防止其它人窃听数据
- ECC对比RSA有以下的优势：加密速度快，效率更高，对服务器资源消耗低，而且最重要的是更安全，抗攻击型更强。尤其BLE协议中每次传输数据量有限，用ECC算法可以节省数据量，加快数据通信效率

缺点：

- ECDH对于篡改和伪装（即中间人攻击，MITM）无能为力，若要解决这些问题，需使引入消息认证码（MAC）或者数字签名技术。但这样的话会极大增加计算量和方案复杂度，在蓝牙设备通讯范畴内可以暂不做考虑。

### 3.4 算法和安全说明

- PID, MAC是云端分配的数据，预先烧录在设备中；

- 预先分配一对ECC私钥（PriKeyA，24 Bytes）和公钥（PubKeyA，48 Bytes），私钥保存在服务端，公钥保存在耳机端。

### 3.5 鉴权流程

![Untitled Diagram](/Users/faterap/Downloads/Untitled Diagram.svg)

鉴权步骤：

1. 开始鉴权时，App向耳机发送数据，下发4 Bytes的当前时间戳T；
2. 耳机通过ECC算法，产生24 Bytes随机数作为临时私钥（PriKeyB），并产生对应的临时公钥（PubKeyB，48 Bytes）；
3. 耳机通过ECDH算法，以公钥PubKeyA和临时公钥PriKeyB生成24 Bytes共享密钥DHKey，并截取其前16 Bytes作为AES密钥（AesKey）;
4. 耳机再生成8 Bytes随机数R，依次拼接组合PID、时间戳T、随机数R、蓝牙MAC（6 bytes）地址，成为22 Bytes明文M，通过后补0的方式补齐明文M到32 Bytes；
5. 耳机再通过AES128 算法，以AesKey对明文M加密，产生32 Bytes密文P；
6. 耳机向App发送数据，把PID（4 Bytes 明文）、临时公钥PubKeyB（48 Bytes）和P（32 Bytes）回传到App；
7. App把步骤6中所有数据，连同时间戳T，上传到服务端；
8. 服务端同样通过ECDH算法，使用私钥PriKeyA、临时公钥PubKeyB产生共享密钥DHKey，同样截取出AesKey，并解密P，得到M；
9. 服务端依次校对时间戳T及MAC地址，若均合法，则鉴权成功，向App返回随机数R，否则返回错误码；
10. 回传随机数R给耳机进行校对，随机数R若一致，证明握手成功，否则握手失败。



**算法参考资料：**

- [翱游公钥密码算法](https://halfrost.com/asymmetric_encryption/)
- [天猫精灵蓝牙BLE基础规范](https://help.aliyun.com/document_detail/173315.html?spm=a2c4g.11186623.6.691.2c0b7c4fvXjUqc)
- [BLE安全机制从入门到放弃](https://blog.csdn.net/helaisun/article/details/100566163)
- [BLE（一）概述&工作流程&常见问题 ](http://www.gandalf.site/2018/11/ble.html)
- 酷狗蓝牙牛方案协议文档  

### 3.6 连接建立指令集

| 指令类型（1 byte） | 长度 | Payload                                          |
| ------------------ | ---- | ------------------------------------------------ |
| 0x0A               | 0x04 | 下发时间戳T                                      |
| 0x0B               | 0x36 | 上传PID, 临时公钥PubKeyB, 密文P                  |
| 0x0C               | 0x08 | 回传服务端返回随机数R                            |
| 0x0D               | 0x01 | 设备与手机端握手结果，0x01代表成功，0x00代表失败 |

BLE 4.0中，指令0x0B和0x0C中，对应Payload长度超过BLE单个数据报文长度，需要进行分包处理，对应SEQ长度需要递增。

### 3.7 连接指令与Characteristics对应关系

| 指令类型 | 说明                    | 传输使用的Characteristics | Characteristics UUID |
| -------- | ----------------------- | ------------------------- | -------------------- |
| 0x0A     | 下发时间戳T             | Write Characteristics     | 0xFEDC               |
| 0x0B     | 上传PID, PubKeyB, 密文P | Indicate Characteristics  | 0xFEDD               |
| 0x0C     | 回传服务端返回随机数R   | Write Characteristics     | 0xFEDC               |
| 0x0D     | 设备与手机端握手结果    | Indicate Characteristics  | 0xFEDD               |

