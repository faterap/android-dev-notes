# CoroutineScope和CoroutineContext

- CoroutineContext 用來提供每個 Coroutine 執行的資訊 (Job, CoroutineExceptionHandler…這些實作 CoroutineContext.Element 的類別)
- CoroutineScope 定義了 coroutines 之間的階層關係，透過 CoroutineScope 的 functions 建構 coroutine 的同時，也會把 parent-child 關係安排好

![coroutine_scopes_by_elizavor](https://jchu.cc/2020/03/22-coroutine/coroutine_scopes_by_elizavor.png)





参考资料：

http://blog.chengyunfeng.com/?p=1086