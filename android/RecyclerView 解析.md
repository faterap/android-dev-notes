# RecyclerView 解析

## 总体设计

### 1. 数据展示

![5c7b96d7c0fc3](https://i.loli.net/2019/03/03/5c7b96d7c0fc3.png)



## LayoutManager

![5c7b96ecd6f5b](https://i.loli.net/2019/03/03/5c7b96ecd6f5b.png)

### 1. Adapter position 和 Layout position

`RecyclerView`在`RecyclerView.Adapter`和`RecyclerView.LayoutManager`中引进了一个抽象的额外中间层来保证在布局计算的过程中能批量的监听到数据变化。这样介绍了`LayoutManager`追踪`adapter`数据变化来计算动画的时间。因为所有的`View`绑定都是在同一时间执行，所以这样也提高了性能和避免了一些非必要的绑定。
因为这个原因，在`RecylcerView`中有两种`position`类型相关的方法:

> `layout position`: 
>
> 1. 在最近一次`layout`计算是`item`的位置。这是`LayoutManager`角度中的位置。
> 2. 返回布局中最新的计算位置，和用户所见到的位置一致，当做用户输入（例如点击事件）的时候考虑使用
>
> `adapter position`: 
>
> 1. `item`在`adapter`中的位置。这是从`Adapter`的角度中的位置。
>
> 2. 返回数据在Adapter中的位置（也许位置的变化还未来得及刷新到布局中），当使用Adapter的时候（例如调用Adapter的notify相关方法时）考虑使用
>

这两种`position`除了在分发`adapter.notify`事件与之后计算布局更新的这段时间之内外都是相同的。
可以通过`getLayoutPosition(),findViewHolderForLayoutPosition(int)`方法来获取最近一次布局计算的`LayoutPosition`。这些`positions`包括从最近一次布局计算的所有改变。你可以根据这些位置来方便的得到用户当前从屏幕上所看到的。例如，如果在屏幕上有一个列表，用户请求的是第五个条目，你可以通过该方法来匹配当前用户正在看的内容。

另一种`AdapterPosition`相关的方法是`getAdapterPosition(),findViewHolderForAdapterPosition(int)`，当及时一些数据可能没有来得及被展现到布局上时便需要获取最新的`adapter`位置可以使用这些相关的方法。例如，如果你想获取一个条目的`ViewHolder`的`click`事件时，你应该使用`getAdapterPosition()`。需要知道这些方法在`notifyDataSetChange()`方法被调用和新布局还没有被计算之前是不能使用的。鉴于这个原因，你应该小心的去处理这些方法有可能返回`NO_POSITION`或者`null`的情况。

下图能更直观的了解:
[![image](https://raw.githubusercontent.com/CharonChui/Pictures/master/RecyclerView.png?raw=true)](https://raw.githubusercontent.com/CharonChui/Pictures/master/RecyclerView.png?raw=true)

## RecyclerPool



## ItemDecoration



## ItemAnimator