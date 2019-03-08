# 硬件加速

## 背景
从Android 3.0（API Level 11）开始, 2D渲染过程就全面支持硬件加速了，也就是说所有在View的Canvas上执行的绘制操作都是使用GPU了。同时，Android平台为在不同的层次为应用程序提供了配置接口或API接口，用来指定程序是否启用硬件加速。比如，你可以在manifest文件中显式设置android:hardwareAccelerated值

```
<applicationandroid:hardwareAccelerated="true" ...>
```

表示整个应用程序都将启用硬件加速，也可以为某个Activity或者View设置hardwareAccelerated属性。
