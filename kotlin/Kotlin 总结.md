# Kotlin 总结

## 类型

### 数组

## 关键字

### inline:

- 原理：编译器把实现内联函数的字节码动态插入到每次的调用点

### noinline:
- 如果你只想被（作为参数）传给一个内联函数的 lamda 表达式中只有一些进行内联，你可以用 noinline 修饰符标记一些函数参数
- 让内联函数的lambda参数不进行内联，保留一般函数的特征。

`inline fun foo(inlined: () -> Unit, noinline notInlined: () -> Unit) {`
`// ……`
`}`

### crossinline:

- 让被标记的lambda表达式不允许非局部返回

### reified:

- 可以将内联函数的泛型参数当做真实类型使用
- 必须配合 inline 来使用

### infix:
标有 infix 关键字的函数也可以使用中缀表示法（忽略该调用的点与圆括号）调用。
- 它们必须是成员函数或扩展函数；
- 它们必须只有一个参数；
- 其参数不得接受可变数量的参数且不能有默认值。

### suspend：
被该关键字修饰的函数/方法/代码块只能由协程代码或者被 `suspend` 修饰的函数/方法/代码块调用。说简单一点，`suspend fun` 只能被 `suspend fun` 调用

## lambda 表达式

### 作用域
- 和Java不一样，Kotlin允许在lambda内部访问非final变量甚至修改它们。从lambda内访问外部变量，我们称这些变量被lambda捕获。

#### 原理：
- 当捕捉final变量时，它的值和使用这个值的lambda代码一起存储。
- 对非final变量，它的值被封装在一个包装器中，这样你就可以改变这个值，而对这个包装器的引用会和lambda代码一起存储。

### 扩展：
- 如果一个类定义有一个成员函数与一个扩展函数，而这两个函数又有相同的接收者类型、相同的名字，都适用给定的参数，这种情况总是取成员函数

## Elvis operator:
`first operand ?: second operand`
It enables you to write a consise code, and works as such:
If first operand isn't null, then it will be returned. If it is null, then the second operand will be returned. This can be used to guarantee that an expression won't return a null value, as you'll provide a non-nullable value if the provided value is null.

### 委托属性：

#### lazy:
- 默认情况下，对于 lazy 属性的求值是同步锁的（synchronized）：该值只在一个线程中计算，并且所有线程会看到相同的值。

### 自定义委托属性：
- [参考链接](https://thoughtbot.com/blog/kotlin-is-dope-and-so-are-its-custom-property-delegates)

## 协程：

### 作用：
- 将回调代码转换为有序代码，避免回调地狱

 ### 挂起函数：
 - `suspend`进行标记
 - 挂起函数能够以与普通函数相同的方式获取参数和返回值，但是调用函数可能挂起协程，挂起函数挂起协程时，不会阻塞协程所在的线程。
 - 挂起函数挂起协程时，不会阻塞协程所在的线程

```Kotlin
fun postItem(item: Item) {
    GlobalScope.launch { // 创建一个新协程
        val token = requestToken()
        val post = createPost(token, item)
        processPost(post)
        // 需要异常处理，直接加上 try/catch 语句即可
    }
}
```

#### 概念
`CoroutineDispatcher`:
协程调度器，决定协程所在的线程或线程池。它可以指定协程运行于特定的一个线程、一个线程池或者不指定任何线程（这样协程就会运行于当前线程）。coroutines-core中 CoroutineDispatcher 有三种标准实现Dispatchers.Default、Dispatchers.IO，Dispatchers.Main和Dispatchers.Unconfined，Unconfined 就是不指定线程。

`CoroutineBuilder`
`CoroutineScope.launch`函数属于协程构建器 `Coroutine builders`，`Kotlin` 中还有其他几种 Builders，负责创建协程:
- `CoroutineScope.launch {}`: 不阻塞当前线程，在后台创建一个新协程
- `runBlocking {}`: 创建一个新的协程，同时**与其子协程共同阻塞**当前线程，直到协程结束.。主要作用为桥接普通阻塞代码和挂起代码的非阻塞代码。（保活 JVM）
- `withContext {}`: 不会创建新的协程，在指定协程上运行挂起代码块，并挂起该协程直至代码块运行完成。
- `async {}`: 创建一个新协程，唯一的区别是它有返回值

#### 比较：
- 1`GlobalScope.launch{}`是非阻塞的，`runBlocking{}`是阻塞的，两者都会启动一个协程

Job:
-  `Parent Job`对应有多个`Children Job`，取消`Parent Job`会取消所有的`Children Job`
-  如果执行`Children Job`时发生了异常，`Parent Job`随之取消
-  `Parent Job`完成时会等待所有`Children Job`完成了，才最终转化成`Completed`状态

#### 状态图：

 * | **State**                        | [isActive] | [isCompleted] | [isCancelled] |
 * | -------------------------------- | ---------- | ------------- | ------------- |
 * | _New_ (optional initial state)   | `false`    | `false`       | `false`       |
 * | _Active_ (default initial state) | `true`     | `false`       | `false`       |
 * | _Completing_ (transient state)   | `true`     | `false`       | `false`       |
 * | _Cancelling_ (transient state)   | `false`    | `false`       | `true`        |
 * | _Cancelled_ (final state)        | `false`    | `true`        | `true`        |
 * | _Completed_ (final state)        | `false`    | `true`        | `false`       |

#### 取消与异常：
- 一个协程在没有任何理由的情况下使用`Job.cancel`取消的时候，它会被终止，但是它不会取消它的父协程。 无理由取消是父协程取消其子协程而非取消其自身的机制。
- 如果协程遇到除`CancellationException`以外的异常，它将取消具有该异常的父协程。 所有的子协程被终止的时候，原本的异常被父协程所处理。

#### 监督(Supervisor)
- `SupervisorJob`中的异常和失败时独立的，也就是说，子协程发生异常或者取消不会导致`SupervisorJob`失败，同时也不会影响其他子协程。
- `SupervisorJob`可以被用于这些目的。它类似于常规的`Job`，唯一的取消异常将只会**向下传播**

### 使用注意事项：
1. 避免使用`GlobalScope`
因为在`Android`中`GlobalScope`对应整个应用的生命周期，所以`CoroutineScope`中的任务不能提前取消。`CoroutineScope`应该`Activity`, `Fragment`, `View`和`ViewModel`。

考虑以下实现方式：

`

```kotlin
class MainActivity : AppCompatActivity(), CoroutineScope {
    
    private val job = SupervisorJob()
    
    override val coroutineContext: CoroutineContext
        get() = Dispatchers.Main + job
    
    override fun onDestroy() {
        super.onDestroy()
        coroutineContext.cancelChildren()
    }
    
    fun loadData() = launch {
        // code
    }
}
```

`

2. 切换协程上下文
    例如修改`UI`界面同时需要修改数据源，此时不需要另外再开启一个协程，可以通过`withContext(){}`切换协程上下文。

```kotlin
val uiScope = CoroutineScope(Dispatchers.Main)
fun loadData() = uiScope.launch {
    view.showLoading() // ui thread
    val result = withContext(bgDispatcher) { // background thread
        // your blocking call
    }
    view.showData(result) // ui thread
}
```

3. 子线程中顺序执行任务
`withContext(){}`实现

```kotlin
val uiScope = CoroutineScope(Dispatchers.Main)
fun loadData() = uiScope.launch {
    view.showLoading() // ui thread

    val result1 = withContext(bgDispatcher) { // background thread
        // your blocking call
    }

    val result2 = withContext(bgDispatcher) { // background thread
        // your blocking call
    }
    
    val result = result1 + result2
    
    view.showData(result) // ui thread
}
```

4. 子线程中并行执行任务
`async{}`中实现

```kotlin
val uiScope = CoroutineScope(Dispatchers.Main)
fun loadData() = uiScope.launch {
    view.showLoading() // ui thread

    val task1 = async(bgDispatcher) { // background thread
        // your blocking call
    }

    val task2 = async(bgDispatcher) { // background thread
        // your blocking call
    }

    val result = task1.await() + task2.await()

    view.showData(result) // ui thread
}
```

5. `async`/`await`使用误区
不推荐`async`单个协程之后马上进行`await`，因为使用`async`目的就是为了开启多个协程，然后再去进行`await`。这种情况应该使用`withContext`进行替换：

```kotlin
launch {
	// bad practice
    val data = async(Dispatchers.Default) { /* code */ }.await()
}
```

6. 避免取消`CoroutineScope`的`Job`

```kotlin
class WorkManager {
    val job = SupervisorJob()
    val scope = CoroutineScope(Dispatchers.Default + job)
    
    fun doWork1() {
        scope.launch { /* do work */ }
    }
    
    fun doWork2() {
        scope.launch { /* do work */ }
    }
    
    fun cancelAllWork() {
        job.cancel()
    }
}

fun main() {
    val workManager = WorkManager()
    
    workManager.doWork1()
    workManager.doWork2()
    workManager.cancelAllWork()
    workManager.doWork1() // (1)
}
```

如上所示，用这种方法`cancel`一个`job`，会使`job`的状态直接变成`completed`，因此`(1)`处的代码不会执行。

正确的方法，应该使用`scope.coroutineContext.cancelChildren()`。

7. 协程构造器


协程资料参考：
- [协程使用背景](https://www.jianshu.com/p/9f720b9ccdea)
- [协程原理解析](https://www.jianshu.com/p/2659bbe0df16)
- [Coroutine recipes](https://proandroiddev.com/android-coroutine-recipes-33467a4302e9)
- [Coroutine patterns & anti patterns](https://proandroiddev.com/kotlin-coroutines-patterns-anti-patterns-f9d12984c68e)
- [解密 CoroutineContext](https://proandroiddev.com/demystifying-coroutinecontext-1ce5b68407ad)
- [协程实战运用（移除回调陷阱）](https://medium.com/@andrea.bresolin/playing-with-kotlin-in-android-coroutines-and-how-to-get-rid-of-the-callback-hell-a96e817c108b)
