# Objective-C KVC和KVO

### 1. KVC

#### 1.1 概述

 KVC（Key-value coding）键值编码，就是指iOS的开发中，可以允许开发者通过Key名直接访问对象的属性，或者给对象的属性赋值。而不需要调用明确的存取方法。这样就可以在运行时动态地访问和修改对象的属性。而不是在编译时确定，这也是iOS开发中的黑魔法之一。很多高级的iOS开发技巧都是基于KVC实现的。

​       在实现了访问器方法的类中，使用点语法和KVC访问对象其实差别不大，二者可以任意混用。但是没有访问起方法的类中，点语法无法使用，这时KVC就有优势了。

#### 1.2 使用

**KVC API**

```Java
(nullable id)valueForKey:(NSString *)key;                          //直接通过Key来取值

(void)setValue:(nullable id)value forKey:(NSString *)key;          //通过Key来设值

(nullable id)valueForKeyPath:(NSString *)keyPath;                  //通过KeyPath来取值

(void)setValue:(nullable id)value forKeyPath:(NSString *)keyPath;  //通过KeyPath来设值
```

如果A类的属性是一个B类，那么为B类的属性存取值就需要键路径访问属性
假设Student类有一个Course类的属性_course，其中Course类有一个属性表示课程名称NSString *_courseName;
下面通过键路径访问属性：

```objectivec
#import <Foundation/Foundation.h>
#import "Student.h"
#import "Course.h"

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        //注意：使用KVC是为实例存取实例变量，所以要初始化，并且为student的_course实例变量赋值为course，否则无法正确赋值
        Student *student = [[Student alloc] init];
        Course *course = [[Course alloc] init];
        [student setValue:course forKey:@"_course"];

        //为student实例变量_course的属性_courseName赋值
        [student setValue:@"English" forKeyPath:@"_course._courseName"];

        //取得student实例变量_course的属性_courseName的值
        NSString *courseName = [student valueForKeyPath:@"_course._courseName"];
        NSLog(@"courseName = %@",courseName);
    }
    return 0;
}
```

### 2.  KVO

KVO，即：key-value observing，提供一种机制，假设A对象观察B对象的指定属性C，当B对象的属性C的值发生变化，KVO会通知相应的观察者A，A对象可以进行某些操作，比如更新视图
使用过程：

1. 注册，指定观察哪些对象的哪些属性
2. 实现回调方法
3. 移除观察

首先定义一个Person类，代码如下：

```objectivec
#import <Foundation/Foundation.h>

@interface Person : NSObject

@property (nonatomic,copy)NSString *name;

- (instancetype)initWithName:(NSString *)name;

@end


#import "Person.h"

@implementation Person

- (instancetype)initWithName:(NSString *)name
{
    self = [super init];
    if (self) {
        _name = name;
    }
    return self;
}

@end
```

然后在ViewController中使用KVO，代码如下：



```objectivec
#import "ViewController.h"
#import "Person.h"

@interface ViewController ()
{
    Person *person;
}
@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    person = [[Person alloc] initWithName:@"John"];
    //为person对象的属性name添加观察者为ViewController
    [person addObserver:self forKeyPath:@"name" options:NSKeyValueObservingOptionNew context:nil];
    //可以通过下面两种方式改变name的值，都会触发回调方法
    person.name = @"SmithJackyson";
    [person setValue:@"SmithJackyson" forKey:@"name"];
}

//实现KVO回调方法，只要person的属性name发生变化，就会回调这个方法
- (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary<NSString *,id> *)change context:(void *)context
{
　　 //change字典里存的内容是根据你注册时的选项option决定的，如果为NSKeyValueObservingOptionNew，则字典里存的是改变后的值，key为new，同样NSKeyValueObservingOptionOld，则字典里存的是改变前的值，key为old，如果两者都有，则字典存储着两个值
    if ([keyPath isEqualToString:@"name"] && object == person) {
        NSLog(@"now name = %@",person.name);
    }
}

- (void)didReceiveMemoryWarning {
    [super didReceiveMemoryWarning];
    // Dispose of any resources that can be recreated.
}

@end
```





https://www.jianshu.com/p/b5170a6823b2