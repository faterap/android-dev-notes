# SharedPreferences的坑

### 1. 背景

SharedPreference 是一个轻量级的数据存储方式，使用起来也非常方便，以键值对的形式存储在本地，初始化 SharedPreference 的时候，会将整个文件内容加载内存中，因此会带来以下问题：

- 通过 `getXXX()` 方法获取数据，可能会导致主线程阻塞
- SharedPreference 不能保证类型安全
- SharedPreference 加载的数据会一直留在内存中，浪费内存
- `apply()` 方法虽然是异步的，可能会发生 ANR，在 8.0 之前和 8.0 之后实现各不相同
- `apply()` 方法无法获取到操作成功或者失败的结果



### 2. 缺陷

#### SP 不能用于跨进程通信

我们在创建 SP 实例的时候，需要传入一个 `mode`，如下所示：

```
val sp = getSharedPreferences("ByteCode", Context.MODE_PRIVATE) 
复制代码
```

Context 内部还有一个 `mode` 是 `MODE_MULTI_PROCESS`，我们来看一下这个 `mode` 做了什么

```
public SharedPreferences getSharedPreferences(File file, int mode) {
    if ((mode & Context.MODE_MULTI_PROCESS) != 0 ||
        getApplicationInfo().targetSdkVersion < android.os.Build.VERSION_CODES.HONEYCOMB) {
        // 重新读取 SP 文件内容
        sp.startReloadIfChangedUnexpectedly();
    }
    return sp;
}
复制代码
```

在这里就做了一件事，当遇到 `MODE_MULTI_PROCESS` 的时候，会重新读取 SP 文件内容，并不能用 SP 来做跨进程通信。







https://juejin.cn/post/6881442312560803853#heading-5