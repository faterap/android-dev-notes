# Objective-C Category & Extension

### 1. Category

```
@implementation SampleClass(Category)

- (NSString *)lastNameFirstNameString{
    return [NSString stringWithFormat:@"%@, %@", self.lastName, self.firstName];
}

@end
```

### 2. Extension

Because no name is given in the parentheses, class extensions are often referred to as *anonymous categories*.

Unlike regular categories, a class extension can add its own properties and instance variables to a class. If you declare a property in a class extension, like this:

```objective-c
@interface XYZPerson ()
@property NSObject *extraProperty;
@end
```

3. ### 对比

- Category 在运行时被附加到本类，Extension 在编译时被附加到本类
- Category 不能为本类声明成员变量，Extension 可以为本类声明成员变量
- Category 中声明的属性只会自动生成 getter setter 的声明，不会生成对应的成员变量，不会生成 getter setter 的实现
- Extension 中声明的属性会自动生成 getter setter 的声明，对应的成员变量，以及 getter setter 的实现
- 添加 Category 不需要知道本类的源码，添加 Extension 需要知道本类的源码



extension看起来很像一个匿名的category，但是extension和有名字的category几乎完全是两个东西。 extension在编译期决议，它就是类的一部分，在编译期和头文件里的@interface以及实现文件里的@implement一起形成一个完整的类，它伴随类的产生而产生，亦随之一起消亡。extension一般用来隐藏类的私有信息，你必须有一个类的源码才能为一个类添加extension，所以你无法为系统的类比如NSString添加extension。（详见[2](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/CustomizingExistingClasses/CustomizingExistingClasses.html)）

但是category则完全不一样，它是在运行期决议的。 就category和extension的区别来看，我们可以推导出一个明显的事实，extension可以添加实例变量，而category是无法添加实例变量的（因为在运行期，对象的内存布局已经确定，如果添加实例变量就会破坏类的内部布局，这对编译型语言来说是灾难性的）。

