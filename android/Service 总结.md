# Service 总结

## 生命周期

![img](https://img-blog.csdn.net/20150717212915626?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

## startService

**onCreate()**
- 如果service没被创建过，调用startService()后会执行onCreate()回调；
- 如果service已处于运行中，调用startService()不会执行onCreate()方法。
- 也就是说，onCreate()只会在第一次创建service时候调用，多次执行startService()不会重复调用onCreate()，此方法适合完成一些初始化工作。

**onStartCommand()**
如果多次执行了Context的startService()方法，那么Service的onStartCommand()方法也会相应的多次调用。onStartCommand()方法很重要，我们在该方法中根据传入的Intent参数进行实际的操作，比如会在此处创建一个线程用于下载数据或播放音乐等。

**onBind()**
Service中的onBind()方法是抽象方法，Service类本身就是抽象类，所以onBind()方法是必须重写的，即使我们用不到。

**onDestory()**
在销毁的时候会执行Service该方法。

## bindService

1. bindService启动的服务和调用者之间是典型的**client-server**模式。调用者是client，service则是server端。service只有一个，但绑定到service上面的client可以有一个或很多个。这里所提到的client指的是组件，比如某个Activity。
2. client可以通过IBinder接口获取Service实例**，从而实现在client端直接调用Service中的方法以实现灵活交互，这在通过startService方法启动中是无法实现的。**
3. **bindService启动服务的生命周期与其绑定的client息息相关。当client销毁时，client会自动与Service解除绑定。当然，client也可以明确调用Context的unbindService()方法与Service解除绑定。**当没有任何client与Service绑定时，Service会自行销毁**。