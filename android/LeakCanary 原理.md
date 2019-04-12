# LeakCanary 原理

该队列的具体作用就是当发生 GC 后，WeakReference 所持有的对象如果被回收就会进入该队列，所以只要在 activity onDestory 时，把 Activity 对象绑定在 WeakReference 中，然后手动执行一次 GC，然后观察 ReferenceQuery 中是不是包含对应的 Activity 对象，如果不包含，说明 Activity 被强引用，也就是发生了内存泄漏。