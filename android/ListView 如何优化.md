# ListView 优化

![img](https://img-blog.csdn.net/20150809205925708?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)



### 使用可回收的view

convertView：参数convertView实际上就是一个之前提到的可回收的View。当ListView要回收这个View的时候，它的数据就会被清空。因此，当convertView不为null的时候，只需要将数据填充到里面，而不用Inflate一个新的View。



### 使用 ViewHolder

ViewHolder就是用来存储那些在你的getView()方法中调用findViewById()方法得到的View。ViewHolder是一个非常轻巧的内部类。**它存储那些Item内部的View的直接引用**。然后可以在Inflate结束之后，将ViewHolder对象存储在Item的tag当中。以这种方式，你只需要在第一次创建Item的时候调用findViewById就可以了。



### 异步加载

让那些需要IO操作或者耗费CPU资源的操作在一个额外的线程中运行。为了实现这个目的，你任然需要遵循ListView的View回收机制的规则。例如，当你的Adapter的getView()正在使用AsyncTask加载一张图片。需要这张图片的Item可能已经在图片加载完成之前就已经被ListView的View回收机制回收了。因此你必须要知道，当你完成异步加载的时候，对应的Item是否已经被回收了。



### 设置缓存

ListView 可以通过三级缓存进行优化：

- 内存
- 文件
- 网络

简要概括就是在getView中，如果加载过一个图片，放入Map类型的一个MemoryCache中来维护一个试用LRU的堆。如果这里获取不到，根据View被Recycle之前放入的TAG中记录的uri从文件系统中读取文件缓存。如果本地都找不到，再去网络中异步加载。

**注意的优化点**：

1. 从文件系统中加载图片也没有内存中加载那么快，甚至可能内存中加载也不够快。因此在ListView中应设立busy标志位，当ListView滚动时busy设为true，停止各个view的图片加载。否则可能会让UI不够流畅用户体验度降低。
2. 文件加载图片放在子线程实现，否则快速滑动屏幕会卡
3. 开启网络访问等耗时操作需要开启新线程，应使用线程池避免资源浪费，最起码也要用AsyncTask。
4. Bitmap从网络下载下来最好先放到文件系统中缓存。这样一是方便下一次加载根据本地uri直接找到，二是如果Bitmap过大，从本地缓存可以方便的使用Option.inSampleSize配合Bitmap.decodeFile(ui, options)或Bitmap.createScaledBitmap来进行内存压缩