# Cocoa框架

### 1. Cocoa Touch是什么?

Cocoa Touch 是 iOS操作系统的程序的运行环境。

###### Cocoa区别于Cocoa Touch， 两者都包含OC运行时的两个核心框架。

Cocoa包含Foundation和AppKit框架，可用于开发Mac OS X系统的应用程序。
Cocoa Touch包含Foundation和UIKit框架，可用于开发iPhone OS 系统的应用程序。
Cocoa是Mac OS X的开发环境，Cocoa Touch是 iPhone OS的开发环境。

### 2. Cocoa framework

Cocoa本身是一个框架的集合，它包含了众多子框架，其中最重要最基本的两个框架是：[Foundation](https://link.jianshu.com/?t=https://developer.apple.com/reference/foundation) 和 [UIKit](https://link.jianshu.com/?t=https://developer.apple.com/reference/uikit)。

Foundation 是框架的基础，和界面无关，其中包含了大量常用的API；后者是基础的UI类库，以后我们在iOS开发中会经常用到。这两个框架在系统中的位置如下图

![img](https://upload-images.jianshu.io/upload_images/1400023-5ef945e7d1f72abb.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/499)

### 3. Foundation框架

iOS程序都是由大量的对象构成，而这些对象的根对象都是NSObject，NSObject就处在Foundation框架之中，具体的类结构如下：

![img](https://upload-images.jianshu.io/upload_images/1400023-754f6003f9bfd7a1.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/373)

### 4. UIKit框架

应用程序可以通过三种方式使用UIKit创建界面

1. 在用户界面工具（interface Buidler）从对象库里 拖拽窗口，视图或者其他的对象使用。

2. 用代码创建

3. 通过继承UIView类或间接继承UIView类实现自定义用户界面

![img](https://upload-images.jianshu.io/upload_images/1400023-292bfed1d2311bf8.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/873)

在图中可以看出，UIResponder 类是图中最大分支的根类，UIResponder处理响应事件和响应链，定义了界面和默认行为。当用户用手指滚动列表或者在虚拟键盘上输入时，UIKit就生成事件传送给UIResponder响应链，直到链中有对象处理这个事件。
相应的核心对象，比如：UIApplication ，UIWindow，UIView都直接或间接的从UIResponder继承