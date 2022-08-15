# Kotlin 泛型

> 逆变、协变和不变都是用来描述类型转换(type transformation)后的继承关系，其定义：如果A、B表示类型，f(⋅)表示类型转换，≤表示继承关系(比如，A≤B表示A是由B派生出来的子类)
> 
> 当 A≤B 时有 f(A)≤f(B)成立，则f(⋅)是协变（covariant）的
> 当 A≤B 时有 f(B)≤f(A)成立，则f(⋅)是逆变（contravariant）的
> 当 A≤B 时上述两个式子均不成立，即f(A)与f(B)相互之间没有继承关系，f(⋅)是不变（invariant）的

## 协变（Covariant）

```java
// Java
interface Collection<E> …… {
  void addAll(Collection<? extends E> items);
}
```

**通配符类型参数** `? extends E` 表示此方法接受 `E` *或者 E 的 一些子类型*对象的集合，而不只是 `E` 自身。 这意味着我们可以安全地从其中（该集合中的元素是 E 的子类的实例）**读取** `E`，但**不能写入**， 因为我们不知道什么对象符合那个未知的 `E` 的子类型。 反过来，该限制可以让`Collection<String>`表示为`Collection<? extends Object>`的子类型。 简而言之，带 **extends** 限定（**上界**）的通配符类型使得类型是**协变的（covariant）**。

### 例子

#### Java

1. 数组 可以编译通过

```java
Object [] arr = new String [] {"hello", "world"};
```

2. 普通对象 可以编译通过

```java
Cat cat = new Cat();
Animal animal = cat;
```

3. 泛型对象 **编译报错**!

```java
List<Cat> cats = new ArrayList<>();
List<Animal> animals = cats;
```

我们可以说**Java对于数组和普通对象是支持协变的，但是对于带有泛型类型的List则不支持协变** 。

### Kotlin

Kotlin 中表现有所不一致：

**Kotlin对数组默认不支持协变，但是对List<泛型>是支持协变的。**



## 逆变（Contravariance）

在 Java 中有 `List<? super String>` 是 `List<Object>` 的一个**超类**。

后者称为**逆变性（contravariance）**，并且对于 `List <? super String>` 你只能调用接受 String 作为参数的方法 （例如，你可以调用 `add(String)` 或者 `set(int, String)`），当然如果调用函数返回 `List<T>` 中的 `T`，你得到的并非一个 `String` 而是一个 `Object`。



## 声明处型变

与 Java 有界通配符不能用于泛型声明，不同的是，Kotlin 中`out`和`in`两个型变注解还可以用于泛型声明时，更加灵活。下面通过 Java 和 Kotlin 中对 Collection 的定义来分析：

```java
// Java 中 Collection 的定义，元素是可读写的
public interface Collection<E> extends Iterable<E> { ... }
public interface List<E> extends Collection<E> { ... }

// Collections 的 copy 方法
public static <T> void copy(List<? super T> dest, List<? extends T> src) { ... }

// 但是下面声明在 Java 中是不允许的
public interface IllegalList<? extends T> extends Collection<E> { ... }
```

但是 Kotlin 中可以通过声明处型变（型变的概念在后面会详细解释）定义只读的集合：

```kotlin
// A generic collection of elements. Methods in this interface support only read-only access to the collection
public interface Collection<out E> : Iterable<E>

// A generic collection of elements that supports adding and removing elements.
public interface MutableCollection<E> : Collection<E>, MutableIterable<E>
```

`Collection<out E>`使得 Collection 里面的元素是只读的，也使得 `Collection<Number>` 是 `Collection<Int>` 的父类，在 Kotlin 中称 Collection 的元素类型是协变的。对于协变的类型，通常不允许泛型类型作为函数的传入参数。

`in`型变注解可以使得元素类型是逆变的，只能被消费，与协变相反，通常**不允许泛型类型作为函数的返回值**，看`Comparable`的定义：

```kotlin
public interface Comparable<in T> {
    public operator fun compareTo(other: T): Int
}

fun demo(x: Comparable<Number>) {
    // 1.0 类型为 Double，是 Number 的子类型
    x.compareTo(1.0)
    // 因为 Comparable 只能被消费，所以可以赋值给 Comoparable<Double> 的变量
    val y: Comparable<Double> = x
}
```



## 星投影

使用泛型的过程中，如果参数类型未知时，在 Java 中可以使用原始类型（Raw Types），但是 Java 的原始类型是类型不安全的：

```java
ArrayList<String> list = new ArrayList<>(5);

ArrayList unkownList = list;
Object first = unkownList.get(0);
unkownList.add(1);  // warning: Unchecked call to 'add(E)'
unkownList.add("1"); // warning: Unchecked call to 'add(E)'
```

而在 Kotlin 中，在参数类型未知时，可以用星投影来安全的使用泛型：

```kotlin
val list = ArrayList<Int>(5)
val unkownList: ArrayList<*> = list
val first: Any = unkownList[0]
unkownList.add(1)  // error
unkownList.add("1") // error
```



## PECS 法则

你只能从中**读取**的对象为**生产者**，并称那些你只能**写入**的对象为**消费者**。他建议：“*为了灵活性最大化，在表示生产者或消费者的输入参数上使用通配符类型*”，并提出了以下助记符：

*PECS 代表生产者-Extends，消费者-Super（Producer-Extends, Consumer-Super）。*



## 总结

型变注解：

- out: 协变、生产者(get)、返回值、**子类泛型对象赋值给父类泛型对象、**
- in: 逆变、消费者(set)、参数、**父类泛型对象可以赋值给子类泛型对象**



| Java 泛型    | Java 中代码示例                                   | Kotlin 中代码示例                                  | Kotlin 泛型    |
| ------------ | ------------------------------------------------- | -------------------------------------------------- | -------------- |
| 泛型类型     | `class Box<T>`                                    | `class Box<T>`                                     | 泛型类型       |
| 泛型方法     | `<K, V> boolean method(Pair<K, V> p)`             | `fun <K, V> function(p: Pair<K, V>)`               | 泛型函数       |
| 有界类型参数 | `class Box<T extends Comparable<T>`               | `class Box<T : Comparable<T>>`                     | 泛型约束       |
| 上界通配符   | `void sumOfList(List<? extends Number> list)`     | `fun sumOfList(list: List<out Number>)`            | 使用处协变     |
| 下界通配符   | `void addNumbers(List<? super Integer> list)`     | `fun addNumbers(list: List<in Int>)`               | 使用处逆变     |
| 无           | 无                                                | `interface Collection<out E> : Iterable<E>`        | **声明处协变** |
| 无           | 无                                                | `interface Comparable<in T>`                       | **声明处逆变** |
| 原始类型     | `ArrayList unkownList = new ArrayList<String>(5)` | `val unkownList: ArrayList<*> = ArrayList<Int>(5)` | 星投影         |



------

[]: http://johnnyshieh.me/p

osts/kotlin-generic/	"Kotlin 泛型 VS Java 泛型"

https://kaixue.io/kotlin-generics/

