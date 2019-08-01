# ConstraintLayout 解析

## 1. Barrier

Barrier 是用多个 View 作为限制源来决定自身位置的一种辅助线。和 Guideline 类似，Barrier 也可以作为「辅助线」进行使用。和 Guideline 的唯一区别是，Barrier 可以根据多个 View 决定位置。

![img](https://upload-images.jianshu.io/upload_images/2726727-8ef7d9b347ec83b3.gif?imageMogr2/auto-orient/)


## 2. Guideline

`Guideline`是只能用在ConstraintLayout布局里面的一个工具类，用于辅助布局，类似为辅助线，可以设置`android:orientation`属性来确定是横向的还是纵向的。

- 当设置为`vertical`的时候，`Guideline`的宽度为0，高度是`parent`也就是ConstraintLayout的高度
- 同样设置为`horizontal`的时候，高度为0，宽度是`parent`的宽度

**重要的是Guideline是不会显示到界面上的，默认是GONE的。**

`Guideline`还有三个重要的属性，每个`Guideline`只能指定其中一个：

- `layout_constraintGuide_begin`，指定左侧或顶部的固定距离，如100dp，在距离左侧或者顶部100dp的位置会出现一条辅助线
- `layout_constraintGuide_end`，指定右侧或底部的固定距离，如30dp，在距离右侧或底部30dp的位置会出现一条辅助线
- `layout_constraintGuide_percent`，指定在父控件中的宽度或高度的百分比，如0.8，表示距离顶部或者左侧的80%的距离。



## 3. Chain

### 创建 Chain

当两个控件双向链接的时候，如下图所示，`A`和`B`里面的两条链，加上`A`和`B`两个控件，叫做一个链条。

![](https://jowan-blog.oss-cn-shenzhen.aliyuncs.com/72588270.jpg)

### Chain Style

![](https://jowan-blog.oss-cn-shenzhen.aliyuncs.com/92220222.jpg)

## 4. Groups

您可以将某些视图分组在一起。不要把这与Android中的普通ViewGroups混淆。ConstraintLayout中的一个组**仅包含对视图ID的引用，而不将组合中的视图嵌套**。这样一来，您可以设置组中控件的可见性仅通过设置组的可见性就行了，而无需设置每个视图的可见性。这对于诸如错误屏幕或加载屏幕的事情是有用的，其中一些元素需要一次更改其可见性。



## 5. DimensionRatio

1. 首先我们在布局中添加一个View：

![-w532](http://cdn.examplecode.cn/2018-11-29-15434615973604.jpg)

此时，没有添加任何约束，显示的比例就是原始图片的比例。

1. 添加水平方向的约束：

![-w868](http://cdn.examplecode.cn/2018-11-29-15434615247851.jpg)

添加完水平方向的约束后，注意此时默认的宽高为wrap_content。

1. 将高度设置为match_constraint

![-w874](http://cdn.examplecode.cn/2018-11-29-15434615973604.jpg)

如上图：这里我们将高度设置为match_constraint，然后发现下面出现了一个三角，这个就是设置View比例的地方。

1. 设置View比例

下面我们点击这个三角形，并设置宽高的比例：

- 1：1
  ![-w855](http://cdn.examplecode.cn/2018-11-29-15434616704856.jpg)
- 1：2
  ![-w871](http://cdn.examplecode.cn/2018-11-29-15434617078743.jpg)

这里设置的是**宽度：高度**的比例，我们查看源码可以看到这个属性：
![-w360](http://cdn.examplecode.cn/2018-11-29-15434617544437.jpg)

此时我们改变View的宽度，就会发现其高度也会保持这个比例而相应地变化了：

![-w868](http://cdn.examplecode.cn/2018-11-29-15434619549050.jpg)

## 总结

本文我们是以**宽度：高度**进行View比例的设置，当然我们也可以以**高度：宽度**进行设置，道理都是一样的。

设置View的比例也是ConstraintLayout相对于传统的布局容器一个强大的功能，它使得布局更加灵活，更加容易得进行屏幕适配。