# Android 绘制流程

### 1. 流程图

`ViewRootImpl` 是连接 `WindowManager` 和 `DecorView` 的纽带，测量、放置和绘制三大流程都是通过 ViewRootImpl 实现的。

在 `ActivityThread` 的 handleResumeActivity() 方法中，会调用 WindowManager 的 addView() 方法，而具体添加 DecorView 的操作是在 `WindowManagerGlobal` 中。

在 WindowManagerGlobal 的 addView() 方法中，会把 DecorView 添加到 Window 中，同时会创建 ViewRootImpl ，并调用 ViewRootImpl 的 setView() 方法 把 ViewRootImpl 和 DecorView 关联起来。

View 的绘制流程是从 ViewRootImpl 的 `performTraversals()` 方法开始的，它经过测量（measure）、放置（layout）和绘制（draw）三个过程才能把一个 View 绘制出来，measure() 方法用于测量 View 的宽高，layout() 用于确定 View 在父容器中的放置位置，draw() 负责做具体的绘制操作。



![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0ba56d52432b4f7ebbf8984400e335ff~tplv-k3u1fbpfcp-watermark.image)



### 2. ViewRootImpl和DecorView

在介绍View的三大流程之前，我们先介绍一下ViewRootImpl和DecorView的基本概念。 

ViewRootImpl是连接WindowManager和DecorView的纽带，View的measure、layout、draw三大流程均是通过ViewRootImpl开始的。 

View的绘制流程是从ViewRoot的`performTraversals`开始的，经过measure、layout、draw才能将一个View绘制出来。

`performTraversals`方法会由`doTraversal`调用，`doTraversal`又被封装在`TraversalRunnable`里面，`TraversalRunnable`会在`scheduleTraversals`方法注册到`Choreographer`中，在下一个VSync信号到达的时候进行触发。 `invalidate`、`requestLayout`等等方法都会调用`scheduleTraversals`。如图：

![View绘制基本流程](https://blog.yorek.xyz/assets/images/android/View%E7%BB%98%E5%88%B6%E5%9F%BA%E6%9C%AC%E6%B5%81%E7%A8%8B.png)

measure过程决定了View的宽高，Measure完成后，可以通过getMeasuredWidth和getMeasuredHeight方法来获取View测量后的宽高。

需要注意的ViewGroup继承至View，所以ViewGroup的measure方法实际上就是View的measure方法。View会在measure方法中调用onMeasure方法，而ViewGroup会在onMeasure中完成对子View的measure操作。如果子元素也是ViewGroup，也会沿着控件树继续传递下去，直到最后的节点是View为止。

### 3. 如何让View刷新

View重绘和更新可以使用`invalidate()`和`requestLayout()`方法，其主要区别如下：

- `invalidate()`方法只会执行onDraw方法
- `requestLayout()`只会执行onMeasure方法和onLayout方法，并不会执行onDraw方法

所以当我们进行View更新时，若仅View的显示内容发生改变且新显示内容不影响View的大小、位置，则只需调用`invalidate()`方法；若View宽高、位置发生改变且显示内容不变，只需调用`requestLayout()`方法；若两者均发生改变，则需调用两者，按照View的绘制流程，推荐先调用`requestLayout()`方法再调用`invalidate()`方法。

与`invalidate()`方法类似的还有一个`postInvalidate()`，两者作用都是刷新View，区别在于：

- `invalidate`方法用于UI线程中重新刷新View
- `postInvalidate`方法用于非UI线程中重新刷新View，这里借助了`ViewRootHandler`来达成目的 

`ViewRootHandler`看着比较陌生，其实我们经常接触到。比如我们调用`View.post(Runnable)`方法，处理Runnable的就是这个`ViewRootHandler`了。详细可以参考[Android消息机制——主线程的消息循环](https://blog.yorek.xyz/android/framework/Android消息机制/#3)中的第二个问题。



### 4. 自定义View

#### 4.1 自定义View的分类[¶](https://blog.yorek.xyz/android/framework/View的绘制原理/#51-view)

自定义View大致可以分为四类：

1. 继承View重写onDraw方法
   此方法主要用于实现一些不方便通过组合控件的方式来实现的不规则的效果。很显然，这只能通过`onDraw`绘制实现。**采用这种方式需要支持wrap_content，且padding也需要处理。**
2. 继承ViewGroup重写onMeasure、onLayout
   这种方式主要实现组合控件的自定义布局。**需要合适的处理ViewGroup的测量、布局这两个过程，并同时处理子元素的测量和布局。**
3. 继承特定的View
   此方法一般用于拓展某种已有的View功能，不需要自己支持wrap_content以及padding。
4. 继承特定的ViewGroup
   一般用来实现组合控件。采用这种方式不需要自己处理测量和布局两个过程。

#### 4

#### .2 自定义View注意事项[¶](https://blog.yorek.xyz/android/framework/View的绘制原理/#52-view)

1. 让View支持wrap_content
   直接继承View或ViewGroup的控件，如果不在`onMeasure`对wrap_content特殊处理，那么wrap_content无法正常使用。
2. 如有必要，让View支持padding
   直接继承View的控件，如果不在draw方法中处理paidding，那么padding属性无法起作用。直接继承ViewGroup的控件需要在`onMeasure`和`onLayout`中考虑自身的padding和子元素的margin，不然导致失效。
3. 如要需要在View中使用Handler，用`post(Runnable)`方法代替 
4. View中如果有线程或者动画，需要在适当的时候停止
   如果有线程或者动画需要停止时，可以在`onDetachedFromWindow`中停止。当包含View的Activity退出或者当前View被remove时，View的此方法会回调。与此方法对应的是`onAttachedFromWindow`。当包含此View的Activity启动时会回调。同时，当View变得不可见时，我们也需要停止，否则有可能会造成内存泄露。

