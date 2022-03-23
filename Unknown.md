# Android

Android 学习路线：

- 不能仅仅为了做功能，而忽略了自己技能点的提升。
- 做完一个功能模块之后，要学会总结。将开发过程中不熟悉的点要系统学一下一遍。如 **Android 事件分发**这部分。
- 有空研究一下 Android 领域的新知识。如 RxJava , MVP 。自己写写Demo
- 关注 Andriod 的一些有名开源库。比如现在开发过程可能要用到的 Glide, Otto。学会用很简单，但是最好是吃透原理。
- 需要看书了，系统地整理自己的知识。Linux 设计模式 java基础知识

Summary:

- 主动引进一些新技术到自己模块中，学习主动一点！不然每天写业务代码是没有长进的。
- 不能忘记设计模式，多读一些 Android 的设计模式。

## 设计模式

- 状态机思想代替条件变量
- 新模块用模块化的思想来开发？不过先要抽出来common公共基础组件（是否可以用现用的Framework替换）
- 某个UI太复杂， 可以通过ViewDelegate进行组合，封装ViewModel，View等等

## 中台组件心得

- 设计class的时候基本先设计一个IXXX…接口，这样便于扩展，有利于使用方覆盖默认实现
- 接口尽可能抽象，不仅可以有方法，也可以有成员变量
- 框架设计时候少用object，单例一般充当工具类的作用
- 设计class的时候，尽可能在一个地方进行初始化，不要在View层进行，通过依赖注入的方式也是一个比较好的解决方式？
- 逻辑不要过分依赖ViewModel，不然会变成一个逻辑非常庞大混乱的主题。还是通过class去实现不同功能
- 少新建外部Object，能用内部类尽量用内部类解决，尽量用class
- 多用组合（委托），少用继承
- 调用某个方法，可以考虑用interface方式实现
- 尽量所有东西接口化，可以保证复用
- 能不进行继承就不继承，最好能用一个class去实现全部东西（参考CleanAdapter），静态方法new所有东西
- 尽量使用观察者模式

## Design pattern
- 代理模式
- 责任链模式
- 外观模式
- 装饰器模式

## Kotlin

- 工具类转换成委托模式(by Delegate)，代码优雅一点
- 能用成员变量写的，就不用函数实现
- 各种不同类型的函数（putBoolea, putString……），kotlin里面都可以统一替换掉
- 带有接收者的函数字面值
- with()用法
- 扩展函数尽可能写成inline
- 参数T 和 ()->T分别在什么场景下用
- 泛型不是很熟
- Crossinline, noinline, inline区别

## JetPack：

- Paging3
- 新注入库
- WorkManager
- Navigation
- Compose


## 协程：

- Flow


## Android能力扩展

- Flutter
- AOP
- Kotlin进阶

## 技术栈拓展
- iOS
- AI
- python
- go
- web 小程序


