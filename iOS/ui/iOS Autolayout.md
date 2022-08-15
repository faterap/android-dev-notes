# iOS AutoLayout

## 约束

### 优先级

AutoLayout中添加的约束也有优先级,优先级的数值是1~1000。分为两种情况：

- 一种情况是我们经常添加的各种约束,默认的优先级是1000，也就是最高级别，条件允许的话系统会满足我们所有的约束需求。
- 另外一种情况就是固有约束(intinsic content size)，严格来说这个这个更像是指UILabel和UIButton控件的一种属性,但是在AutoLayout中这个属性的取值和约束优先级的属性相结合才能完成图形的绘制。

固有约束分为两种：

1. Content Hugging Priority
   官方文档的解释是
   `Returns the priority with which a view resists being made larger than its intrinsic size.`
   即表示的是控件的抗拉伸优先级，优先级越高，越不易被拉伸，默认为251
2. Content Compression Resistance Priority
   `Returns the priority with which a view resists being made smaller than its intrinsic size.`
   这个优先级的字面意思很明确了，是防压缩优先级，优先级越高，越不易被压缩，默认为750

 为什么约束需要优先级？因为有的时候2个约束可能会有冲突。 比如：有一个UIView 距离父UIView的左右距离都是0，若此时再给此UIView加一个宽度约束，比如指定宽度为100，那么就会产生约束冲突了。在涉及冲突发生时，Auto Layout 会尝试 break 一些优先级低的约束，尽量满足最多并且优先级最高的约束。

- 优先级只有在两个约束有冲突的时候才起作用，优先级高的会覆盖优先级低的，默认的优先级为1000。  
- 设置过优先级后，会优先满足优先级高的，并且控制台不会打印冲突日志，非默认优先级的约束不一定要满足。

### Intrinsic content size

> The natural size for the receiving view, considering only properties of the view itself.
>
> 可以简单理解为View默认的高度或者宽度

| View                                       | Intrinsic content size                 |
| ------------------------------------------ | -------------------------------------- |
| UIView and NSView                          | No intrinsic content size.             |
| Sliders                                    | Defines only the width                 |
| Labels, buttons, switches, and text fields | Defines both the height and the width. |
| Text views and image views                 | Intrinsic content size can vary.       |



https://www.jianshu.com/p/1410f4eab8b3
