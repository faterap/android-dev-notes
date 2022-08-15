# Objective-c @selector

### 1. calling the function vs @selector(function)

`performSelector: withObject:`是在iOS中的一种方法调用方式。他可以向一个对象传递任何消息，而不需要在编译的时候声明这些方法。所以这也是`runtime`的一种应用方式。

所以`performSelector`和直接调用方法的区别就在与`runtime`。直接调用编译是会自动校验。如果方法不存在，那么直接调用 在编译时候就能够发现，编译器会直接报错。
 但是使用`performSelector`的话一定是在运行时候才能发现，如果此方法不存在就会崩溃。所以如果使用`performSelector`的话他就会有个最佳伴侣`- (BOOL)respondsToSelector:(SEL)aSelector;`来在运行时判断对象是否响应此方法。







