# View 解析

## 1. measureWidth 和 Width

说得直白一点，measuredWidth 与 width 分别对应于视图绘制 的 measure 与 layout 阶段。很重要的一点是，我们要明白，View 的宽高是由 View 本身和 parent 容器共同决定的，要知道有这个 MeasureSpec 类的存在。

比如，View 通过自身 measure() 方法向 parent 请求 100x100 的宽高，那么这个宽高就是 measuredWidth 和 measuredHeight 值。但是，在 parent 的 onLayout() 方法中，通过调用 childview.layout() 方法只分配给 childview 50x50 的宽高。那么，这个 50x50 宽高就是 childview 实际绘制并显示到屏幕的宽高，也就是 width 和 height 值。

如果你对自定义 View 过程很熟练的话，理解这部分内容就比较轻松一些。事实上，开发过程中，getWidth() 和 getHeight() 方法用得更多一些。