# Android 类加载器

```
libcore/dalvik/src/main/java/dalvik/system/
    - PathClassLoader.java
    - DexClassLoader.java
    - BaseDexClassLoader.java
    - DexPathList.java
    - DexFile.java

art/runtime/native/dalvik_system_DexFile.cc

libcore/ojluni/src/main/java/java/lang/ClassLoader.java
```

## 关系图

![img](http://gityuan.com/images/classloader/classloader.jpg)



## 概述

Android从5.0开始就采用art虚拟机, 该虚拟机有些类似Java虚拟机, 程序运行过程也需要通过ClassLoader 将目标类加载到内存.

传统Jvm主要是通过读取class字节码来加载, 而art则是从dex字节码来读取. 这是一种更为优化的方案, 可以将多个.class文件合并成一个classes.dex文件。



## 原理

热修复核心逻辑：在DexPathList.findClass()过程，一个Classloader可以包含多个dex文件，每个dex文件被封装到一个Element对象，这些Element对象排列成有序的数组dexElements。当查找某个类时，会遍历所有的dex文件，如果找到则直接返回，不再继续遍历dexElements。也就是说当两个类不同的dex中出现，会优先处理排在前面的dex文件，这便是热修复的核心精髓，将需要修复的类所打包的dex文件插入到dexElements前面。



## ClassLoader

1. PathClassLoader

PathClassLoader被用来加载本地文件系统上的文件或目录，但不能从网络上加载，**关键是它被用来加载系统类和我们的应用程序**，这也是为什么它的两个构造函数中调用父类构造器的时候第二个参数传null，具体的参数意义请看接下来DexClassLoader的注释。

2. DexClassLoader

DexClassLoader用来加载jar、apk，其实还包括zip文件或者直接加载dex文件，它可以被用来执行未安装的代码或者未被应用加载过的代码。



## 总结

- PathClassLoader: 主要用于系统和app的类加载器,其中optimizedDirectory为null, 采用默认目录/data/dalvik-cache/
- DexClassLoader: 可以从包含classes.dex的jar或者apk中，加载类的类加载器, 可用于执行动态加载,但必须是app私有可写目录来缓存odex文件. 能够加载系统没有安装的apk或者jar文件， 因此很多插件化方案都是采用DexClassLoader;
- BaseDexClassLoader: 比较基础的类加载器, PathClassLoader和DexClassLoader都只是在构造函数上对其简单封装而已.
- BootClassLoader: 作为父类的类构造器。



------

[]: https://www.jianshu.com/p/3afa47e9112e	"Android类加载机制的细枝末节"
[]: http://gityuan.com/2017/03/19/android-classloader/	"Android类加载器ClassLoader"

