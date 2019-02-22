# Kotlin总结

## 关键字

### inline:

- 原理：编译器把实现内联函数的字节码动态插入到每次的调用点

### noinline:
- 如果你只想被（作为参数）传给一个内联函数的 lamda 表达式中只有一些被内联，你可以用 noinline 修饰符标记一些函数参数
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
- 