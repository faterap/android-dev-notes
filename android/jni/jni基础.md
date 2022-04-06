# jni基础

### 1. JNIEnv

首先理解一下JNIEnv，它表示了java在native层的语言环境，是一个指针，几乎所有JNI有关的函数都通过这个指针来调用，例如上面的`env->RegisterNatives(...)`。

在native方法中，第一个亘古不变的参数env便是这个JNIEnv，它是线程内部存储，**不能多个线程共享一个JNIEnv** 。如果你的code无法直接拿到env或者要多线程使用，可以通过JavaVM来获取，最典型的使用场景就是c++层的异步callback函数。这里的示例等讲完JavaVM再给出。

### 2. JavaVM

JavaVM可以理解为c++层可以获取的Java的虚拟机。一个进程只有一个JavaVM，所有线程共享它，它是一个全局变量。
他的获得时机只有2个：

1. 在加载动态链接库的时候，JVM会调用`JNI_OnLoad(JavaVM* jvm, void* reserved)`（如果定义了该函数）。第一个参数会传入JavaVM指针。
2. 在native code中调用`JNI_CreateJavaVM(&jvm, (void**)&env, &vm_args)`

由于Android限制只有能一个JavaVM，所以第二种方式并不常用，因为你需要一直维护它的生命周期，不断的去调用`JNI_DestroyJavaVM()`，很容易崩溃。而既然他是一个全局变量，所以更常用的方式是在时机1的时候，把它存下来。

关于时机1，这里又多出来一个新的话题，就是加载动态链接库，长话短说，就是当java层执行`System.loadLibrary("NativeLib");`的时候，这个`NativeLib.so被加载`，此时如果它定义了`JNI_OnLoad(JavaVM* jvm, void* reserved)`，则该函数会被调用。