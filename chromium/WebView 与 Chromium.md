# WebView 与 Chromium



## 渲染方式

### Content shell

下面详细描述Content Shell的渲染方式。图中有多个线程，其中标注橙色的是在Browser进程中，标注另外颜色的是Renderer进程。在Renderer线程中，主要是渲染树和合成树，同时有个合成器线程，根据之前章节的描述，它负责合成网页的各个层。在Android系统上，Chromium中的GPU工作变成一个线程，工作在Browser进程中，具体功能之前介绍过。

在Browser进程中，还有一个浏览器测的合成器线程，它的主要工作是将Renderer进程中合成结果和浏览器界面的一些内容合成起来，输出到SurfaceView对象。因为Android上SurfaceView的特性，所以可以为它创建单独的EGLContext，合成器线程可以结果绘制到该SurfaceView对象中显示出来。

在这一过程中，Chromium并**不需要UI线程**的参与，所有的工作都是在其它线程来完成的，所以UI线程不会被阻塞。不过，因为Android系统上的动画、变换等并能作用于SurfaceView对象，所以浏览器没有关系，但是用来嵌入其它UI界面，可能不能满足开发者的需求。具体的过程如下图所示。

![](https://img-blog.csdn.net/20140914180334343?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWlsYWRvX25qdQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)



### WebView

![](https://img-blog.csdn.net/20140914180543375?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWlsYWRvX25qdQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

因为Chromium WebView没有多进程架构，所以图中所有线程都**工作在主进程**中。首先看渲染线程，它也不再工作在Renderer进程。同Content Shell类似，它也是包括渲染树和合成树。GPU线程也是类似的功能。不同之处在于同步的合成器，将**网页的合成器放在主线程中来工作**，这里主要的问题在于，需要绘制网页内容到UI中的时候需要在UI线程。

在Android系统中，如果用户界面设置的渲染方式硬件加速渲染方式，那么Android会使用内部HwUI机制。该机制会为每个Activity的内部View（ViewRootImpl类）构建一个HardwareRenderer，该对象能够创建EGLSurface和EGLContext，并将绘图动作转变成OpenGLES的操作。当绘制界面内容的时候，会充值当前EGLContext为自己创建的，逐次调用每个View的绘图操作。当绘制到WebView对象的时候，它会调用同步合成器将网页的结果绘制到当前的FBO中。下图是Android系统如何调用WebView的绘图网页内容的过程。

![](https://img-blog.csdn.net/20140914180549865?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWlsYWRvX25qdQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

除了总体上的绘制机制存在明显的不同之外，两者还存在对某些层的绘制方法不一致的地方。这里的主要原因在于Chromium WebView能够使用系统内部的函数，而作为App的Content Shell缺不行。主要针对于那些常见的层，也就是基本文字图片等层PictureLayer，如下图所示。

![](https://img-blog.csdn.net/20140914180355203?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWlsYWRvX25qdQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

左侧是Content Shell所使用的机制，右侧是Chromium WebView所使用的机制。因为跨进程的原因，Chromium使用共享内存将SkPicture绘制的内容（CPU内存中保存）通过共享内存传到Browser进程，并上传到GPU的纹理对象。右侧则是使用“gralloc”函数来分配”graphic buffer”，SkPicture直接将网页的一层绘制到该缓冲区对象上。

相信随着时间的推移，ChromiumWebView会越来越接近Content Shell，不过如果Android图形系统不改变，那么在某些方面Chromium WebView仍然不会赶上Content Shell的性能，特别是将网页的结果合成部分必须在UI线程来进行，必然会阻碍UI线程对用户事件等的响应。

[]: https://blog.csdn.net/milado_nju/article/details/39271463	"理解WebKit和Chromium: Chromium WebView和Chrome浏览器渲染机制"


