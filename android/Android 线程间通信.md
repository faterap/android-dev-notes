# Android 线程间通信

## 种类

- 单向管道。非 Android 独有
- 共享内通信
- 消费者生产者模型 — BlockingQueue。非 Android 独有
- 消息队列。Handler 机制



## Handler 机制

### Looper

#### Looper.prepare()

![2011091315284989](https://pic002.cnblogs.com/images/2011/315542/2011091315284989.png)





## 总结

- Looper调用prepare()进行初始化，创建了一个与当前线程对应的Looper对象（**通过ThreadLocal实现**），并且初始化了一个与当前Looper对应的MessageQueue对象。
- Looper调用静态方法loop()开始消息循环，通过MessageQueue.next()方法获取Message对象。
  当获取到一个Message对象时，让Message的发送者（target）去处理它。
- Message对象包括数据，发送者（Handler），可执行代码段（Runnable）三个部分组成。
- Handler可以在一个已经Looper.prepare()的线程中初始化，如果线程没有初始化Looper，创建Handler对象会失败
  一个线程的执行流中可以构造多个Handler对象，它们都往同一个MQ中发消息，消息也只会分发给对应的Handler处理。
- Handler将消息发送到MQ中，Message的target域会引用自己的发送者，Looper从MQ中取出来后，再交给发送这个Message的Handler去处理。
- Message可以直接添加一个Runnable对象，当这条消息被处理的时候，直接执行Runnable.run()方法。

