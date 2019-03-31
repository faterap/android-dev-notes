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

## 种类

- Messenger（基于AIDL的上层封装）
- AIDL（基于Binder封装）
- Intent（基于Binder封装）
- Binder（基于Binder封装）
- ContentProvider（基于Binder封装）

### Messenger

- Handler 每次只会处理一个message
- 无法考虑并发的情况
- 只能传输 Bundle 支持的数据类型

## 总结

- 只有允许不同应用的客户端用 IPC 方式调用远程方法，并且想要在服务中处理多线程时，才有必要使用 AIDL
- 如果需要调用远程方法，但不需要处理并发 IPC，就应该通过实现一个 Binder 创建接口
- 如果您想执行 IPC，但只是传递数据，不涉及方法调用，也不需要高并发，就使用 Messenger 来实现接口
- 如果需要处理一对多的进程间数据共享（主要是数据的 CRUD），就使用 ContentProvider
- 如果要实现一对多的并发实时通信，就使用 Socket
