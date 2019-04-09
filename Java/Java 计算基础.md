# Java 计算基础

## 1. Integer 大小比较

```java
Integer integer1 = 128;
Integer integer2 = 128;
System.out.println(integer1 == integer2); // false
System.out.println(integer1.equals(integer2)); // true
System.out.println("-----------");

Integer integer3 = 127;
Integer integer4 = 127;
System.out.println(integer3 == integer4); // true
System.out.println(integer3.equals(integer4));// true
```

查看Integer原码可知，针对-128和127之间的数据，Java做了一个数据缓冲区，如果数据属于这个范围内，会从缓冲区里面直接去数据，并不会创建新的空间。

这种 Integer 缓存策略仅在自动装箱（autoboxing）的时候有用，使用构造器创建的 Integer 对象不能被缓存。

**扩展**

这种缓存行为不仅适用于Integer对象。我们针对所有整数类型的类都有类似的缓存机制。

- 有 ByteCache 用于缓存 Byte 对象
- 有 ShortCache 用于缓存 Short 对象
- 有 LongCache 用于缓存 Long 对象
- 有 CharacterCache 用于缓存 Character 对象
- Byte，Short，Long 有固定范围: -128 到 127。对于 Character, 范围是 0 到 127。除了 Integer 可以通过参数改变范围外，其它的都不行。

## 2. 类型转换

### Java 中的类型

| 基本类型 | 大小    | 最小值  | 最大值  | 包装器类型 |
| -------- | ------- | ------- | ------- | ---------- |
| boolean  | -       | -       | -       | -          |
| char     | 16-bit  |         |         | Character  |
| byte     | 8 bits  | -128    | 127     | Byte       |
| short    | 16 bits | -2^15   | 2^15-1  | Short      |
| int      | 32 bits | -2^31   | 2^31-1  | Integer    |
| long     | 64 Bits | -2^63   | 2^63-1  | Long       |
| float    | 32 bits | IEEE754 | IEEE754 | Float      |
| double   | 64 bits | IEEE754 | IEEE754 | Double     |
| void     | -       | -       | -       | Void       |

Java中的基本类型的强制转换都是非常粗暴的，对于浮点型转为整型，都进行非常**粗暴的截尾**。对于多位数转换为少位数，也**只是截断**，根本不做舍入和约算。

### 例子

```java
public static void main(String[] args) {
        // TODO Auto-generated method stub
        byte a=(byte)127; // 127
        byte b=(byte)128; // -128
        byte c=(byte)100; // 44
        int  x=0xff;      
        byte d=(byte)x;   // -1
        x=0x80;           
        byte e=(byte)x;   // -128
        c=(byte)(c*3);
        System.out.println(a+" "+b+" "+c+" "+d+" "+e );
    }
```



## 3. 随机数生成

## 4. 进制表示

16 进制：0x01

8 进制：0123

2 进制：0b101



