# Android 内存优化

## 背景

Android中有Native Heap和Dalvik Heap。Android的Native Heap言理论上可分配的空间取决了硬件RAM，而对于每个进程的Dalvik Heap都是有大小限制的。

Android Dalvik Heap与原生Java一样，将堆的内存空间分为三个区域，Young Generation，Old Generation， Permanent Generation。

最近分配的对象会存放在Young Generation区域，当这个对象在这个区域停留的时间达到一定程度，它会被移动到Old Generation，最后累积一定时间再移动到Permanent Generation区域。系统会根据内存中不同的内存数据类型分别执行不同的gc操作。

## 问题

### 内存泄露

- 单例（主要原因还是因为一般情况下单例都是全局的，有时候会引用一些实际生命周期比较短的变量，导致其无法释放）
- 静态变量（同样也是因为生命周期比较长）
- Handler内存泄露
- 匿名内部类（匿名内部类会引用外部类，导致无法释放，比如各种回调）
- 资源使用完未关闭（BraodcastReceiver，ContentObserver，File，Cursor，Stream，Bitmap）

#### 工具使用

- LeakCanary
- MAT



### 图片资源分辨率

举个例子，对于一张1280×720的图片，如果放在xhdpi，那么xhdpi的设备拿到的大小还是1280×720而xxhpi的设备拿到的可能是1920×1080，这两种情况在内存里的大小分别为：3.68M和8.29M，相差4.61M，在移动设备来说这几M的差距还是很大的。

### 图片压缩

BitmapFactory.Options

### 缓存池大小

默认缓存池设置太大了会导致浪费内存，设置小了又会导致图片经常被回收，所以需要根据每个App的情况，以及设备的分辨率，内存计算出一个比较合理的初始值，可以参考Glide的做法。

### 内存抖动

一个很经典的案例是string拼接创建大量小的对象(比如在一些频繁调用的地方打字符串拼接的log的时候)

### 其他

- **数据结构优化**。ArrayMap及SparseArray是android的系统API，是专门为移动设备而定制的。用于在一定情况下取代HashMap而达到节省内存的目的,具体性能见HashMap，ArrayMap，SparseArray源码分析及性能对比[10]，对于key为int的HashMap尽量使用SparceArray替代，大概可以省30%的内存，而对于其他类型，ArrayMap对内存的节省实际并不明显，10%左右，但是数据量在1000以上时，查找速度可能会变慢。
- **枚举**，Android平台上枚举是比较争议的，在较早的Android版本，使用枚举会导致包过大，在个例子里面，使用枚举甚至比直接使用int包的size大了10多倍 在stackoverflow上也有很多的讨论, 大致意思是随着虚拟机的优化，目前枚举变量在Android平台性能问题已经不大，而目前Android官方建议，使用枚举变量还是需要谨慎，因为枚举变量可能比直接用int多使用2倍的内存。
- **ListView复用**，这个大家都知道，getView里尽量复用conertView,同时因为getView会频繁调用，要避免频繁地生成对象
- **谨慎使用多进程**，现在很多App都不是单进程，为了保活，或者提高稳定性都会进行一些进程拆分，而实际上即使是空进程也会占用内存(1M左右)，对于使用完的进程，服务都要及时进行回收。
- **尽量使用系统资源**，系统组件，图片甚至控件的id
- **减少view的层级**，对于可以 延迟初始化的页面，使用viewstub
- **数据相关**：序列化数据使用protobuf可以比xml省30%内存，慎用shareprefercnce，因为对于同一个sp，会将整个xml文件载入内存，有时候为了读一个配置，就会将几百k的数据读进内存，[数据库](http://lib.csdn.net/base/14)字段尽量精简，只读取所需字段。
- **dex优化**，代码优化，谨慎使用外部库， 有人觉得代码多少于内存没有关系，实际会有那么点关系，现在稍微大一点的项目动辄就是百万行代码以上，多dex也是常态，不仅占用rom空间，实际上运行的时候需要加载dex也是会占用内存的(几M)，有时候为了使用一些库里的某个功能函数就引入了整个庞大的库，此时可以考虑抽取必要部分，开启proguard优化代码，使用Facebook redex使用优化dex(好像有不少坑)



