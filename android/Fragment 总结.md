# Fragment 总结

### 1. 生命周期

![img](https://images2015.cnblogs.com/blog/462303/201510/462303-20151013221141007-1433074538.jpg)

### 2. 回退栈

通过获得当前 Activity/Fragment 的 FragmentManager/ChildFragmentManager，进而拿到事务的实现类 BackStackRecord，它将目标 Fragment 构造成 Ops（包装Fragment 和状态信息），然后提交给 FragmentManager 处理。

如果是异步提交，就通过 Handler 发送 Runnable 任务，FragmentManager 拿到任务后，先处理 Ops 状态，然后调用 moveToState() 方法根据状态调用 Fragment 对应的生命周期方法，从而达到 Fragment 的添加、布局的替换隐藏等。

### 3. 数据保存



### 4. 懒加载



### 5. 动画



### 6. commit & commitAllowStateLoss()

在`Activity`和`FragmentActivity`内的`onSaveInstanceState`方法保存了`fragment`的状态。在`onSaveInstanceState`方法后调用`commit`和`commitAllowingStateLoss`会引起一种问题：因内存不足而把不显示在前台的 activity （带有 fragment）销毁，之后用户再回到此 activity 页面时，是会丢失在`onSaveInstanceState`后调用`commit`方法提交的页面状态信息！
不同的只是调用`commit`会报错，调用`commitAllowingStateLoss`不报错（睁一只眼闭一只眼）



1. 一般情况下，尽量提早调用 commit 方法，比如说`onCreate()`；
2. 异步回调中避免使用`commit`；
3. 不得已的情况，可以使用`commitAllowingStateLoss`替代`commit`。毕竟报错奔溃，比页面状态信息丢失更严重；

------

[]: https://blog.csdn.net/u011240877/article/details/78132990#_BackStackRecord_506	"Android 进阶17：Fragment FragmentManager FragmentTransaction 深入理解"

https://www.jianshu.com/p/d9143a92ad94

