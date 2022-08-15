# iOS整体框架

### 1. 介绍

在Android开发中，采用模板模式来实现应用程序的一些特性行为，Android提供了Activity,Service,Content providers,Broadcast receivers四大组件默认功能，应用通过继承这些组件根据需要覆盖组件的一些方法来完成应用程序开发。在iOS中则采用代理和协议模式来实现应用的特性行为。例如Cocoa Touch框架集合中的UIKit框架的UIApplication对象，它负责整个应用程序生命周期的事件分发。是应用最核心的一个对象，Android的设计中就需要对其子类化，覆盖父类的方法，iOS中则交给UIApplication的代理AppDeleagte来处理应用程序的各种状态改变相关事件(AppDelegate需要实现UIApplicationDelegate协议) 。在iOS的框架中，大量的使用代理和协议。

### 2. 框架图

iOS提供的许多可使用的框架，构成了iOS操作系统的层次结构，从下到上依次是:Core OS、Core Ssevices、MediaLayer、Cocoa Touch共四层。下图为iOS8.3系统的框架架构图。

![img](https://upload-images.jianshu.io/upload_images/696136-ade633eeaa109baa.png?imageMogr2/auto-orient/strip|imageView2/2/w/1070)

Core OS Layer：系统核心层包含大多数低级别接近硬件的功能，它所包含的框架常常被其它框架所使用。Accelerate框架包含数字信号，线性代数，图像处理的接口。针对所有的iOS设备硬件之间的差异做优化，保证写一次代码在所有iOS设备上高效运行。CoreBluetooth框架利用蓝牙和外设交互，包括扫描连接蓝牙设备，保存连接状态，断开连接，获取外设的数据或者给外设传输数据等等。Security框架提供管理证书，公钥和私钥信任策略，keychain,hash认证数字签名等等与安全相关的解决方案。

Core Services Layer：系统服务层提供给应用所需要的基础的系统服务。如Accounts账户框架，广告框架，数据存储框架，网络连接框架，地理位置框架，运动框架等等。这些服务中的最核心的是CoreFoundation和Foundation框架，定义了所有应用使用的数据类型。CoreFoundation是基于C的一组接口，Foundation是对CoreFoundation的OC封装。

Media Layer：媒体层提供应用中视听方面的技术，如图形图像相关的CoreGraphics,CoreImage,GLKit,OpenGL ES,CoreText,ImageIO等等。声音技术相关的CoreAudio,OpenAL,AVFoundation,视频相关的CoreMedia,Media Player框架，音视频传输的AirPlay框架等等。

Cocoa Touch Layer：触摸层提供应用基础的关键技术支持和应用的外观。如NotificationCenter的本地通知和远程推送服务，iAd广告框架，GameKit游戏工具框架，消息UI框架，图片UI框架，地图框架，连接手表框架，自动适配等等