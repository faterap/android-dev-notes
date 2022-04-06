# JNI 数据类型

### 1. 种类

Java中有两种类型：**基本数据类型(int、float、char等)**和**引用类型(类、对象、数组等)**。JNI定义了一个C/C++类型的集合，集合中每一个类型对应于Java中的每一个类型，其中，对于基本类型而言，JNI与Java之间的映射是一对一的，比如Java中的int类型直接对应于C/C++中的jint；而对引用类型的处理却是不同的，JNI把Java中的对象当作一个C指针传递到本地函数中，这个指针指向JVM中的内部数据结构，而内部数据结构在内存中的存储方式是不可见的，本地代码必须通过在JNIEnv中选择适当的JNI函数来操作JVM中的对象。比如，对于java.lang.String对应的JNI类型是jstring，本地代码只能通过GetStringUTFChars这样的JNI函数来访问字符串的内容。以下是JNI数据类型映射关系表，通过这种映射JNI就可以正确识别并转换Java数据类型。



基本数据类型转换表：

| Java    | Native类型 | 符号属性 | 字长 |
| ------- | ---------- | -------- | ---- |
| boolean | jboolean   | 无符号   | 8位  |
| byte    | jbyte      | 无符号   | 8位  |
| char    | jchar      | 无符号   | 16位 |
| short   | jshort     | 有符号   | 16位 |
| int     | jint       | 有符号   | 32位 |
| long    | jlong      | 有符号   | 64位 |
| float   | jfloat     | 有符号   | 32位 |
| double  | jdouble    | 有符号   | 64位 |



引用数据类型的转换：

| Java引用类型            | Native类型    | Java引用类型 | Native类型     |
| ----------------------- | ------------- | ------------ | -------------- |
| **All object**          | **jobject**   | char[]       | jcharArray     |
| **java.lang.class实例** | **jclass**    | short[]      | jshortArray    |
| java.lang.String实例    | jstring       | int[]        | jintArray      |
| object[]                | jobjectArray  | short[]      | jshortArray    |
| boolean[]               | jbooleanArray | long[]       | **jlongArray** |
| byte[]                  | jbyteArray    | double[]     | jdoubleArray   |
| java.lang.Throwable实例 | jthrowable    | float[]      | jfloatArray    |



### 2. 字符串处理

 在JNI开发中，jstring类型指向JVM内部的一个字符串和C字符串类型char*是不同的，前者编码格式为Unicodec(UTF-16)，后者为UTF-8。因此，我们不能简单的将jstring当作一个普通的C字符串来使用，必须要使用合适的JNI函数把jstring转化为C/C++字符串，即GetStringUTFChars。该函数可以把一个jstring指针(指向JVM内部的Unicode字符序列)，转化为一个UTF-8格式的C字符串。需要注意的是，如果jstring字符串中包含中文，还需要将UTF-8转化为GB2312，否则会出现中文乱码。



------

[]: https://blog.csdn.net/AndrExpert/article/details/72851294	"详解JNI数据类型与C/C++、Java之间的互调"

