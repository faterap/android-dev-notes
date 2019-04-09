# RxJava 原理

## 角色

### 抽象主题

ObservableSource

```Java
public interface ObservableSource<T> {

    /**
     * Subscribes the given Observer to this ObservableSource instance.
     * @param observer the Observer, not null
     * @throws NullPointerException if {@code observer} is null
     */
    void subscribe(@NonNull Observer<? super T> observer);
}
```

这里很明显了，ObservableSource 就是抽象主题（被观察者）的角色。按照之前观察者模式中约定的职责，subscribe 方法就是用来实现订阅观察者（Observer）角色的功能。从这里我们也可以看出，抽象观察者的角色就是Observer了。

### 具体主题

回过头来继续看Observable，他实现了ObservableSource接口，并且实现了其subscribe方法，但是它并没有真正的去完成**主题**和**观察者**之间的订阅关系，而是把这个功能，转接给了另一个抽象方法subscribeActual（具体细节后面分析）。

因此，Observable依旧是一个抽象类，我们知道**抽象类是不能被实例化的**，因此从理论上来说，他好像不能作为具体主题的角色。其实不然，Observable内部提供了create,defer,fromXXX,repeat,just等一系列**创建型操作符**， 用来创建各种Observable。

### 抽象观察者

```java
public interface Observer<T> {

    void onSubscribe(@NonNull Disposable d);

    void onNext(@NonNull T t);

    void onError(@NonNull Throwable e);

    void onComplete();

}
```



### 具体观察者

大家平时使用应该都是直接用new一个Observer的实例。RxJava内部有很多Observer的子类。



## 订阅

## 事件发送

## 线程切换

### subscribeOn

原理：

直接把订阅方法放在了一个Runnable中去执行，这样就一旦这个Runnable在某个子线程执行，那么上游所有事件只能在这个子线程中执行了。


**多次用 subscribeOn 指定上游线程为什么只有第一次有效?**，看完通篇其实也很好理解了，因为上游Observable只有一个任务，就是subscribe(准确的来说是subscribeActual()），而subscribeOn 要做的事情就是把上游任务切换到一个指定线程里，那么一旦被切换到了某个指定的线程里，后面的切换不就是没有意义了吗。



通过 subscribeOn 我们完成了以下操作：

- 创建了一个 ObservableSubscribeOn 对象，本质上来说他就是一个Observable，他同时实现了 AbstractObservableWithUpstream（HasUpstreamObservableSource ）这样一个接口，是他变了一个**拥有上游**的Observeable。
- 在 ObservableSubscribeOn 的 subscribeActual 方法中

### observeOn



### 区别

subscribeOn() 原理：

![subscribeOn() 原理](http://ww4.sinaimg.cn/mw1024/52eb2279jw1f2rxcynbsuj20ha0d7wg2.jpg)

observeOn() 原理：

![observeOn() 原理](http://ww4.sinaimg.cn/mw1024/52eb2279jw1f2rxd05lttj20hj0cyabl.jpg)

`subscribeOn()` 和 `observeOn()` 都做了线程切换的工作（图中的 "schedule..." 部位）。不同的是， `subscribeOn()`的线程切换发生在 `OnSubscribe` 中，即在它通知上一级 `OnSubscribe` 时，这时事件还没有开始发送，因此 `subscribeOn()` 的线程控制可以从事件发出的开端就造成影响；而 `observeOn()` 的线程切换则发生在它内建的 `Subscriber` 中，即发生在它即将给下一级 `Subscriber` 发送事件时，因此 `observeOn()` 控制的是它后面的线程。

## 总结

- 观察者订阅主题后，主题才会开始发送事件
- RxJava中Observer通过onSubscribe获取了发送事件中的Disposable对象，这样他就可以主动的获取订阅关系中二者的状态，甚至是控制或者是中断事件序列的发送。在常规的观察者模式中，主题有权利添加订阅者，但也能是由他移除特定的订阅者，因为只有他持有所有订阅者的集合
- 抽象主题（上游）并没有直接控制onNext，onComplete,onError事件的发送，而是只关注Emitter 实例的发送，ObservableOnSubscribe接口监听ObservableEmitter对象的发送，一旦接受到此对象就会通过他开始发送具体的事件，这里可以有点观察者模式嵌套的意味。



------

[]: https://blog.csdn.net/jdsjlzx/article/details/51685769	"RxJava observeOn()与subscribeOn()的关系"

[]: https://gank.io/post/560e15be2dca930e00da1083#toc_4	"给 Android 开发者的 RxJava 详解"

