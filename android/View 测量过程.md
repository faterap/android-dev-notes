# View 测量过程

![这里写图片描述](https://img-blog.csdn.net/20150529163050000)

## ViewGroup的职责

ViewGroup相当于一个放置View的容器，并且我们在写布局xml的时候，会告诉容器（**凡是以layout为开头的属性，都是为用于告诉容器的**），我们的宽度（layout_width）、高度（layout_height）、对齐方式（layout_gravity）等；当然还有margin等；于是乎，ViewGroup的职能为：给childView计算出**建议的宽和高和测量模式** ；决定childView的位置；为什么只是建议的宽和高，而不是直接确定呢，别忘了childView宽和高可以设置为wrap_content，这样只有childView才能计算出自己的宽和高。

## MeasureSpec

> MeasureSpec到底是什么？？？
>
> MeasureSpec是父控件提供给子View的一个参数，作为设定自身大小参考，只是个参考，要多大，还是View自己说了算。

在Measure流程中，系统会将View的LayoutParams根据父容器所施加的规则转换成对应的MeasureSpec，然后在onMeasure方法中根据这个MeasureSpec来确定View的测量宽高。 MeasureSpec由size和mode组成，mode包括三种，UNSPECIFIED、EXACTLY、AT_MOST，**size就是配合mode给出的参考尺寸**

- **MeasureSpec.EXACTLY **是精确尺寸， 当我们将控件的layout_width或layout_height指定为具体数值时如andorid:layout_width=”50dip”，是控件大小已经确定的情况，都是精确尺寸。一般当childView设置其宽、高为精确值、match_parent时，ViewGroup会将其设置为EXACTLY。
  **这时的MeasureSpec一般是父控件根据自身的MeasureSpec跟子View的布局参数来确定的，一般size>0，有确定值。**
- **MeasureSpec.AT_MOST** 是最大尺寸，当控件的layout_width或layout_height指定为WRAP_CONTENT时 ，控件大小一般随着控件的子空间或内容进行变化，此时控件尺寸只要不超过父控件允许的最大尺寸即可。因此，此时的mode是AT_MOST，size给出了父控件允许的最大尺寸。
  **这种模式也是父控件根据自身的MeasureSpec跟子View的布局参数来确定的，一般是子View布局参数采用wrap_content时。**
- **MeasureSpec.UNSPECIFIED**：父View不对子View有任何限制，子View需要多大就多大。一般出现在AadapterView的item的heightMode中、ScrollView的childView的heightMode中；此种模式比较少见。

对于顶级 View（无上级 View，DecorView），其 MeasureSpec 是有窗口的尺寸和自身的 `Layoutparams` 共同确定的；而普通 View（包括 ViewGroup） 其 MeasureSpec 由父容器的 MeasureSpec 和本身的 `LayoutParams` 确定。

## MeasureSpec与LayoutParams之间的关系

## View 测量

View 的测量主要涉及到 `measure`， `onMeasure` 和 `setMeasuredDimension` 这三个方法。

#### measure

`measure` 是被父容器调用的，根据传入的 `MeasureSpec` 结合自身的情况进行调整产生新的 `MeasureSpec`。

#### onMeasure

`onMeasure` 是在 `measure` 方法中被调用，传入新的 `MeasureSpec`。（作为回调，可以在自定义 View 中定制）

```java
protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
    setMeasuredDimension(getDefaultSize(getSuggestedMinimumWidth(), widthMeasureSpec),
            getDefaultSize(getSuggestedMinimumHeight(), heightMeasureSpec));
}
```

## ViewGroup 测量

container 本身有一个 `MeasureSpec`，上面说到 childView 的 `MeasureSpec` 由其自身 `LayoutParams` 和 container 的 `MeasureSpec` 确定。

`ViewGroup#measureChild` 中：

```java
protected void measureChild(View child, int parentWidthMeasureSpec,
        int parentHeightMeasureSpec) {
    final LayoutParams lp = child.getLayoutParams();

    final int childWidthMeasureSpec = getChildMeasureSpec(parentWidthMeasureSpec,
            mPaddingLeft + mPaddingRight, lp.width);
    final int childHeightMeasureSpec = getChildMeasureSpec(parentHeightMeasureSpec,
            mPaddingTop + mPaddingBottom, lp.height);

    child.measure(childWidthMeasureSpec, childHeightMeasureSpec);
}
```

可以看到，childView 宽度/高度 `MeasureSpec` 由 container 的 `MeasureSpec` 、自身的 `LayoutParams`和 `padding` 共同产生。

## ChildView 的 MeasureSpec

接下来详细讲一下如何利用这三者生成子 View 的 `MeasureSpec`。

假设我们是在生成 childView 的宽度 `MeasureSpec`。首先我们根据 container 的 `MeasureSpec` 获取其 `SpecMode` 和 `SpecSize`。将 `SpecSize` 减去 `padding` 即可得到父容器的内容宽度。

接着，我们按照 container的 `SpecMode` 分为三种情况，可以为子View构建合适的MeasureSpec：

| **LayoutParams** \ Parent Spec mode | **EXACTLY**        | **AT_MOST**        | **UNSPECIFIED**   |
| ----------------------------------- | ------------------ | ------------------ | ----------------- |
| exact dp                            | EXACTLY childSize  | EXACTLY childSize  | EXACTLY childSize |
| match_parent                        | EXACTLY parentSize | AT_MOST parentSize | UNSPECIFIED 0     |
| wrap_content                        | AT_MOST parentSize | AT_MOST parentSize | UNSPECIFIED 0     |

## Container 自身的测量

确定了子 View 的 `MeasureSpec`，我们看回 `ViewGroup#measureChild`：

```java
protected void measureChild(View child, int parentWidthMeasureSpec,
        int parentHeightMeasureSpec) {
    final LayoutParams lp = child.getLayoutParams();

    final int childWidthMeasureSpec = getChildMeasureSpec(parentWidthMeasureSpec,
            mPaddingLeft + mPaddingRight, lp.width);
    final int childHeightMeasureSpec = getChildMeasureSpec(parentHeightMeasureSpec,
            mPaddingTop + mPaddingBottom, lp.height);

    child.measure(childWidthMeasureSpec, childHeightMeasureSpec);
}
```

注意到最后一句，我们调用了子 View 的 `measure` 方法，它会根据我们确定的 `MeasureSpec` 去测量自身。

## 总结

我们看回开头的例子：

```xml
<FrameLayout
    android:layout_width="200dp"
    android:layout_height="200dp">

    <TextView
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

</FrameLayout>
```

这个结构的测量流程是：

- `FrameLayout` 根据自身的 `MeasureSpec`(SpecMode=EXACTLY，SpecSize=200dp) 和 `TextView` 的 `LayoutParams`(match_parent)，产生 `TextView` 的 `MeasureSpec`(SpecMode=EXACTLY，SpecSize=200dp)
- 让 `TextView` 测量自身并确定宽高
- `FrameLayout` 获取 `TextView` 大小再确定自身大小

这里简单概括一下整个流程：测量始于DecorView，通过不断的遍历子View的measure方法，根据ViewGroup的MeasureSpec及子View的LayoutParams来决定子View的MeasureSpec，进一步获取子View的测量宽高，然后逐层返回，不断保存ViewGroup的测量宽高。

从文章开始到现在，View的测量流程已经全部分析完毕，View的measure流程是三大流程中最复杂的一个流程，其中的MeasureSpec贯穿了整个测量流程，占有非常重要的地位。

------

[]: https://blog.csdn.net/a553181867/article/details/51494058	"Android View 测量流程(Measure)完全解析"

[]: https://mindjet.github.io/coding/android/view/android-view-measurement	"Android View 测量机制"

[]: https://blog.csdn.net/lmj623565791/article/details/38339817	"Android 手把手教您自定义ViewGroup"

[]: https://juejin.im/post/5d6678dc6fb9a06ae37272ae	"[Android 自定义 View] —— 深入总结 onMeasure、 onLayout"

https://blog.yorek.xyz/android/framework/View的绘制原理/#31-measure

