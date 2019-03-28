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



## 总结

- PathClassLoader: 主要用于系统和app的类加载器,其中optimizedDirectory为null, 采用默认目录/data/dalvik-cache/
- DexClassLoader: 可以从包含classes.dex的jar或者apk中，加载类的类加载器, 可用于执行动态加载,但必须是app私有可写目录来缓存odex文件. 能够加载系统没有安装的apk或者jar文件， 因此很多插件化方案都是采用DexClassLoader;
- BaseDexClassLoader: 比较基础的类加载器, PathClassLoader和DexClassLoader都只是在构造函数上对其简单封装而已.
- BootClassLoader: 作为父类的类构造器。



------

[]: https://www.jianshu.com/p/3afa47e9112e	"Android类加载机制的细枝末节"
[]: http://gityuan.com/2017/03/19/android-classloader/	"Android类加载器ClassLoader"

