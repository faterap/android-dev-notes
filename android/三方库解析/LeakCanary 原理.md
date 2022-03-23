# LeakCanary 原理

在 `Activity` 或 `Fragment` 被销毁后, 将他们的引用包装成一个 `WeakReference`, 然后将这个 `WeakReference` 关联到一个 `ReferenceQueue` 。查看`ReferenceQueue`中是否含有 `Activity` 或 `Fragment` 的引用。如果没有 **触发GC** 后再次查看。还是没有的话就说明回收成功, 否则可能发生了泄露. 这时候开始 `dump` 内存的信息,并分析泄露的引用链。

