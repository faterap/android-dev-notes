# Thread

### 1. 线程状态

Java中线程的状态分为6种。

> 1. **初始(NEW)**：新创建了一个线程对象，但还没有调用start()方法。
> 2. **运行(RUNNABLE)**：Java线程中将就绪（ready）和运行中（running）两种状态笼统的称为“运行”。线程对象创建后，其他线程(比如main线程）调用了该对象的start()方法。该状态的线程位于可运行线程池中，等待被线程调度选中，获取CPU的使用权，此时处于就绪状态（ready）。就绪状态的线程在获得CPU时间片后变为运行中状态（running）。
>
> 3. **阻塞(BLOCKED)**：表示线程阻塞于锁。
> 4. **等待(WAITING)**：进入该状态的线程需要等待其他线程做出一些特定动作（通知或中断）。
> 5. **超时等待(TIMED_WAITING)**：该状态不同于WAITING，它可以在指定的时间后自行返回。
>
> 6. **终止(TERMINATED)**：表示该线程已经执行完毕。

这6种状态定义在Thread类的State枚举中，可查看源码进行一一对应。





### ![img](https://aijishu.com/img/bVcVo)

1. NEW: 线程创建之后，但是还没有启动(not yet started)。这时候它的状态就是NEW
2. RUNNABLE: 正在Java虚拟机下跑任务的线程的状态。在RUNNABLE状态下的线程可能会处于等待状态， 因为它正在等待一些系统资源的释放，比如IO
3. BLOCKED: 阻塞状态，等待锁的释放，比如线程A进入了一个synchronized方法，线程B也想进入这个方法，但是这个方法的锁已经被线程A获取了，这个时候线程B就处于BLOCKED状态
4. WAITING: 等待状态，处于等待状态的线程是由于执行了3个方法中的任意方法。 1. Object的wait方法，并且没有使用timeout参数; 2. Thread的join方法，没有使用timeout参数 3. LockSupport的park方法。 处于waiting状态的线程会等待另外一个线程处理特殊的行为。 再举个例子，如果一个线程调用了一个对象的wait方法，那么这个线程就会处于waiting状态直到另外一个线程调用这个对象的notify或者notifyAll方法后才会解除这个状态
5. TIMED_WAITING: 有等待时间的等待状态，比如调用了以下几个方法中的任意方法，并且指定了等待时间，线程就会处于这个状态。 1. Thread.sleep方法 2. Object的wait方法，带有时间 3. Thread.join方法，带有时间 4. LockSupport的parkNanos方法，带有时间 5. LockSupport的parkUntil方法，带有时间
6. TERMINATED: 线程中止的状态，这个线程已经完整地执行了它的任务



### InterruptedException

中断阻塞方法的操作线程并不是自身线程干的，而是其它线程。而中断操作发生之后，随后会抛出一个InterruptedException，伴随着这个异常抛出的同时，当前线程的中断状态重新被置为false。



https://www.ibm.com/developerworks/cn/java/j-jtp05236.html

[]: https://blog.csdn.net/Androidlushangderen/article/details/54984681	"遇见InterruptedException异常，怎么办？"

