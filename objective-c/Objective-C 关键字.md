# Objective-C 关键字

### 1. static

`static` 关键字，主要定义变量的**作用域**和**生命周期**。

`static` 修饰局部变量，主要定义变量生命周期，静态局部变量，因为存储在全局数据区，不会像其他存储在栈区的局部变量一样随着函数体结束被释放。

`static` 修饰全局变量，定义变量的作用域，被 `static` 修饰的量，只存储一份，始化一次，其他地方共享这一份数据。在 OC 中，`static` 变量声明一般在源文件（ “.m” ）中，如果放在头文件（ “.h” ）中，其他文件引入这个头文件时，容易引起命名冲突。被 `static` 修饰的全局变量，作用域为当前文件。

### 2. extern

`extern` 关键字，主要用来定义**外部全局变量**。前面说用 `static` 定义作用域为当前文件的全局变量。那如果想定义作用域为整个工程文件全局变量，即外部全局变量，则用 `extern`。

一般在头文件中使用 `extern` 声明变量，**在源文件中赋值**，尽量不要将外部全局变量的值暴露在头文件中；或者在头文件中声明，在其他文件中使用的时候再进行赋值。`extern` 关键字只对变量进行声明，表明该变量可能在本模块使用也可以在其他模块使用。例如类B如果想使用类A中定义的全局变量，只需要引入类A的头文件即可，这样即使在编译的时候找类B不到变量的定义也不会报错，它会在链接的时候在类A的目标代码中找到这个变量。

### 3. const

`const` 关键字，多与 `static` 和 `extern` 连用，定义的类型为**常类型**，属性为 **readonly**。当初学习 C++ 时经常被这几个名词搞懵逼：常指针，指向常量的指针，指向常量的常指针。对应到 OC 上大同小异，请注意”异”在哪里。`const` 一般有两种用法：
（1）修饰基本变量，即 int、double、float 等类型

```objc
// 下面两种写法是等价的
const int a = 10;		// a 不可变
int const a = 10;		// a 不可变
a = 12;	// error
```



(2) 修饰指针变量。在 OC 中，很多数据对象类型都有 `mutable（可变）` 和 `immutable（不可变）` 两种。`const` 在修饰”不可变”的指针变量时，多被用做定义”指针常量”。因为指针已经为不可变，再用 `const` 修饰没有意义。

```objc
// const 定义 "常量指针"，没有什么意义。'值'不可变的本身就不可变，可变的依然可变。
const NSString *str1 = @"hello";   // const 修饰不可变字符串，字符串本身就不可变。
const NSMutableString *str2 = [NSMutableString stringWithFormat:@"you"];   // const 修饰可变字符串
[str2 appendString:@"name"];  // str2 的值为@"you name"。str2 可以改变，const 没有起到作用。

==================================================================================================

// const 定义指针常量
NSString * const str3 = @"const value";
str3 = @"change point";	// 此种操作不合法，str3 指向对象不能改变
NSMutableString * const str4 = [NSMutableString stringWithFormat:@"mutable value"];
str4 = [NSMutableString stringWithFormat:@"change point"];   // 此种操作不合法，str4 指向对象不能改变
[str4 appendString:@"change value"];   // str4 的值可以改变。所以 str4 的定义方式没有意义。
```

综上，如果想定义不可变字符串（不可变数据对象），直接用 `NSString`；如果想定义可变字符串（可变数据对象），直接用 `NSMutableString`；如果想定义一个不可以改变的字符串（数据对象），即值不可变，指向对象也不可变，用 `NSString * const str = @"xxx"` 方式。且定义时就应赋值，如果不赋值，后面一直为 `nil`；如果想定义一个值可以改变，所指对象可以改变的字符串（数据对象），直接用 `NSString` 不就可以了么？

### 4. id

id 被称为”万能指针”，可以指向任何对象，可以用于任何类型的对象。由 id 关键字定义的对象，在编译器看来只是一个对象指针，关于对象的信息，需要等到运行时才能确定。也就是说，id 定义的对象不做类型检查，向它发送未知的消息，编译阶段不会报错。id 在 OC 中如下定义：

```objc
#if !OBJC_TYPES_DEFINED
/// Class 是一个 objc_class 结构体指针.
typedef struct objc_class *Class;

/// objc_object 结构体,里面是一个 Class 类型成员.
struct objc_object {
    Class isa  OBJC_ISA_AVAILABILITY;
};

/// id 为一个 objc_object 结构体指针.
typedef struct objc_object *id;
#endif
```

从上面代码可以看出，id 本质是一个结构体指针，结构体中只有一个成员 `isa`。任何一个 OC 对象，都会带一个默认的 `isa` 指针来存储对象的具体类型和信息。

id 关键字主要有以下几个使用场景：

```objc
// 1.定义 id 类型对象
id newObj = [NSArray array];   // newObj 在运行时才确定指向对象类型为 NSArray,编译时不确定
[newObj log];	                 // NSArray 类中并没有 'log' 这个对象方法,但是编译时不报错,运行时报错.

// 2.定义 delegate
@property (nonatomic, weak) id<MyDelegate> delegate;   // 不确定什么类型的对象作为代理,定义为 id 类型.并且规定实现 <MyDelegate> 这个协议的对象才能作为代理.因此像 delegate 发送消息时,首先要做 respondsToSelector:<#(SEL)#> 检查

// 3.作为返回类型
- (id)copy;
```

从 clang3.5 开始，出现类 `instancetype` 关键字。它可以表示一个方法的相关返回类型，与 `id` 不同的是，`instancetype` 返回是相关类的具体类型，编译器可以清楚的明确该类的信息，在调用该类的方法和属性时会进行检查。目前一般类的初始化方法，返回类型都为 `instancetype`。

```objc
// NSArray 的一些初始化方法
+ (instancetype)array;
+ (instancetype)arrayWithObject:(ObjectType)anObject;
+ (instancetype)arrayWithObjects:(const ObjectType [])objects count:(NSUInteger)cnt;
+ (instancetype)arrayWithObjects:(ObjectType)firstObj, ... NS_REQUIRES_NIL_TERMINATION;
+ (instancetype)arrayWithArray:(NSArray<ObjectType> *)array;

- (instancetype)initWithObjects:(ObjectType)firstObj, ... NS_REQUIRES_NIL_TERMINATION;
- (instancetype)initWithArray:(NSArray<ObjectType> *)array;
- (instancetype)initWithArray:(NSArray<ObjectType> *)array copyItems:(BOOL)flag;
```

### 5. @dynamic

@dynamic是相对于@synthesize的，它们用样用于修饰@property，用于生成对应的的getter和setter方法。但是@dynamic表示这个成员变量的getter和setter方法并不是直接由编译器生成，而是手工生成或者运行时生成。具体用法是在实现文件中，即原来写@synthesize的地方用@dynamic修饰，这样的话，就不会自动生成setter和getter方法了。

注：在Xcode4.4以后只要用@property修饰的属性编译器都会自动生成对应的getter和setter方法，即不用手动@synthesize生成了。

```objc
@implementation ClassName
@dynamic anotherProperty;
@end
```

### 6. @compatibility_alias

@compatibility_alis是用于给一个类设置一个别名。这样就不用重构以前的类文件就可以用新的名字来替代原有名字。

```objc
//现在你就可以使用MyUICollectionVC这个名字替代UICollectionViewController了
@compatibility_alias UICollectionViewController MyUICollectionVC;
```

### 7. @class

为了减少由依赖关系引起的重新编译所带的影响，Objective-C引入了关键字@class来告诉编译器：这是一个类，所以我只需要通过指针来引用它。它并不需要知道关于这个类的更多信息，只要了解它是通过指针引用即可。有的时候，@interface声明会在属性中引用外部类或者作为参数类型。而不是给每个类添加#import语句，在头文件使用前置声明，并且在implementation中引入它们是很好的做法。这样做编译时间更短，循环引用的机会更少。

```objc
//如果ClassA.h中仅需要声明一个ClassB的指针，那么就可以在ClassA.h中声明
@ClassB
...
ClassB *pointer;
//如果在implementation中要用到ClassB类的实体变量或者方法的话就需要import了
```

### 8. @throw, @try, @catch, @finally

在其他语言中用try/catch/finally块来捕获异常，如java。Objective-C主要通过NSError来沟通意想不到的异常状态。凡是也能像java那样使用@throw,@try,@catch,@finally这些关键字构成的块来捕获异常。

```objc
//代码来自Apple官方文档
@try {
    // code that throws an exception
    ...
}
@catch (CustomException *ce) { // most specific type
    // handle exception ce
    ...
}
@catch (NSException *ne) { // less specific type
    // do whatever recovery is necessary at his level
    ...
    // rethrow the exception so it's handled at a higher level
    @throw;
}
@catch (id ue) { // least specific type
    // code that handles this exception
    ...
}
@finally {
    // perform tasks necessary whether exception occurred or not
    ...
}
```

### 9. @synchronized

@synchronized，代表这个方法加锁, 相当于不管哪一个线程（例如线程A），运行到这个方法时,都要检查有没有其它线程例如B正在用这个方法，有的话要等正在使用synchronized方法的线程B运行完这个方法后再运行此线程A,没有的话,直接运行。

```objc
-(void)criticalMethod
{
    @synchronized(self)
    {
        //这里的代码会被加锁保护;
    }
}
```

@synchronized涉及到了一些线程同步的知识，如果想要详细了解线程同步，请到这里。

### 10. @autoreleasepool

@autoreleasepool叫自动释放池，是OC里面的一种内存回收机制，一般可以将一些临时变量添加到自动释放池中，统一回收释放，当自动释放池销毁时，池里面的所有对象都会调用一次release，也就是计数器会减1，但是自动释放池被销毁了，里面的对象并不一定会被销毁。使用自动释放池时要注意：

不要把大量循环放在autoreleasepool中，这样会造成内存峰值上升，因为里面创建的对象要等释放池销毁了才能释放，这种情况应该手动管理内存。
尽量避免大内存使用该方法，对于这种延迟释放机制，尽量少用

```objc
// 如果 someArray 里的元素非常多
for (id obj in someArray)
{
    @autoreleasepool
    {
        // 或者在每次遍历里都创建了大量的临时变量
    }
}
```

### 11. @selector





https://luoanhao.github.io/2017/02/26/Keywords-in-Objective-C/