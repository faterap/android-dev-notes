# Android 性能优化工具

## 内存

- MAT: 
- Android Memory Profiler: [Android Profiler](https://developer.android.com/studio/preview/features/android-profiler.html?hl=zh-cn) 中的一个组件，可帮助您识别导致应用卡顿、冻结甚至崩溃的内存泄漏和流失。 它显示一个应用内存使用量的实时图表，让您可以捕获堆转储、强制执行垃圾回收以及跟踪内存分配。

### Android Memory Profiler

![memory-profiler-callouts_2x](https://developer.android.com/studio/images/profile/memory-profiler-callouts_2x.png)

内存计数中的类别如下所示：

- **Java**：从 Java 或 Kotlin 代码分配的对象内存。

- **Native**：从 C 或 C++ 代码分配的对象内存。

  即使您的应用中不使用 C++，您也可能会看到此处使用的一些原生内存，因为 Android 框架使用原生内存代表您处理各种任务，如处理图像资源和其他图形时，即使您编写的代码采用 Java 或 Kotlin 语言。

- **Graphics**：图形缓冲区队列向屏幕显示像素（包括 GL 表面、GL 纹理等等）所使用的内存。 （请注意，这是与 CPU 共享的内存，不是 GPU 专用内存。）

- **Stack**： 您的应用中的原生堆栈和 Java 堆栈使用的内存。 这通常与您的应用运行多少线程有关。

- **Code**：您的应用用于处理代码和资源（如 dex 字节码、已优化或已编译的 dex 码、.so 库和字体）的内存。

- **Other**：您的应用使用的系统不确定如何分类的内存。

- **Allocated**：您的应用分配的 Java/Kotlin 对象数。 它没有计入 C 或 C++ 中分配的对象。

#### 查看内存分配

- **Arrange by class**：基于类名称对所有分配进行分组。
- **Arrange by package**：基于软件包名称对所有分配进行分组。
- **Arrange by callstack**：将所有分配分组到其对应的调用堆栈。

#### 捕获堆转储

在类列表中，您可以查看以下信息：

- **Heap Count**：堆中的实例数。
- **Shallow Size**：此堆中所有实例的总大小（以字节为单位）。
- **Retained Size**：为此类的所有实例而保留的内存总大小（以字节为单位）。

在类列表顶部，您可以使用左侧下拉列表在以下堆转储之间进行切换：

- **Default heap**：系统未指定堆时。
- **App heap**：您的应用在其中分配内存的主堆。
- **Image heap**：系统启动映像，包含启动期间预加载的类。 此处的分配保证绝不会移动或消失。
- **Zygote heap**：写时复制堆，其中的应用进程是从 Android 系统中派生的。

在 **Instance View** 中，每个实例都包含以下信息：

- **Depth**：从任意 GC 根到所选实例的最短 hop 数。
- **Shallow Size**：此实例的大小。
- **Retained Size**：此实例支配的内存大小（根据 [dominator 树](https://en.wikipedia.org/wiki/Dominator_(graph_theory))）。

