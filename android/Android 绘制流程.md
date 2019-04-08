# Android 绘制流程

## 流程

![这里写图片描述](https://img-blog.csdn.net/20150529090922419)



### onMeasure()

#### 测量模式

- **MeasureSpec.EXACTLY**是精确尺寸， 当我们将控件的layout_width或layout_height指定为具体数值时如andorid:layout_width=”50dip”，或者为FILL_PARENT是，都是控件大小已经确定的情况，都是精确尺寸。
- **MeasureSpec.AT_MOST**是最大尺寸，当控件的layout_width或layout_height指定为WRAP_CONTENT时 ，控件大小一般随着控件的子空间或内容进行变化，此时控件尺寸只要不超过父控件允许的最大尺寸即可。因此，此时的mode是AT_MOST，size给出了父控件允许的最大尺寸。
- **MeasureSpec.UNSPECIFIED**是未指定尺寸，这种情况不多，一般都是父控件是AdapterView,通过measure方法传入的模式。



![这里写图片描述](https://img-blog.csdn.net/20150529163050000)

measure过程主要就是从顶层父View向子View递归调用view.measure方法（measure中又回调onMeasure方法）的过程



### onLayout()

![这里写图片描述](https://img-blog.csdn.net/20150529163153998)

### onDraw()

![这里写图片描述](https://img-blog.csdn.net/20150530154328068)



draw流程总结：

可以看见，绘制过程就是把View对象绘制到屏幕上，整个draw过程需要注意如下细节：

- 如果该View是一个ViewGroup，则需要递归绘制其所包含的所有子View。

- View默认不会绘制任何内容，真正的绘制都需要自己在子类中实现。

- View的绘制是借助onDraw方法传入的Canvas类来进行的。

- 区分View动画和ViewGroup布局动画，前者指的是View自身的动画，可以通过setAnimation添加，后者是专门针对ViewGroup显示内部子视图时设置的动画，可以在xml布局文件中对ViewGroup设置layoutAnimation属性（譬如对LinearLayout设置子View在显示时出现逐行、随机、下等显示等不同动画效果）。

- 在获取画布剪切区（每个View的draw中传入的Canvas）时会自动处理掉padding，子View获取Canvas不用关注这些逻辑，只用关心如何绘制即可。

默认情况下子View的ViewGroup.drawChild绘制顺序和子View被添加的顺序一致，但是你也可以重载ViewGroup.getChildDrawingOrder()方法提供不同顺序。

## View.invalidate()

![这里写图片描述](https://img-blog.csdn.net/20150531111928069)

