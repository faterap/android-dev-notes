# Android 多进程

## 背景

### 进程模型

安装Android应用程序的时候，Android会为每个程序分配一个Linux用户ID，并设置相应的权限，这样其它应用程序就不能访问此应用程序所拥有的数据和资源了。

在 Linux 中，一个用户ID 识别一个给定用户；在 Android 上，一个用户ID 识别一个应用程序。应用程序在安装时被分配用户 ID，应用程序在设备上的存续期间内，用户ID 保持不变。

![image](https://images.cnblogs.com/cnblogs_com/ghj1976/201104/201104281216437986.png)

## 影响

两个进程对应的是不同的内存区域，所有会有以下影响：

- **1.Application对象会创建多次**
- **2.静态成员不共用**
- **3.同步锁失效**
- **4.单例模式失效**
- **5.数据传递的对象必须可序列化**

## IPC 方法

- Messenger(基于AIDL的上层封装)
- AIDL

### Messenger

- Handler 每次只会处理一个message
- 无法考虑并发的情况

