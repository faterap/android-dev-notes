# Android WebView  总结

## 结构

![WebView 层次结构](https://img-blog.csdn.net/20140113204112015?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWlsYWRvX25qdQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

WebView相关的主要是上面三个部分：

- WebView接口部分，它提供对外编程接口，同时它的内部实现可以基于桥接代码。
- 桥接部分，其代码主要有两个作用，其一是实现WebView接口对实现的调用，第二是调用下面一层的代码，这里面有个重要的部分就是需要设置AwContents为了绘图而需要设置的两组函数数组，这个在渲染部分介绍。它的代码可以在frameworks/webview/chromium部分找到。以上两个部分都是AOSP部分代码。
- Android 平台支撑代码。AwContents是在Chromium项目中的，主要是构建被桥接代码使用的接口，这一部分主要基于Content接口，里面有很多不同于Chrome浏览器的实现。

## 渲染方式
Android KitKat 之后，`WebView`支持「软件渲染」和「硬件渲染」(GPU)。

新版的WebView同时支持两种渲染模式，即**软件渲染**和**硬件渲染**。简单的说，软件渲染方式就是用“CPU”这支“笔”将页面内容绘制到以位图Bitmap为材质的“纸张”上，而硬件渲染方式则是用GPU这支笔（当然也需要借助CPU）将页面内容绘制到以Texture为材质的“纸张”上。本文将深入分析新版WebView的软件渲染过程，后续将对硬件渲染做进一步讨论。

WebView是Android系统的内置组件，对组件的升级必须保证对应用程序的兼容性。换句话说，那些使用了WebView但没有启用硬件加速的应用程序也能够在Android 4.4上正常工作。这是最主要的原因。

硬件加速方式虽然看上去“高端大气上档次“，但相应地应用程序会耗费更多的系统内存，例如需要加载OpenGL的动态库，会占用一定的内容空间（大约8M）。因此，软件渲染方式提供了另一种备选方案，应用程序可以根据Android设备配置选择是否启用硬件加速，确保正常的运行。

值得提出的是，这里的硬件加速机制同Chrome浏览器的硬件加速机制是不一致的，因为Chrome浏览器为渲染网页使用的控件是Android的SurfaceView，根据Android系统的说明，SurfaceView是可以在**不同的线程来绘制**的，由此，Chrome浏览器是首先获取SurfaceView的Surface对象的句柄（ID），然后由Chrome浏览器的GPU线程来绘制网页。这样，网页的渲染工作同主线程完全隔离开来了，不会因为网页的渲染而阻碍用户界面的响应。

## 特性
### 优点
- 支持更多的HTML5特性，例如Web Socket, Web Worker, FileSystem API, Page Visibility API, CSSfilter等等。html5test.com跑分结果为448分，与Chrome浏览器非常接近了。

- 添加了对远程调试功能的支持。任何使用WebView的应用程序默认都开启了远程调试功能，通过USB线连接到开发主机上，在主机的Chrome浏览器中输入chrome://inspect就可以直接在开发主机上直接调试WebView加载的页面。开发者也可以调用 setWebContentsDebuggingEnabled开启或关闭这个功能。这对广大HTML5应用开发者绝对是一个福音。

- 更智能的内存管理策略。旧版WebView需要应用程序在系统内存不足的情况下，显式地调用freeMemory方法来释放WebView占用的内存资源，。而新版的 WebView中，freeMemory这个方法已经被标记为“deprecated”，也就是应用程序不需要自己去释放内存，WebView的内部实现已经考虑了对系统低内存运行时的响应，一旦发现系统处于低内存的运行状态，WebView会主动调整内存分配策略，尽可能释放一些已占用的内存资源。

- 支持软件渲染和硬件加速模式。Android应用程序可以在manifest文件中指定hardwareAccelerated的值表明是否启动硬件加速，为了兼容早期使用WebView的应用程序，新版WebView同时支持软件渲染和硬件渲染两种模式。

### 不足之处
- 尚有一些重量级的HTML5特性没有支持，比如WebGL，WebRTC，其中原因是WebGL对图形显卡的要求很高，而目前市面上每个厂商的硬件能力不尽相同，所有激进地开启WebGL的支持可能会导致平台的一致性问题，即同一个HTML5应用在不同的设备上行不一致，有的能跑，有的挂掉。
尚有一些轻量级但实用的HTML5 API还不支持。典型的有两个，一个是全屏Fullscreen API，另外一个是Network Information API.

- Canvas 2D没有开启硬件加速模式。Canvas2D的性能和Chrome浏览器还是有一定差距，从代码注释来看，是因为skia库中硬件加速的Canvas 2D后端Ganesh可能会导致WebView崩溃。
硬件加速渲染模式下所有对GPU的操作都发生在主线程中。UI主线程实在是太忙，如果能和Chrome一样，专门为GPU操作创建一个线程，性能还可得到进一步提升。

## 绘制模型

*简单的来说，当一个视图（View）部件的内容发生更新时，会调用invalidate()方法通知Android视图系统表明这个View的内容已经失效，视图系统会根据view层次结构计算有哪些View需要重绘，并调用View.draw方法将更新的内容绘制到传入Canvas上，对于用户自定义的View，绘制的逻辑代码实现在重载地View.onDraw方法中，决定在在Canvas上绘制什么内容。*

Android SDK中，android.webkit.WebView实际上是一个ViewGroup，并将后端的具体实现抽象为WebViewProvider，而WebViewChromium正是一个提供基于Chromium的具体实现类，对核心类AwContents做了一层简单的封装，加强了对线程安全方面的考量。

![WebView 结构图](https://img-blog.csdn.net/20140118232706453)

回到WebView的情况。当WebView部件发生内容更新时，例如页面加载完毕，CSS动画，或者是滚动、缩放操作导致页面内容更新，同样会在WebView触发invalidate方法，随后在视图系统的统筹安排下，WebView.onDraw方法会被调用，最后实际上调用了AwContents.onDraw方法，它会请求对应的native端对象执行OnDraw方法，将页面的内容更新绘制到WebView对应的Canvas上去。

## 总结

WebView只是一个普通的View部件而已，当页面内容更新时，会触发invalidate，Android视图系统收到失效消息后会要求WebView部件回调onDraw方法重绘自己。WebView重绘过程较为复杂，方法InProcessViewRenderer::OnDraw是理解和分析WebView渲染模型的入口点，Canvas对象的硬件加速属性决定了渲染路径的不同。

### 参考资料：

[]: https://blog.csdn.net/hongbomin/article/details/18499295	"理解Chromium WebView的绘制模型"

