# 

Dagger2 用法与详解

## **注解介绍**

- @Inject
- @Provides
- @Module
- @Component: used to annotate the **interface** that returns the root object of the graph.()

## **关系**



## **用法**

### **常用注解**

- @Inject 一般情况下是标注成员属性和构造函数，标注的成员属性不能是private，Dagger 2 还支持方法注入，@Inject还可以标注方法。
- @Provides 只能标注方法，必须在 Module 中。
- @Module 用来标注 Module 类
- @Component 只能标注接口或抽象类，声明的注入接口的参数类型必须和目标类一致。

### **其他注解**

- @Lazy(延迟注入)：有时我们想注入的依赖在使用时再完成初始化，加快加载速度，就可以使用注入Lazy<T>。
- @Provider: 有时候不仅仅是注入单个实例，我们需要多个实例，这时可以使用注入Provider<T>
- @Qualifier: Provides有多个方法时，使用该注解确定使用哪种provide方法
- @Scope（作用域）：Scope 注解只能标注目标类、@provide 方法和 Component。Scope 注解要生效的话，需要同时标注在 Component 和提供依赖实例的 Module 或目标类上。Module 中 provide 方法中的 Scope 注解必须和 与之绑定的 Component 的 Scope 注解一样，否则作用域不同会导致编译时会报错。
- @Binds: @Binds methods must have only one parameter whose type is assignable to the return type

### **@Scope**

##### **原理：**

当 Component 与 Module、目标类（需要被注入依赖）使用 Scope 注解绑定时，意味着 Component 对象持有绑定的依赖实例的一个引用直到 Component 对象本身被回收。**也就是作用域的原理，其实是让生成的依赖实例的生命周期与 Component 绑定，Scope 注解并不能保证生命周期，要想保证赖实例的生命周期，需要确保 Component 的生命周期。**

### **@Singleton**

Java中，单例通常保存在一个静态域中，这样的单例往往要等到虚拟机关闭时候，该单例所占用的资源才释放。但是，Dagger通过Singleton创建出来的单例并不保持在静态域上，而是保留在Component实例中。

### **@Subcomponent**

Subcomponent用于拓展原有component。同时，这将产生一种副作用——子component同时具备两种不同生命周期的scope。子Component具备了父Component拥有的Scope，也具备了自己的Scope。

### **@Component**

- Dagger 会自动生成带有 @Component 标注的模板代码，如 DaggerSomeClass....java

## **Dagger Android 使用：**

- Application级别的 Component 的 modules 需要添加 AndroidInjectionModule

## **总结：**

- Module的优先级比@Inject标注构造函数的高，意味着 Dagger 2 会先从 Module 寻找依赖实例。

参考资料：

- <https://blog.csdn.net/duo2005duo/article/details/50696166>
- <https://code.luasoftware.com/tutorials/android/setup-dagger2-for-android-kotlin/>
- <https://blog.csdn.net/mq2553299/article/details/77725912>
- <http://johnnyshieh.me/posts/dagger-use-in-android/>