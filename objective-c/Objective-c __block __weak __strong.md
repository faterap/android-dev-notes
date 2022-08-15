# Objective-C  __block __weak __strong

### 1. __block

在一个 `block`里头如果使用了在 `block`之外的变数，会将这份变数先复制一份再使用，也就是，在没有特别宣告下，对于目前的`block` 来说，所有的外部的变数都是只可读的，不可改的。

如果我们想让某个 `block` 可以改动某个外部的变数，我们则需要将这个可以被 `block` 改动的变数前面加上`__block`。

```objective-c
__block int i = 1;
void (^block)(void) = ^{
     i = i + 1;
};
```

从另一个角度说，使用了`__block`关键字的变量会将变量从栈上复制到堆上。栈上那个变量会指向复制到堆上的变量。

### 2. __weak

有时在使用`block` 的时候，由于`self` 是被强引用的，在 ARC 下，当编译器自动将代码中的`block`从栈拷贝到堆时，`block` 会强引用和持有`self`，而 `self` 恰好也强引用和持有了 `block`，就造成了传说中的循环引用。
 此时 `__weak` 就出场了，在变量声明时用 `__weak`修饰符修饰变量 self，让 block 不强引用 self，从而破除循环。

```objectivec
__weak __typeof(self) weakSelf = self; 
self.testBlock = ^{
       [weakSelf test];
});
```

ps: 弱引用不会影响对象的释放，但是当对象被释放时，所有指向它的弱引用都会自定被置为 nil，这样可以防止野指针。

### 3. __strong

在上述使用 `block`中，虽说使用`__weak`，但是此处会有一个隐患，你不知道 `self` 什么时候会被释放，为了保证在`block`内不会被释放，我们添加`__strong`。



```objectivec
__weak __typeof(self) weakSelf = self; 
self.testBlock =  ^{
       __strong __typeof(weakSelf) strongSelf = weakSelf;
       [strongSelf test]; 
});
```

PS： `__strong` 表示强引用，对应定义 property 时用到的 strong。当对象没有任何一个强引用指向它时，它才会被释放。

> `weakSelf`是为了block不持有self，避免循环引用，而再声明一个`strongSelf`是因为一旦进入block执行，就不允许self在这个执行过程中释放。block执行完后这个`strongSelf`会自动释放，没有循环引用问题。



### 4. 比较

> __block  &  __weak

```objectivec
1. __block不管是ARC还是MRC模式下都可以使用，可以修饰对象，还可以修饰基本数据类型。
2. __weak只能在ARC模式下使用，也只能修饰对象（NSString），不能修饰基本数据类型（int）。
3. __block对象可以在block中被重新赋值，__weak不可以。
4. __block对象在ARC下可能会导致循环引用，非ARC下会避免循环引用，__weak只在ARC下使用，可以避免循环引用。
```

> strong & __strong

```objectivec
1、strong，weak 用来修饰属性。
strong 用来修饰强引用的属性；weak 用来修饰弱引用的属性；
2、__weak, __strong 用来修饰变量.
__strong 是默认的引用；__weak 声明了一个可以自动 nil 化的弱引用。
```

