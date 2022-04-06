# jni静态/动态注册

### 0x01. 静态注册

静态注册时，C/C++文件代码中，需要以固定的命名规则来命名导出的JNI函数：

```c++
/* 
- 命名规则：
Java_{package_and_classname}_{function_name}(JNI_arguments)`，用下划线代替"."

- 参数：
JNIEnv*: JNI环境的引用
jobject:  "this" Java对象的引用
*/

#include <jni.h>
#include <stdio.h>
JNIEXPORT void JNICALL Java_com_testing_jni_HelloWorldJNI_sayHello(JNIEnv* env, jobject thisObject) {
    printf("Hello World!\n");
}
```

除了固定的命名规则以外，还需要用JNIEXPORT和JNICALL来标识JNI方法。在/Android/SDK/ndk-bundle/……/include/jni.h中，可看到JNIEXPORT和JNICALL的宏定义：

```c++
#define JNIIMPORT
#define JNIEXPORT  __attribute__ ((visibility ("default")))
#define JNICALL
```

JNIEXPORT 确保标识的函数被正常导出。C++中提供了elf文件的[导出表隐藏功能](http://gcc.gnu.org/wiki/Visibility)，而为了JNI能找到Native方法，需要设置Visibility属性为”default“, 即确保标识函数会出现到so文件的导出表中，因此JNI接口可以找到目标方法。JNICALL 则确保函数的命名规范正确。

Native方法静态注册后，JNI环境可以很轻松地在so的导出表中，根据调用者的包名、类名和方法名，找到对应的方法。

### 0x02. 动态注册

除了使用静态注册这种传统方法实现JNI外，也可以使用RegisterNatives动态实现JNI。

JNIEnv类中提供了动态注册Native函数的方法**RegisterNatives**，这个方法可动态地将so库中的方法名，与JVM中某个类调用的Native方法名做绑定和映射，这样JNI就不需要通过函数命名规则来搜索目标函数，可直接在JNIEnv中定位目标函数。动态注册的过程，一般发生在JNI_Onload执行期间。

JVM在System.loadLibrary加载so文件时，会第一时间调用JNI_Onload函数，它有两个重要的作用：

- 指定JNI版本：告诉VM该组件使用那一个JNI版本(若未提供JNI_OnLoad()函数，VM会默认该使用最老的JNI 1.1版)，如果要使用新版本的JNI，例如JNI 1.4版，则必须由JNI_OnLoad()函数返回常量JNI_VERSION_1_4来告知VM。
- 初始化设定，进行各种资源的初始化操作。

由于JNI_Onload执行了初始化资源的操作，动态注册也一般在初始化时进行。重写JNI_Onload函数

```c++
#include <jni.h> 
#include <stdio.h>
#include <stdlib.h> 
#include <unistd.h> 
#include <sys/types.h> 
#include <sys/stat.h> 
#include <fcntl.h> 

JNIEXPORT void JNICALL sayHello(JNIEnv* env, jobject thisObject) {
    printf("Hello World!\n");
}

//Java和JNI函数的绑定表
static JNINativeMethod gMethods[] = {
        {"sayHello", "()V", (void *)sayHello},
}; 

//注册native方法到java中
static int registerNativeMethods(JNIEnv* env, const char* className,JNINativeMethod* gMethods, int numMethods){
    jclass clazz;
    clazz = (*env)->FindClass(env, className); 
    if (clazz == NULL) { 
        return JNI_FALSE;
    }
    if ((*env)->RegisterNatives(env, clazz, gMethods,numMethods) < 0){ 
        return JNI_FALSE;
    } 
    return JNI_TRUE;
} 

int register_ndk_load(JNIEnv *env){ 
    return registerNativeMethods(env, "com/testing/jni/HelloWorldJNI",gMethods,sizeof(gMethods) / sizeof(gMethods[0])); 
}

JNIEXPORT jint JNI_OnLoad(JavaVM* vm, void* reserved)
{
    JNIEnv* env = NULL;
    jint result = -1; 
    if ((*vm)->GetEnv(vm, (void**) &env, JNI_VERSION_1_4) != JNI_OK){ 
        return result;
    }
    // 动态注册
    register_ndk_load(env); 
    
    // 返回jni的版本
    return JNI_VERSION_1_4;
}
```

在C＋＋和Java中创建关联的是JNINativeMethod，保存了Java中调用的函数名和C++代码中函数名的映射关系，它在jni.h中定义：

```
/*used in RegisterNatives to describe native method name, signature, and function pointer.*/
typedef struct {
    char *name;
    char *signature;
    void *fnPtr;
} JNINativeMethod;
```

name是java中定义的native函数的名字，fnPtr是函数指针，也就是C＋＋中java native函数的实现。signature是java native函数的签名，可以认为是参数和返回值。比如(LMyJavaClass;)V，表示函数的参数是LMyJavaClass(Java类MyJavaClass)，返回值是void。对于基本类型，对应关系如下：

```
字符     Java类型     C/C++类型
V         void         void
Z         jboolean     boolean
I         jint         int
J         jlong        long
D         jdouble      double
F         jfloat       float
B         jbyte        byte
C         jchar        char
S         jshort       short
```

- 数组则以”[“开始，用两个字符表示，比如int数组表示为［I，以此类推。
- 如果参数是Java类，则以”L”开头，以”;”结尾，中间是用”/“隔开包及类名，例如Ljava/lang/String;，而其对应的C＋＋函数的参数为jobject，一个例外是String类，它对应C＋＋类型jstring。
- *(DD)I* 表示2个double的参数，返回值为void

```
static JNINativeMethod gMethods[] = {
    {"processDirectory", "(Ljava/lang/String;Ljava/lang/String;Landroid/media/MediaScannerClient;)V", (void *)android_media_MediaScanner_processDirectory},
    {"processFile", "(Ljava/lang/String;Ljava/lang/String;Landroid/media/MediaScannerClient;)V",    
(void *)android_media_MediaScanner_processFile},
    {"setLocale", "(Ljava/lang/String;)V", (void *)android_media_MediaScanner_setLocale},
    {"extractAlbumArt", "(Ljava/io/FileDescriptor;)[B",     (void *)android_media_MediaScanner_extractAlbumArt},
    {"native_init", "()V", (void *)android_media_MediaScanner_native_init},
    {"native_setup", "(DD)V", (void *)android_media_MediaScanner_native_setup},
    {"native_finalize", "(BDI)V", (void *)android_media_MediaScanner_native_finalize},
};
```

可见动态注册时，不仅仅会将C/C++函数与Java调用的函数名做映射，也会与具体的调用类class进行绑定。因此，调用第三方so文件时，**Java中定义的类名和package名都要严格对应**。

动态注册的好处是：

1. C/C＋＋中函数命名自由，不必拘泥特定的命名方式； 
2. 效率高。传统方式下，Java类call本地函数时，通常是依靠VM去动态寻找.so中的本地函数(因此它们才需要特定规则的命名格式)，而使用RegisterNatives将本地函数向VM进行登记，可以让其更有效率的找到函数； 
3. 运行时动态调整本地函数与Java函数值之间的映射关系，只需要多次call RegisterNatives()方法，并传入不同的映射表参数即可。