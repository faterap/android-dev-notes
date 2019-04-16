# Android 线程间通信

## 种类

- 单向管道。非 Android 独有
- 共享内存通信
- 消费者生产者模型 — BlockingQueue。非 Android 独有
- 消息队列。Handler 机制
- LocalSocket



## Handler 机制

### Looper

#### Looper.prepare()

![2011091315284989](https://pic002.cnblogs.com/images/2011/315542/2011091315284989.png)



### 同步屏障机制

同步屏障可以通过MessageQueue.postSyncBarrier函数来设置：

```java
private int postSyncBarrier(long when) {
        // Enqueue a new sync barrier token.
        // We don't need to wake the queue because the purpose of a barrier is to stall it.
        synchronized (this) {
            final int token = mNextBarrierToken++;
            final Message msg = Message.obtain();
            msg.markInUse();
            msg.when = when;
            msg.arg1 = token;
            Message prev = null;
            Message p = mMessages;
            if (when != 0) {
                while (p != null && p.when <= when) {
                    prev = p;
                    p = p.next;
                }
            }
            if (prev != null) { // invariant: p == prev.next
                msg.next = p;
                prev.next = msg;
            } else {
                msg.next = p;
                mMessages = msg;
            }
            return token;
        }
    }
```
可以看到，该函数仅仅是创建了一个Message对象并加入到了消息链表中。乍一看好像没什么特别的，但是这里面有一个很大的不同点是该**Message没有target**。

我们通常都是通过Handler发送消息的，Handler中发送消息的函数有post***、sendEmptyMessage***以及sendMessage***等函数，而这些函数最终都会调用enqueueMessage函数：

```java
//Handler.java
private boolean enqueueMessage(MessageQueue queue, Message msg, long uptimeMillis) {
    msg.target = this;
    //...
    return queue.enqueueMessage(msg, uptimeMillis);
}
```

可以看到enqueueMessage为msg设置了target字段。所以，从代码层面上来讲，**同步屏障就是一个Message，一个target字段为空的Message**。

当设置了同步屏障之后，**next函数将会忽略所有的同步消息**，返回异步消息。设置了同步屏障之后，Handler只会处理异步消息。再换句话说，同步屏障为Handler消息机制增加了一种简单的优先级机制，异步消息的优先级要高于同步消息。

#### 应用

Android应用框架中为了更快的响应UI刷新事件在ViewRootImpl.scheduleTraversals中使用了同步屏障

```java
void scheduleTraversals() {
    if (!mTraversalScheduled) {
        mTraversalScheduled = true;
        //设置同步障碍，确保mTraversalRunnable优先被执行
        mTraversalBarrier = mHandler.getLooper().getQueue().postSyncBarrier();
        //内部通过Handler发送了一个异步消息
        mChoreographer.postCallback(
                Choreographer.CALLBACK_TRAVERSAL, mTraversalRunnable, null);
        if (!mUnbufferedInputDispatch) {
            scheduleConsumeBatchedInput();
        }
        notifyRendererOfFramePending();
        pokeDrawLockIfNeeded();
    }
}
```


mTraversalRunnable调用了performTraversals执行measure、layout、draw。为了让mTraversalRunnable尽快被执行，在发消息之前调用MessageQueue.postSyncBarrier设置了同步屏障。


## 总结

- Looper调用prepare()进行初始化，创建了一个与当前线程对应的Looper对象（**通过ThreadLocal实现**），并且初始化了一个与当前Looper对应的MessageQueue对象。
- Looper调用静态方法loop()开始消息循环，通过MessageQueue.next()方法获取Message对象。
  当获取到一个Message对象时，让Message的发送者（target）去处理它。
- Message对象包括数据，发送者（Handler），可执行代码段（Runnable）三个部分组成。
- Handler可以在一个已经Looper.prepare()的线程中初始化，如果线程没有初始化Looper，创建Handler对象会失败
  一个线程的执行流中可以构造多个Handler对象，它们都往同一个MQ中发消息，消息也只会分发给对应的Handler处理。
- Handler将消息发送到MQ中，Message的target域会引用自己的发送者，Looper从MQ中取出来后，再交给发送这个Message的Handler去处理。
- Message可以直接添加一个Runnable对象，当这条消息被处理的时候，直接执行Runnable.run()方法。

