# ImageView 属性

## 1. src 和 background

background 会根据 ImageView 组件给定的长宽进行拉伸, 而 src 就存放的是原图的大小, 不会进行拉伸。src 是图片内容（前景）, bg 是背景, 可以同时使用。

此外: scaleType 只对 src 起作用；bg 可设置透明度, 比如在 ImageView 中就可以用 android:scaleType 控制图片的缩放方式。



## 2. scaleType

