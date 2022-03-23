# ImageView解析

## foreground、src 和 background 属性区别

1）`background`指的是背景，`foreground`指的是前景，而`src`指的是内容；三者可以同时使用；
2）`src`填入图片时，是按照图片大小直接填充，并不会进行拉伸；而使用`background`和`foreground`填入图片，则是会根据ImageView给定的宽度来进行拉伸；
3）`background`和`foreground`是所有view都有的属性，总是缩放到view的大小，不受`scaleType`影响；而`src`是ImageView特有属性，它会受到`scaleType`的影响。