#  Kotlin 总结

## 特性

1. Java 互通性
2. 类型推断
3. 字符串模板
4. 智能类型转换
5. 不需要通过 equals 判断相等
6. 默认参数
7. 命名参数
8. when表达式
9. 数据类
10. 运算符重载
11. 扩展方法
12. 万物皆函数
13. 空值安全
14. Lambda 表达式
15. 协程

...



## 数据类型

- 数字

| Type   | Bit width |
| ------ | --------- |
| Double | 64        |
| Float  | 32        |
| Long   | 64        |
| Int    | 32        |
| Short  | 16        |
| Byte   | 8         |

- 数组(Array)
- 字符(Char)
- 布尔(Boolean)
- 字符串(String)



## Elvis operator:

```kotlin
val l = b?.length ?: -1
```

如果 `?:` 左侧表达式非空，elvis 操作符就返回其左侧表达式，否则返回右侧表达式。 请注意，当且仅当左侧为空时，才会对右侧表达式求值。

## 可见性修饰符

> **模块**
>
> 可见性修饰符 `internal` 意味着该成员只在相同模块内可见。更具体地说， 一个模块是编译在一起的一套 Kotlin 文件：
>
> - 一个 IntelliJ IDEA 模块；
> - 一个 Maven 项目；
> - 一个 Gradle 源集（例外是 `test` 源集可以访问 `main` 的 internal 声明）；
> - 一次 `<kotlinc>` Ant 任务执行所编译的一套文件。


- public
- internal：它会在相同[模块](https://www.kotlincn.net/docs/reference/visibility-modifiers.html#%E6%A8%A1%E5%9D%97)内随处可见
- protected
- private：它只会在声明它的文件内可见



## 关键字和操作符

### 硬关键字：

- in

  - 指定在 [for 循环](https://www.kotlincn.net/docs/reference/control-flow.html#for-%E5%BE%AA%E7%8E%AF)中迭代的对象
  - 用作中缀操作符以检查一个值属于[一个区间](https://www.kotlincn.net/docs/reference/ranges.html)、 一个集合或者其他[定义“contains”方法](https://www.kotlincn.net/docs/reference/operator-overloading.html#in)的实体
  - 在 [when 表达式中](https://www.kotlincn.net/docs/reference/control-flow.html#when-%E8%A1%A8%E8%BE%BE%E5%BC%8F)用于上述目的
  - 将一个类型参数标记为[逆变](https://www.kotlincn.net/docs/reference/generics.html#%E5%A3%B0%E6%98%8E%E5%A4%84%E5%9E%8B%E5%8F%98)

- !in

  - 用作中缀操作符以检查一个值**不**属于[一个区间](https://www.kotlincn.net/docs/reference/ranges.html)、 一个集合或者其他[定义“contains”方法](https://www.kotlincn.net/docs/reference/operator-overloading.html#in)的实体
  - 在 [when 表达式中](https://www.kotlincn.net/docs/reference/control-flow.html#when-%E8%A1%A8%E8%BE%BE%E5%BC%8F)用于上述目的



### 软关键字：

- by

  - [将接口的实现委托给另一个对象](https://www.kotlincn.net/docs/reference/delegation.html)
  - [将属性访问器的实现委托给另一个对象](https://www.kotlincn.net/docs/reference/delegated-properties.html)

- `catch` 开始一个[处理指定异常类型](https://www.kotlincn.net/docs/reference/exceptions.html)的块

- `constructor` 声明一个[主构造函数或次构造函数](https://www.kotlincn.net/docs/reference/classes.html#%E6%9E%84%E9%80%A0%E5%87%BD%E6%95%B0)

- `delegate` 用作[注解使用处目标](https://www.kotlincn.net/docs/reference/annotations.html#%E6%B3%A8%E8%A7%A3%E4%BD%BF%E7%94%A8%E5%A4%84%E7%9B%AE%E6%A0%87)

- `dynamic` 引用一个 Kotlin/JS 代码中的[动态类型](https://www.kotlincn.net/docs/reference/dynamic-type.html)

- `field` 用作[注解使用处目标](https://www.kotlincn.net/docs/reference/annotations.html#%E6%B3%A8%E8%A7%A3%E4%BD%BF%E7%94%A8%E5%A4%84%E7%9B%AE%E6%A0%87)

- `file` 用作[注解使用处目标 da](https://www.kotlincn.net/docs/reference/annotations.html#%E6%B3%A8%E8%A7%A3%E4%BD%BF%E7%94%A8%E5%A4%84%E7%9B%AE%E6%A0%87)

- `finally` 开始一个[当 try 块退出时总会执行的块](https://www.kotlincn.net/docs/reference/exceptions.html)

- get

  - 声明[属性的 getter](https://www.kotlincn.net/docs/reference/properties.html#getters-%E4%B8%8E-setters)
  - 用作[注解使用处目标](https://www.kotlincn.net/docs/reference/annotations.html#%E6%B3%A8%E8%A7%A3%E4%BD%BF%E7%94%A8%E5%A4%84%E7%9B%AE%E6%A0%87)

- `import` [将另一个包中的声明导入当前文件](https://www.kotlincn.net/docs/reference/packages.html)

- `init` 开始一个[初始化块](https://www.kotlincn.net/docs/reference/classes.html#%E6%9E%84%E9%80%A0%E5%87%BD%E6%95%B0)

- `param` 用作[注解使用处目标](https://www.kotlincn.net/docs/reference/annotations.html#%E6%B3%A8%E8%A7%A3%E4%BD%BF%E7%94%A8%E5%A4%84%E7%9B%AE%E6%A0%87)

- `property` 用作[注解使用处目标](https://www.kotlincn.net/docs/reference/annotations.html#%E6%B3%A8%E8%A7%A3%E4%BD%BF%E7%94%A8%E5%A4%84%E7%9B%AE%E6%A0%87)

- `receiver`用作[注解使用处目标](https://www.kotlincn.net/docs/reference/annotations.html#%E6%B3%A8%E8%A7%A3%E4%BD%BF%E7%94%A8%E5%A4%84%E7%9B%AE%E6%A0%87)

- set

  - 声明[属性的 setter](https://www.kotlincn.net/docs/reference/properties.html#getters-%E4%B8%8E-setters)
  - 用作[注解使用处目标](https://www.kotlincn.net/docs/reference/annotations.html#%E6%B3%A8%E8%A7%A3%E4%BD%BF%E7%94%A8%E5%A4%84%E7%9B%AE%E6%A0%87)

- `setparam` 用作[注解使用处目标](https://www.kotlincn.net/docs/reference/annotations.html#%E6%B3%A8%E8%A7%A3%E4%BD%BF%E7%94%A8%E5%A4%84%E7%9B%AE%E6%A0%87)

- `where` 指定[泛型类型参数的约束](https://www.kotlincn.net/docs/reference/generics.html#%E4%B8%8A%E7%95%8C)



### 修饰符关键字：

#### inline:

- 原理：编译器把实现内联函数的字节码动态插入到每次的调用点

### noinline:
- 如果你只想被（作为参数）传给一个内联函数的 lamda 表达式中只有一些进行内联，你可以用 noinline 修饰符标记一些函数参数
- 让内联函数的lambda参数不进行内联，保留一般函数的特征。

`inline fun foo(inlined: () -> Unit, noinline notInlined: () -> Unit) {`
`// ……`
`}`

#### crossinline:

- 让被标记的lambda表达式不允许非局部返回

reified:

> There's no way to call Kotlin inline functions from Java because they must be transformed and inlined at the call sites (in your case, T should be substituted with the actual type at each call site, but there's much more compiler logic for inline functions than just this), and the Java compiler is, expectedly, completely unaware of that.

- 可以将内联函数的泛型参数当做真实类型使用
- 必须配合 inline 来使用

原理：

**每次调用带实化类型参数的函数时，编译器都知道此次调用中作为泛型类型实参的具体类型。所以编译器只要在每次调用时生成对应不同类型实参调用的字节码插入到调用点即可。**总之一句话很简单，就是带实化参数的函数每次调用都生成不同类型实参的字节码，动态插入到调用点。由于生成的字节码的类型实参引用了具体的类型，而不是类型参数所以不会存在擦除问题。

[]: https://droidyue.com/blog/2019/07/28/kotlin-reified-generics/	"使用Kotlin Reified 让泛型更简单安全"



#### infix:
标有 infix 关键字的函数也可以使用中缀表示法（忽略该调用的点与圆括号）调用。
- 它们必须是成员函数或扩展函数；
- 它们必须只有一个参数；
- 其参数不得接受可变数量的参数且不能有默认值。

#### suspend：
被该关键字修饰的函数/方法/代码块只能由协程代码或者被 `suspend` 修饰的函数/方法/代码块调用。说简单一点，`suspend fun` 只能被 `suspend fun` 调用

#### actual
表示[多平台项目](https://www.kotlincn.net/docs/reference/multiplatform.html)中的一个平台相关实现

#### expect 
将一个声明标记为[平台相关](https://www.kotlincn.net/docs/reference/multiplatform.html)，并期待在平台模块中实现。
#### external
将一个声明标记为不是在 Kotlin 中实现（通过 [JNI](https://www.kotlincn.net/docs/reference/java-interop.html#%E5%9C%A8-kotlin-%E4%B8%AD%E4%BD%BF%E7%94%A8-jni) 访问或者在 [JavaScript](https://www.kotlincn.net/docs/reference/js-interop.html#external-%E4%BF%AE%E9%A5%B0%E7%AC%A6) 中实现）
#### internal

将一个声明标记为[在当前模块中可见](https://www.kotlincn.net/docs/reference/visibility-modifiers.html)
#### tailrec

将一个函数标记为[尾递归](https://www.kotlincn.net/docs/reference/functions.html#%E5%B0%BE%E9%80%92%E5%BD%92%E5%87%BD%E6%95%B0)（允许编译器将递归替换为迭代）
#### vararg

允许[一个参数传入可变数量的参数](https://www.kotlincn.net/docs/reference/functions.html#%E5%8F%AF%E5%8F%98%E6%95%B0%E9%87%8F%E7%9A%84%E5%8F%82%E6%95%B0varargs)



### 特殊标识符

以下标识符由编译器在指定上下文中定义，并且可以用作其他上下文中的常规标识符：

- `field` 用在属性访问器内部来引用该[属性的幕后字段](https://www.kotlincn.net/docs/reference/properties.html#%E5%B9%95%E5%90%8E%E5%AD%97%E6%AE%B5)
- `it` 用在 lambda 表达式内部来[隐式引用其参数](https://www.kotlincn.net/docs/reference/lambdas.html#it%E5%8D%95%E4%B8%AA%E5%8F%82%E6%95%B0%E7%9A%84%E9%9A%90%E5%BC%8F%E5%90%8D%E7%A7%B0)



### 特殊符号

- `::` 创建一个[成员引用](https://www.kotlincn.net/docs/reference/reflection.html#%E5%87%BD%E6%95%B0%E5%BC%95%E7%94%A8)或者一个[类引用](https://www.kotlincn.net/docs/reference/reflection.html#%E7%B1%BB%E5%BC%95%E7%94%A8)
- `..` 创建一个[区间](https://www.kotlincn.net/docs/reference/ranges.html)



## lambda 表达式

### 作用域
- 和Java不一样，Kotlin允许在lambda内部访问非final变量甚至修改它们。从lambda内访问外部变量，我们称这些变量被lambda捕获。

#### 原理：
- 当捕捉final变量时，它的值和使用这个值的lambda代码一起存储。
- 对非final变量，它的值被封装在一个包装器中，这样你就可以改变这个值，而对这个包装器的引用会和lambda代码一起存储。

### 扩展：
- 如果一个类定义有一个成员函数与一个扩展函数，而这两个函数又有相同的接收者类型、相同的名字，都适用给定的参数，这种情况总是取成员函数



## 运算符重载

### 一元操作符

#### 一元前缀操作符

| 表达式 | 翻译为           |
| ------ | ---------------- |
| `+a`   | `a.unaryPlus()`  |
| `-a`   | `a.unaryMinus()` |
| `!a`   | `a.not()`        |

#### 递增与递减

| 表达式 | 翻译为             |
| ------ | ------------------ |
| `a++`  | `a.inc()` + 见下文 |
| `a--`  | `a.dec()` + 见下文 |

二元操作符

| 表达式  | 翻译为         |
| ------- | -------------- |
| `a + b` | `a.plus(b)`    |
| `a - b` | `a.minus(b)`   |
| `a * b` | `a.times(b)`   |
| `a / b` | `a.div(b)`     |
| `a % b` | `a.rem(b)`     |
| `a..b`  | `a.rangeTo(b)` |

“In”操作符

| 表达式    | 翻译为           |
| --------- | ---------------- |
| `a in b`  | `b.contains(a)`  |
| `a !in b` | `!b.contains(a)` |

#### 索引访问操作符

| 表达式                | 翻译为                   |
| --------------------- | ------------------------ |
| `a[i]`                | `a.get(i)`               |
| `a[i, j]`             | `a.get(i, j)`            |
| `a[i_1, ……, i_n]`     | `a.get(i_1, ……, i_n)`    |
| `a[i] = b`            | `a.set(i, b)`            |
| `a[i, j] = b`         | `a.set(i, j, b)`         |
| `a[i_1, ……, i_n] = b` | `a.set(i_1, ……, i_n, b)` |

#### 调用操作符

| 表达式            | 翻译为                   |
| ----------------- | ------------------------ |
| `a()`             | `a.invoke()`             |
| `a(i)`            | `a.invoke(i)`            |
| `a(i, j)`         | `a.invoke(i, j)`         |
| `a(i_1, ……, i_n)` | `a.invoke(i_1, ……, i_n)` |

#### 广义赋值

| 表达式   | 翻译为             |
| -------- | ------------------ |
| `a += b` | `a.plusAssign(b)`  |
| `a -= b` | `a.minusAssign(b)` |
| `a *= b` | `a.timesAssign(b)` |
| `a /= b` | `a.divAssign(b)`   |
| `a %= b` | `a.remAssign(b)`   |

#### 相等与不等操作符

| 表达式   | 翻译为                            |
| -------- | --------------------------------- |
| `a == b` | `a?.equals(b) ?: (b === null)`    |
| `a != b` | `!(a?.equals(b) ?: (b === null))` |

#### 比较操作符

| 表达式   | 翻译为                |
| -------- | --------------------- |
| `a > b`  | `a.compareTo(b) > 0`  |
| `a < b`  | `a.compareTo(b) < 0`  |
| `a >= b` | `a.compareTo(b) >= 0` |
| `a <= b` | `a.compareTo(b) <= 0` |



## 委托属性：

定义委托属性语法：

 `val/var <property name>: <Type> by <expression>`

### lazy:

在Kotlin中，變數的初始化都必須指定值。我們先前為了避免在使用var宣告變數時就先給定初始值，使用lateinit來應變。而使用`lazy`则可以避免声明`val`变量时就给定初始值，实现「懒加载」。

```
`val propLazy: Int by lazy{1}`
```

- 默认情况下，对于 lazy 属性的求值是同步锁的（synchronized）：该值只在一个线程中计算，并且所有线程会看到相同的值。

**observable:**

让属性在发生变动的时候可以被关注的地方观察到。

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
- `runBlocking {}`: 创建一个新的协程，同时**与其子协程共同阻塞**当前线程，直到协程结束。主要作用为桥接普通阻塞代码和挂起代码的非阻塞代码。（保活 JVM）
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





------


协程资料参考：
- [协程使用背景](https://www.jianshu.com/p/9f720b9ccdea)
- [协程原理解析](https://www.jianshu.com/p/2659bbe0df16)
- [Coroutine recipes](https://proandroiddev.com/android-coroutine-recipes-33467a4302e9)
- [Coroutine patterns & anti patterns](https://proandroiddev.com/kotlin-coroutines-patterns-anti-patterns-f9d12984c68e)
- [解密 CoroutineContext](https://proandroiddev.com/demystifying-coroutinecontext-1ce5b68407ad)
- [协程实战运用（移除回调陷阱）](https://medium.com/@andrea.bresolin/playing-with-kotlin-in-android-coroutines-and-how-to-get-rid-of-the-callback-hell-a96e817c108b)
- https://www.bennyhuo.com/2019/10/19/coroutine-why-so-called-lightweight-thread/
