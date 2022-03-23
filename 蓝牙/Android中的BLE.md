# Android中的BLE



## 0x1 广播

1.1 AdvertiseSettings和AdvertisingSetParameters

安卓系统对应的是AdvertiseSettings或AdvertisingSetParameters参数，这两个类都可以表示出广播参数，只不过AdvertisingSetParameters包含的内容更多，同时也使用与扩展广播，即如果你想使能一个扩展广播，则参数只能使用AdvertisingSetParameters来组织

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020032616110487.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDI2MDAwNQ==,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200326161135857.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDI2MDAwNQ==,size_16,color_FFFFFF,t_70)



## 0x2 扫描





## 0x3 连接





## 0x4 通信



## 0x5 Android中的坑

### 5.1 扫描

1. ScanCallback.onScanFailed errorCode = 2

开启/关闭扫描过于频繁，手动打开关闭蓝牙可解。