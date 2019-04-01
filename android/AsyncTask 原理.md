# AsyncTask 原理

## Callable

Callable与Runnable的功能大致相似，Callable中有一个call()函数，**但是call()函数有返回值**，而Runnable的run()函数不能将结果返回给客户程序。

## Future

Future就是对于具体的Runnable或者Callable任务的执行结果进行取消、查询是否完成、获取结果、设置结果操作。

## FutureTask

FutureTask则是一个RunnableFuture<V>，而RunnableFuture实现了Runnbale又实现了Futrue<V>这两个接口。

因此FutureTask既是Future、Runnable，又是包装了Callable( 如果是Runnable最终也会被转换为Callable )， 它是这两者的合体。

## AsyncTask

FutureTask + Handler



------

[]: https://blog.csdn.net/bboyfeiyu/article/details/24851847	"Java中的Runnable、Callable、Future、FutureTask的区别与示例"

