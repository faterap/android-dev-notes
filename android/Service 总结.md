# Service 总结

## 0x1 生命周期

![img](https://img-blog.csdn.net/20150717212915626?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

## 0x2 API

### 2.1 startService

**onCreate()**

- 如果service没被创建过，调用startSer                                        vice()后会执行onCreate()回调；
- 如果service已处于运行中，调用startService()不会执行onCreate()方法。
- 也就是说，onCreate()只会在第一次创建service时候调用，多次执行startService()不会重复调用onCreate()，此方法适合完成一些初始化工作。

**onStartCommand()**
如果多次执行了Context的startService()方法，那么Service的onStartCommand()方法也会相应的多次调用。onStartCommand()方法很重要，我们在该方法中根据传入的Intent参数进行实际的操作，比如会在此处创建一个线程用于下载数据或播放音乐等。

**onBind()**
Service中的onBind()方法是抽象方法，Service类本身就是抽象类，所以onBind()方法是必须重写的，即使我们用不到。

**onDestory()**
在销毁的时候会执行Service该方法。

### 2.2 bindService

1. bindService启动的服务和调用者之间是典型的**client-server**模式。调用者是client，service则是server端。service只有一个，但绑定到service上面的client可以有一个或很多个。这里所提到的client指的是组件，比如某个Activity。
2. client可以通过IBinder接口获取Service实例**，从而实现在client端直接调用Service中的方法以实现灵活交互，这在通过startService方法启动中是无法实现的。**
3. **bindService启动服务的生命周期与其绑定的client息息相关。当client销毁时，client会自动与Service解除绑定。当然，client也可以明确调用Context的unbindService()方法与Service解除绑定。**当没有任何client与Service绑定时，Service会自行销毁**。

## 0x3 前台服务和后台服务

### 3.0 前台应用和后台应用

一个前台应用必须满足如下某一个条件:

1. 有可见的Activity。无论是resume状态还是pause状态，只要可见就行。
2. 有前台Service。
3. 有其它app连接到当前app，通过绑定Service或者使用ContentProvider。

### 3.1 前台服务

前台Service会发送一条通知，让用户察觉到有一个Service正在运行，而后台Service没有通知，用户不会察觉到有一个Service正在运行。

### 3.2 后台服务

在 Android 8.0 之前，创建前台 Service 的方式通常是先创建一个后台 Service，然后将该 Service 推到前台。 Android 8.0 有一项复杂功能：系统不允许后台应用创建后台 Service。 因此，Android 8.0 引入了一种全新的方法，即 `startForegroundService()`，以在前台启动新 Service。 在系统创建 Service 后，应用有五秒的时间来调用该 Service 的`startForeground()` 方法以显示新 Service 的用户可见通知。 如果应用在此时间限制内*未*调用 `startForeground()`，则系统将停止此 Service 并声明此应用为 [ANR](https://developer.android.com/training/articles/perf-anr?hl=zh-cn)。

当一个app处于前台时，它可以随意创建和使用后台服务。当这个app进入后台，它只有几分钟的窗口期可以创建和使用服务。当这个窗口期结束后，系统认为这个app进入了空闲状态，此时系统会停止app的后台服务。



https://juejin.cn/post/6854573221954453512



### 3.3 Android 11后限制

https://developer.android.com/guide/components/foreground-services#restrictions-exemptions

https://developer.android.google.cn/guide/topics/media/sharing-audio-input#pre-behavior