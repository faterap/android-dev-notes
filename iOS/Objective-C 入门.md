# Objective C 入门

## 1. 数据类型

### 基本数据类型

| 类型                   | 范围                                       | NSLog中输出的格式 |
| ---------------------- | ------------------------------------------ | ----------------- |
| char                   | ASCII 字元                                 | %c                |
| short int              | -32768~32767                               | %hi, %hx, %ho     |
| unsigned short int     | 0~65535                                    |                   |
| int                    | -2147483648~2147483647                     | %i                |
| unsigned int           | 0~4294967295                               | %u, %x, %o        |
| long int               | -2147483648~2147483647                     | %li, %lx, %lo     |
| unsinged long int      | 0~4294967295                               |                   |
| long long int          | -9223372036854775808 ~ 9223372036854775807 |                   |
| unsigned long long int | 0~18446744073709551615                     |                   |
| float                  | 3.4E-38 ~ 3.4+38                           | %f                |
| double                 | 1.7E-308 ~ 1.7E+308                        | %e                |
| long double            | 3.4E-4932 to 1.1E+4932                     |                   |
| BOOL                   | YES, NO, signed char                       |                   |
| id                     | 任何形态的类型                             |                   |



## 2. 常量定义

`Objective-C`中有两种方法定义常量：

- 使用`#define`进行预处理
- 使用`const`关键字



## 3. 关键字

**@interface**

**@implementation**

**@protocol**

**@optional**

**@required**

**@end**

**@encde**



## 4. 类

![2011021920525849](https://www.runoob.com/wp-content/uploads/2015/08/2011021920525849.jpg)

**Interface**

定义部分，清楚定义了类的名称、数据成员和方法。 以关键字@interface作为开始，@end作为结束。

```objective-c
@interface MyObject : NSObject {
    int memberVar1; // 实体变量
    id  memberVar2;
}

+(return_type) class_method; // 类方法

-(return_type) instance_method1; // 实例方法
-(return_type) instance_method2: (int) p1;
-(return_type) instance_method3: (int) p1 andPar: (int) p2;
@end
```

方法前面的 +/- 号代表函数的类型：加号（+）代表类方法（class method），不需要实例就可以调用，与C++ 的静态函数（static member function）相似。减号（-）即是一般的实例方法（instance method）。

**Implementation**

实现区块则包含了公开方法的实现，以及定义私有（private）变量及方法。 以关键字@implementation作为区块起头，@end结尾。

```objective-c
@implementation MyObject {
  int memberVar3; //私有實體變數
}

+(return_type) class_method {
    .... //method implementation
}
-(return_type) instance_method1 {
     ....
}
-(return_type) instance_method2: (int) p1 {
    ....
}
-(return_type) instance_method3: (int) p1 andPar: (int) p2 {
    ....
}
@end
```

值得一提的是不只Interface区块可定义实体变量，Implementation区块也可以定义实体变量，两者的差别在于访问权限的不同，Interface区块内的实体变量默认权限为protected，宣告于implementation区块的实体变量则默认为private，故在Implementation区块定义私有成员更匹配面向对象之封装原则，因为如此类别之私有信息就不需曝露于公开interface（.h文件）中。