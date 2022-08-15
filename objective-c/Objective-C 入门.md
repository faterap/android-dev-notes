# Objective C 入门

### 1. 数据类型

####  1.1 基本数据类型

| 类型                   | 范围                                       | NSLog中输出的格式 | 占用字节数 |
| ---------------------- | ------------------------------------------ | ----------------- | ---------- |
| char                   | -128 to 127 or 0 to 255                    | %c                | 1          |
| unsigned char          | 0 to 255                                   |                   | 1          |
| signed char            | -128 to 127                                |                   | 1          |
| short int              | -32768~32767                               | %hi, %hx, %ho     | 2          |
| unsigned short int     | 0~65535                                    |                   | 2          |
| int                    | -2147483648~2147483647                     | %i                | 2 or 4     |
| unsigned int           | 0~4294967295                               | %u, %x, %o        | 2 or 4     |
| long int               | -2147483648~2147483647                     | %li, %lx, %lo     | 4          |
| unsinged long int      | 0~4294967295                               |                   | 4          |
| long long int          | -9223372036854775808 ~ 9223372036854775807 |                   |            |
| unsigned long long int | 0~18446744073709551615                     |                   |            |
| float                  | 1.2E-38 to 3.4E+38                         | %f                | 4          |
| double                 | 2.3E-308 to 1.7E+308                       | %e                | 8          |
| long double            | 3.4E-4932 to 1.1E+4932                     |                   | 10         |
| BOOL                   | YES, NO, signed char                       |                   |            |
| id                     | 任何形态的类型                             |                   |            |



#### 1.2 字符串

Objective-C里有字符串是由双引号包裹，并在引号前加一个@符号，例如：

```
title = @"Hello";
if(title == @"hello") {}
```

####  

### 2. 常量定义

`Objective-C`中有两种方法定义常量：

- 使用`#define`进行预处理
- 使用`const`关键字

#### 2.1 #define

```objective-c
#import <Foundation/Foundation.h>

#define LENGTH 10   
#define WIDTH  5
#define NEWLINE '\n'

int main() {
   int area;
   area = LENGTH * WIDTH;
   NSLog(@"value of area : %d", area);
   NSLog(@"%c", NEWLINE);

   return 0;
}
```

#### 2.2 const关键字

```objective-c
#import <Foundation/Foundation.h>

int main() {
   const int  LENGTH = 10;
   const int  WIDTH  = 5;
   const char NEWLINE = '\n';
   int area;  
   
   area = LENGTH * WIDTH;
   NSLog(@"value of area : %d", area);
   NSLog(@"%c", NEWLINE);

   return 0;
}
```



### 3. 关键字

#### 3.1 @interface

#### 3.2 @implementation

#### 3.3 @protocol

#### 3.4 @optional

#### 3.5 @required

#### 3.6 @end

#### 3.7 @encde



### 4. 类

![2011021920525849](https://www.runoob.com/wp-content/uploads/2015/08/2011021920525849.jpg)

#### 4.1 Interface

定义部分，清楚定义了类的名称、数据成员和方法。 以关键字@interface作为开始，@end作为结束。

```objective-c
@interface Person : NSObject{
	NSString *name
	int age;
}

//定义一个属性，这个属性就相当于对成员变量的 get/set
@property(nonatomic,strong)NSString *personName;

//上面属性的定义的本质就是定义了如下方法：
/*- (void) setName:(NSString *)name;
- (NSString *)getName;*/
@end
```

方法前面的 +/- 号代表函数的类型：加号（+）代表类方法（class method），不需要实例就可以调用，与C++ 的静态函数（static member function）相似。减号（-）即是一般的实例方法（instance method）。

#### 4.2 Implementation

实现区块则包含了公开方法的实现，以及定义私有（private）变量及方法。 以关键字@implementation作为区块起头，@end结尾。

```objective-c
// Person.m
#import "Person.h"

@implementation Person

@synthesize personName = name;

- (instancetype)init{
	self = [super init];
	if (self)
	{
		name = @"张三"；
	}
	return self；
}

/*- (void) setName:(NSString *)name{
	personName = name;
}

- (NSString *)getName{
	return personName;
}*/

@end
```

值得一提的是不只Interface区块可定义实体变量，Implementation区块也可以定义实体变量，两者的差别在于访问权限的不同，Interface区块内的实体变量默认权限为protected，宣告于implementation区块的实体变量则默认为private，故在Implementation区块定义私有成员更匹配面向对象之封装原则，因为如此类别之私有信息就不需曝露于公开interface（.h文件）中。

#### 4.3 属性定义

```objective-c
@interface Person:NSObject
	@property NSString *firstName;
	@property NSNumber *age;
	@property int id;
	@property (readonly)NSString * lastName;
@end
```

定义属性必须以@property 开头，然后是属性的类型，后面是变量名，如果变量是以`*`开头的，那么表明这个变量是一个指针，指向的是一块对内存中的区域；

同时，属性也有基本类型，eg：int，double 等，这表明的是一个基本类型；

用(readonly)前缀的变量，表明该变量时只读的，这和 Java 中的 final 关键字很类似

#### 4.4 成员变量和实例变量

1. 成员变量是在{}中声明的变量
2. 如果成员变量的类型是一个类则称这个变量为实例变量

```
#import <Foundation/Foundation.h>
NS_ASSUME_NONNULL_BEGIN
@interface Persion : NSObject{
    NSString *name; //实例变量
    int age;//成员变量
}
@end
NS_ASSUME_NONNULL_END
```

##### 访问权限

**@private、@protected、@public**

- @protected 受保护的，只能在本类或子类中访问，默认类型

- @private 私有的，只能在本类中访问

- @public 公开的，可以在外部进行访问

#### 4.5 方法定义

##### 4.5.1 对象/静态方法

减号方法（普通方法，又称为对象方法）

加号方法（类方法，又称为静态方法）

```objective-c
SampleClass *sampleClass = [[SampleClass alloc]init];

  sampleClass.lastName = @"tylertan";

  [sampleClass sampleMethod];

  [SampleClass staticSampleMethod];
```

##### 4.5.2 方法声明

```
-(returnType)methodName:(typeName) variable1 :(typeName)variable2;
```

- 方法类型，可选的有 + 或者 -，其中前者代表类方法，后者代表对象方法
- 方法返回值类型：要用括号包裹起来，eg：(void) ，(int) ，(NSString *)等
- 方法名：和其它语言相同
- 方法参数：没有参数，那么方法名后面就不用跟冒号了，如果有参数，那么方法名后边要跟一个冒号

举个例子🌰

```
- (void)showName : (NSString *) name :(int) age;
```

#### 4.6 创建对象

Objective-C创建对象需通过alloc以及init两个消息。alloc的作用是分配内存，init则是初始化对象。 init与alloc都是定义在NSObject里的方法，父对象收到这两个信息并做出正确回应后，新对象才创建完毕。以下为范例：

```
MyObject * my = [[MyObject alloc] init];
```

在Objective-C 2.0里，若创建对象不需要参数，则可直接使用new

```
MyObject * my = [MyObject new];
```

仅仅是语法上的精简，效果完全相同。

若要自己定义初始化的过程，可以重写init方法，来添加额外的工作。（用途类似C++ 的构造函数constructor）

### 5. Protocol协议（接口）

#### 语法

```objective-c
@protocol Locking
- (void)lock;
- (void)unlock;
@end
```

### 5. 基本语法

#### 5.1 条件控制

关于 OC 中的布尔值，和其它语言不同，并不是 true、false，而是用 0 来表示 false，如果 if 中的条件表达式中的值不是 0，其它所有的数字，或者字符串都可以表示 true；

#### 5.2 循环语句



### 6. 属性修饰符

#### 6.1 访问权限

- readonly
- readwrite

#### 6.2 内存访问

- strong
- assign
- weak

#### 6.3 原子控制

- nonatomic
- atomic

#### 6.4 行为控制

- Copy

### 7. 静态类型和动态类型

#### 7.1 静态类型

将一个指针变量定义为特定类的对象时,使用的是静态类型,在编译的时候就知道这个指针变量所属的类,这个变量总是存储特定类的对象

```objc
Person *p = [Person new];
```

#### 7.2 动态类型

这一特性是程序直到执行时才确定对象所属的类

```objc
id obj = [Person new];
```

#### 7.3 总结

id数据类型可以存储任何类型的对象，但是不要养成滥用这种通用类型

- 如没有使用到多态尽量使用静态类型
- 静态类型可以更早的发现错误(在编译阶段而不是运行阶段)
- 静态类型能够提高程序的可读性
- 使用动态类型前最好判断其真实类型
