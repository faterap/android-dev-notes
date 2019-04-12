# Activity 启动模式



## 任务栈

每个应用都有一个任务栈，是用来存放Activity的，功能类似于函数调用的栈，先后顺序代表了Activity的出现顺序

## Task Affinity

### 定义

Activity与Task的吸附关系，也就是该Activity属于哪个Task。一般情况下在同一个应用中，启动的Activity都在同一个Task中，它们在该Task中度过自己的生命。每个Activity都有taskAffinity属性，这个属性指出了它希望进入的Task。如果一个Activity没有显式的指明taskAffinity，那么它的这个属性就等于Application指明的taskAffinity，如果Application也没有指明，那么该taskAffinity的值就等于应用的包名。

## Intent flags

当同时使用launchMode和上面的FLAG_ACTIVITY_NEW_TASK等标签时，以FLAG_ACTIVITY_NEW_TASK为标准。也就是说，代码的优先级比manifest中配置文件的优先级更高。

### Intent.FLAG_ACTIVITY_NEW_TASK

**很少单独使用FLAG_ACTIVITY_NEW_TASK，通常与FLAG_ACTIVITY_CLEAR_TASK或FLAG_ACTIVITY_CLEAR_TOP联合使用。因为单独使用该属性会导致奇怪的现象，通常达不到我们想要的效果。**

Intent.FLAG_ACTIVITY_NEW_TASK是启动模式中最关键的一个Flag，依据该Flag启动模式可以分成两类，设置了该属性的与未设置该属性的，**对于非Activity启动的Activity**（比如Service中启动的Activity）需要显式的设置Intent.FLAG_ACTIVITY_NEW_TASK，而singleTask及singleInstance在AMS中被预处理后，**隐形的设置了Intent.FLAG_ACTIVITY_NEW_TASK**，而启动模式是standard及singletTop的Activity不会被设置Intent.FLAG_ACTIVITY_NEW_TASK，除非通过显式的intent setFlag进行设置。

```java
  Intent intent = new Intent(BackGroundService.this, A.class);
  intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
  startActivity(intent);    
```


注意：

1. 设置该`flag`时需要同时设置`taskAffinity`属性，否则将不会生效；

2. 设置了该`flag`之后，结果分好几个场景：

- Activity的taskAffinity属性的Task栈是否存在
- 如果存在，要看Activity是否存已经存在于该Task
- 如果已经存在于该taskAffinity的Task，要看其是不是其rootActivity
- 如果是其rootActivity，还要看启动该Activity的Intent是否跟当前intent相等

它与launchMode="singleTop"具有相同的行为。实际上，的确如此！单独的使用FLAG_ACTIVITY_SINGLE_TOP，就能达到和launchMode="singleTop"一样的效果。

### Intent.FLAG_ACTIVITY_CLEAR_TOP

顾名思义，FLAG_ACTIVITY_CLEAR_TOP的作用清除"包含Activity的task"中位于该Activity实例之上的其他Activity实例。FLAG_ACTIVITY_CLEAR_TOP和FLAG_ACTIVITY_NEW_TASK两者同时使用，就能达到和launchMode="singleTask"一样的效果。

### Intent.FLAG_ACTIVITY_CLEAR_TASK

FLAG_ACTIVITY_CLEAR_TASK的作用包含Activity的task。使用FLAG_ACTIVITY_CLEAR_TASK时，通常会包含FLAG_ACTIVITY_NEW_TASK。这样做的目的是启动Activity时，清除之前已经存在的Activity实例所在的task；这自然也就清除了之前存在的Activity实例。

## 启动模式

### standard

![这里写图片描述](https://img-blog.csdn.net/20170303203558620?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSVRlcm1lbmc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

它是默认模式。在该模式下，Activity可以拥有多个实例，并且这些实例既可以位于同一个task，也可以位于不同的task。

### singleTop

![这里写图片描述](https://img-blog.csdn.net/20170303204628314?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSVRlcm1lbmc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

该模式下，在同一个task中，如果存在该Activity的实例，并且该Activity实例位于栈顶(即，该Activity位于前端)，则调用startActivity()时，不再创建该Activity的示例；而仅仅只是调用Activity的onNewIntent()。否则的话，则新建该Activity的实例，并将其置于栈顶。

### singleTask

![这里写图片描述](https://img-blog.csdn.net/20170303205023925?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSVRlcm1lbmc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

> 在singleTask模式下，如果是第一次创建该Activity实例时，则会新建task并将该Activity添加到该task中。否则(该Activity的实例已存在)，则会打开已有的Activity实例，并调用Activity的onNewIntent()方法，而不会新建Activity实例。在任意时刻，最多只会有该一个Activity实例存在。

顾名思义，只容许有一个包含该Activity实例的task存在。

如果要激活的那个Activity在任务栈中存在该实例，则不需要创建，只需要把此Activity放入栈顶，并把该Activity以上的Activity实例都pop。

### singleInstance
![singleInstance](https://inthecheesefactory.com/uploads/source/launchMode/singleInstance.jpg)

> 在该模式下，只允许有一个Activity实例。当第一次创建该Activity实例时，会新建一个task，并将该Activity添加到该task中。注意：该task只能容纳该Activity实例，不会再添加其他的Activity实例！如果该Activity实例已经存在于某个task，则直接跳转到该task。

顾名思义，是单一实例的意思，即任意时刻只允许存在唯一的Activity实例，而且该Activity所在的task不能容纳除该Activity之外的其他Activity实例。

如果应用1的任务栈中创建了MainActivity实例，如果应用2也要激活MainActivity，则不需要创建，两应用共享该Activity实例。

### singleTask 和 singleInstance 比较：

**相同之处**：

任意时刻，最多只允许存在一个实例。

**不同之处**：
1. singleTask受android:taskAffinity属性的影响，而singleInstance不受android:taskAffinity的影响。 
2. singleTask所在的task中能有其它的Activity，而singleInstance的task中不能有其他Activity。
3. 当跳转到singleTask类型的Activity，并且该Activity实例已经存在时，会删除该Activity所在task中位于该Activity之上的全部Activity实例；而跳转到singleInstance类型的Activity，并且该Activity已经存在时，不需要删除其他Activity，因为它所在的task只有该Activity唯一一个Activity实例。

## 注意

**上面的场景仅仅适用于Activity启动Activity，并且采用的都是默认Intent，没有额外添加任何Flag**



------

[]: http://wangkuiwu.github.io/2014/06/26/IntentFlag/	"Android 之Activity启动模式(二)之 Intent的Flag属性"
[]: https://juejin.im/post/59b0f25551882538cb1ecae1#heading-1	"Android面试官装逼失败之：Activity的启动模式"