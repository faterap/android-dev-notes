# Objective-C SEL IMP Method

### 1. SEL

selector其实只是一个SEL类型的一个实例：

```
@property SEL selector;
```

 SEL：一个不透明的类型，代表方法的选择器/选择子。定义如下：

```
typedef struct objc_selector *SEL;
// 是一个指向 objc_selector 结构体的指针
// objc_selector 未开源
```

 在源码中没有直接找到objc_selector的定义，从一些书籍上与Blog上看到可以将SEL理解为一个char*指针。

```
// GNU OC 中的 objc_selector
struct objc_selector {  
  void *sel_id;  
  const char *sel_types;  
}; 
```

注意：

 不同类中相同名字的方法所对应的方法选择器是相同的，即使方法名字相同而变量类型不同，也会导致它们具有相同的方法选择子。比如：

```
- (void)caculate(NSInteger)num;
- (void)caculate(CGFloat)num; 
```

### 2. IMP

指向方法实现的首地址的指针。源码里实现如下：（可以看得出来是对方法类型起了一个别名。）

```
/// A pointer to the function of a method implementation. 
#if !OBJC_OLD_DISPATCH_PROTOTYPES
typedef void (*IMP)(void /* id, SEL, ... */ ); 
#else
typedef id _Nullable (*IMP)(id _Nonnull, SEL _Nonnull, ...); 
#endif
```


 IMP的数据类型是指针，指向方法实现开始的位置。这样的方法使用标准的C调用协议（此协议是为当前CPU体系结构实现的）。

第一个参数是指向self的指针（实例的内存<实例方法> 或 元类的指针<类方法>）；
第二个参数是方法的选择器；
接下来是方法的参数。

### 3. Method

 一个不透明的类型，表示类中定义的方法。定义如下：

```
/// An opaque type that represents a method in a class definition.
typedef struct objc_method *Method;

struct objc_method {
    SEL _Nonnull method_name   OBJC2_UNAVAILABLE;
    char * _Nullable method_types   OBJC2_UNAVAILABLE;
    IMP _Nonnull method_imp    OBJC2_UNAVAILABLE;
}                                                            OBJC2_UNAVAILABLE;
```

 从代码的定义可以看出，Method是一个指向objc_method结构体类型的指针。objc_method结构体中定一个三个属性：

method_name：SEL类型（选择器），表示方法名的字符串。

method_types：char*类型的，表示方法的类型；包含返回值和参数的类型。

method_imp：IMP类型，指向方法实现地址的指针。



### 4. 总结

- `SEL`：就是一个字符串（`Char*`类型），表示方法的名字
- `IMP`：就是指向方法实现首地址的指针
- `Method`：是一个结构体，包含一个`SEL`表示方法名、一个`IMP`指向函数的实现地址、一个`Char*`表示函数的类型（包括返回值和参数类型）



SEL、IMP、Method之间的关系可以这么理解：

一个类（Class）持有一系列的方法（Method），在load类时，runtime会将所有方法的选择器（SEL）hash后映射到一个集合（NSSet）中（NSSet里的元素不能重复）。
当需要发消息时，会根据选择器（SEL）去查找方法；找到之后，用Method结构体里的函数指针（IMP）去调用方法。这样在运行时查找selecter的速度就会非常快。