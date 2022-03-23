# Crash/ANR监控

#### 1. Java Crash

Java的Crash监控非常简单，Java中的Thread定义了一个接口： UncaughtExceptionHandler ；用于处理未捕获的异常导致线程的终止（注意：catch了的是捕获不到的），当我们的应用crash的时候，就会走UncaughtExceptionHandler.uncaughtException ，在该方法中可以获取到异常的信息，我们通过Thread.setDefaultUncaughtExceptionHandler 该方法来设置线程的默认异常处理器，我们可以将异常信息保存到本地或者是上传到服务器，方便我们快速的定位问题。



#### 2. Native Crash

信号机制是Linux进程间通信的一种重要方式，Linux信号一方面用于正常的进程间通信和同步，另一方面它还负责监控系统异常及中断。当应用程序运行异常时，Linux内核将产生错误信号并通知当前进程。当前进程在接收到该错误信号后，可以有三种不同的处理方式。

忽略该信号；
捕捉该信号并执行对应的信号处理函数（信号处理程序）；
执行该信号的缺省操作（如终止进程）；
当Linux应用程序在执行时发生严重错误，一般会导致程序崩溃。其中，Linux专门提供了一类crash信号，在程序接收到此类信号时，缺省操作是将崩溃的现场信息记录到核心文件，然后终止进程。
常见崩溃信号列表：

信号	描述
SIGSEGV	内存引用无效。
SIGBUS	访问内存对象的未定义部分。
SIGFPE	算术运算错误，除以零。
SIGILL	非法指令，如执行垃圾或特权指令。
SIGSYS	糟糕的系统调用。
SIGXCPU	超过CPU时间限制。
SIGXFSZ	文件大小限制。

一般的出现崩溃信号，Android系统默认缺省操作是直接退出我们的程序。但是系统允许我们给某一个进程的某一个特定信号注册一个相应的处理函数（signal），即对该信号的默认处理动作进行修改。因此NDK Crash的监控可以采用这种信号机制，捕获崩溃信号执行我们自己的信号处理函数从而捕获NDK Crash。



#### 3. ANR

那么哪些场景会造成ANR呢？

- Service Timeout:比如前台服务在20s内未执行完成；
- BroadcastQueue Timeout：比如前台广播在10s内未执行完成
- ContentProvider Timeout：内容提供者,在publish过超时10s;
- InputDispatching Timeout: 输入事件分发超时5s，包括按键和触摸事件。



1、当监控线程发现主线程卡死时，主动向系统发送SIGNAL_QUIT信号。
2、等待/data/anr/traces.txt文件生成。
3、文件生成以后进行上报。

ANR监控：https://zhuanlan.zhihu.com/p/119293585







https://www.cnblogs.com/huansky/p/14954020.html