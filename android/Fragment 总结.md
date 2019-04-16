# Fragment 总结

## 生命周期

![img](https://images2015.cnblogs.com/blog/462303/201510/462303-20151013221141007-1433074538.jpg)

## 回退栈

通过获得当前 Activity/Fragment 的 FragmentManager/ChildFragmentManager，进而拿到事务的实现类 BackStackRecord，它将目标 Fragment 构造成 Ops（包装Fragment 和状态信息），然后提交给 FragmentManager 处理。

如果是异步提交，就通过 Handler 发送 Runnable 任务，FragmentManager 拿到任务后，先处理 Ops 状态，然后调用 moveToState() 方法根据状态调用 Fragment 对应的生命周期方法，从而达到 Fragment 的添加、布局的替换隐藏等。

## 数据保存



## 懒加载



------

[]: https://blog.csdn.net/u011240877/article/details/78132990#_BackStackRecord_506	"Android 进阶17：Fragment FragmentManager FragmentTransaction 深入理解"

