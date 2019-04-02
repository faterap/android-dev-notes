# ArrayMap 和 SparseArray

## HashMap

HashMap内部是使用一个默认容量为16的数组来存储数据的，而数组中每一个元素却又是一个链表的头结点，所以，更准确的来说，HashMap内部存储结构是使用哈希表的拉链结构（数组+链表）,**且每一个结点都是Entry类型**(也就是键值对)。

![img](https://img-blog.csdn.net/20150820130200565)

## 原理

### ArrayMap对象存储格式

- mHashes是一个记录所有key的hashcode值组成的数组，是从小到大的排序方式；
- mArray是一个记录着key-value键值对所组成的数组，是mHashes大小的2倍；

![arrayMap](http://gityuan.com/images/arraymap/arrayMap.jpg)

### 如何解决 hash 冲突

![ArrayMap memeory.jpg](https://user-gold-cdn.xitu.io/2019/1/17/1685788f707aaf51?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



------

[]: http://gityuan.com/2019/01/13/arraymap/	"深度解读ArrayMap优势与缺陷"

