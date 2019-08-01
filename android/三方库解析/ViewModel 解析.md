# ViewModel  解析

## 核心思想

数据驱动视图。（观察者模式）



## 特点

### 1. 规范的抽象接口

Google官方建议`ViewModel`尽量保证 **纯的业务代码**，不要持有任何View层(`Activity`或者`Fragment`)或`Lifecycle`的引用，这样保证了`ViewModel`内部代码的可测试性，避免因为`Context`等相关的引用导致测试代码的难以编写（比如，MVP中Presenter层代码的测试就需要额外成本，比如依赖注入或者Mock，以保证单元测试的进行）。

### 2. 便于保存数据

由系统响应用户交互或者重建组件，用户无法操控。当组件被销毁并重建后，原来组件相关的数据也会丢失——最简单的例子就是**屏幕的旋转**，如果数据类型比较简单，同时数据量也不大，可以通过`onSaveInstanceState()`存储数据，组件重建之后通过`onCreate()`，从中读取`Bundle`恢复数据。但如果是大量数据，不方便序列化及反序列化，则上述方法将不适用。

`ViewModel`的扩展类则会在这种情况下自动保留其数据，如果`Activity`被重新创建了，它会收到被之前相同`ViewModel`实例。当所属`Activity`终止后，框架调用`ViewModel`的`onCleared()`方法释放对应资源：

![img](https://upload-images.jianshu.io/upload_images/7293029-0b71443385ac3bdc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/962/format/webp)

这样看来，`ViewModel`是有一定的 **作用域** 的，它不会在指定的作用域内生成更多的实例，从而节省了更多关于 **状态维护**（数据的存储、序列化和反序列化）的代码。

`ViewModel`在对应的 **作用域** 内保持生命周期内的 **局部单例**，这就引发一个更好用的特性，那就是`Fragment`、`Activity`等UI组件间的通信。

### 3. 便于UI组件之间的通信

一个`Activity`中的多个`Fragment`相互通讯是很常见的，如果`ViewModel`的实例化作用域为`Activity`的生命周期，则两个`Fragment`可以持有同一个ViewModel的实例。`ViewModel`可以实现**数据状态之间的共享**

```java
public class AFragment extends Fragment {
    private CommonViewModel model;
    public void onActivityCreated() {
        model = ViewModelProviders.of(getActivity()).get(CommonViewModel.class);
    }
}

public class BFragment extends Fragment {
    private CommonViewModel model;
    public void onActivityCreated() {
        model = ViewModelProviders.of(getActivity()).get(CommonViewModel.class);
    }
}
```

## 

## ViewModel 本质

`ViewModel`层的根本职责，就是负责维护**UI的状态**，追根究底就是维护对应的**数据**——毕竟，无论是MVP还是MVVM，UI的展示就是对数据的渲染。

- 1.定义了`ViewModel`的基类，并建议通过持有`LiveData`维护保存数据的状态；
- 2.`ViewModel`不会随着`Activity`的屏幕旋转而销毁，减少了**维护状态**的代码成本（数据的存储和读取、序列化和反序列化）；
- 3.在对应的作用域内，保正只生产出对应的唯一实例，**多个Fragment维护相同的数据状态**，极大减少了UI组件之间的**数据传递**的代码成本。

现在我们对于`ViewModel`的职责和思想都有了一定的了解，按理说接下来我们应该阐述如何使用`ViewModel`了，但我想先等等，因为我觉得相比API的使用，**掌握其本质的思想**会让你在接下来的代码实践中**如鱼得水**。



总结

- 数据层内容发生变化，视图层能自动检测到数据变化，自动进行更新
- 减少`View`层和`Model`之间相互调用的代码，将工作重心放在业务逻辑上
- `ViewModel`更像一个状态存储器



------

[]: <https://www.jianshu.com/p/59adff59ed29>	"Android官方架构组件ViewModel:从前世今生到追本溯源"