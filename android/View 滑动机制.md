# View 滑动机制

## Scroller

http://chanthuang.github.io/2016/08/31/Android-View-的滚动原理和-Scroller、VelocityTracker-类的使用/

https://juejin.cn/post/6844903922671288333#heading-10 Android Scroller 滑动机制

https://blog.csdn.net/guolin_blog/article/details/48719871

https://blog.csdn.net/IT_XF/article/details/83344780

https://juejin.cn/post/6844903750629326862#heading-2 自定义View卷尺参考



## computeSroll

当view发生滚动的时候，会调用一次这个方法，所以连续滚动的时候就会连续调用这个方法，还记得滚动会发生啥事吧，如果你还记得上面我提到的：

```
protected int mScrollX;
protected int mScrollY;
```

这两个变量，你现在就可以这样理解，view进行滚动，也就是`mScrollX`和`mScrollY`发生变化的时候，就会调用`computeScroll`方法。



## 坐标系

ScrollX

## ![img](https://img-blog.csdn.net/20140523143516265?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYmlnY29udmllbmNl/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

- 手指向左滑动，内容将向右显示，这时 mScrollX > 0。
- 手指向右滑动，内容将向左显示，这时 mScrollX < 0.
- 手指向上滑动，内容将向下显示，这时 mScrollY < 0。
- 手指向下滑动，内容将向上显示，这时 mScrollY > 0.