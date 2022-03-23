# ArrayMap 和 SparseArray

### 1. HashMap

HashMap内部是使用一个默认容量为16的数组来存储数据的，而数组中每一个元素却又是一个链表的头结点，所以，更准确的来说，HashMap内部存储结构是使用哈希表的拉链结构（数组+链表）,**且每一个结点都是Entry类型**(也就是键值对)。

![img](https://img-blog.csdn.net/20150820130200565)

### 2. ArrayMap原理

#### 2.1 ArrayMap对象存储格式

- mHashes是一个记录所有key的hashcode值组成的数组，是从小到大的排序方式；
- mArray是一个记录着key-value键值对所组成的数组，是mHashes大小的2倍；

![arrayMap](http://gityuan.com/images/arraymap/arrayMap.jpg)

#### 2. 如何解决 hash 冲突

![ArrayMap memeory.jpg](https://user-gold-cdn.xitu.io/2019/1/17/1685788f707aaf51?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



### 3. SparseArray 原理

ArrayMap是Android专门针对内存优化而设计的，用于取代Java API中的HashMap数据结构。为了更进一步优化key是int类型的Map，Android再次提供效率更高的数据结构SparseArray，可避免自动装箱过程。对于key为其他类型则可使用ArrayMap。HashMap的查找和插入时间复杂度为O(1)的代价是牺牲大量的内存来实现的，而SparseArray和ArrayMap性能略逊于HashMap，但更节省内存。



### 4. 总结

从以下几个角度总结一下：

- 数据结构
  - ArrayMap和SparseArray采用的都是两个数组，Android专门针对内存优化而设计的
  - HashMap采用的是数据+链表+红黑树
- 内存优化
  - ArrayMap比HashMap更节省内存，综合性能方面在数据量不大的情况下，推荐使用ArrayMap；
  - Hash需要创建一个额外对象来保存每一个放入map的entry，且容量的利用率比ArrayMap低，整体更消耗内存
  - SparseArray比ArrayMap节省1/3的内存，但SparseArray只能用于key为int类型的Map，所以int类型的Map数据推荐使用SparseArray；
- 性能方面：
  - ArrayMap查找时间复杂度O(logN)；ArrayMap增加、删除操作需要移动成员，速度相比较慢，对于个数小于1000的情况下，性能基本没有明显差异
  - HashMap查找、修改的时间复杂度为O(1)；
  - SparseArray适合频繁删除和插入来回执行的场景，性能比较好
- 缓存机制
  - ArrayMap针对容量为4和8的对象进行缓存，可避免频繁创建对象而分配内存与GC操作，这两个缓存池大小的上限为10个，防止缓存池无限增大；
  - HashMap没有缓存机制
  - SparseArray有延迟回收机制，提供删除效率，同时减少数组成员来回拷贝的次数
- 扩容机制
  - ArrayMap是在容量满的时机触发容量扩大至原来的1.5倍，在容量不足1/3时触发内存收缩至原来的0.5倍，更节省的内存扩容机制
  - HashMap是在容量的0.75倍时触发容量扩大至原来的2倍，且没有内存收缩机制。HashMap扩容过程有hash重建，相对耗时。所以能大致知道数据量，可指定创建指定容量的对象，能减少性能浪费。
- 并发问题
  - ArrayMap是非线程安全的类，大量方法中通过对mSize判断是否发生并发，来决定抛出异常。但没有覆盖到所有并发场景，比如大小没有改变而成员内容改变的情况就没有覆盖
  - HashMap是在每次增加、删除、清空操作的过程将modCount加1，在关键方法内进入时记录当前mCount，执行完核心逻辑后，再检测mCount是否被其他线程修改，来决定抛出异常。这一点的处理比ArrayMap更有全面。

ConcurrentModificationException这种异常机制只是为了提醒开发者不要多线程并发操作，这里强调一下千万不要并发操作ArrayMap和HashMap。 本文还重点介绍了ArrayMap的缺陷，这个缺陷是由于在开发者没有遵循非线程安全来不可并发操作的原则，从而引入了脏缓存导致其他人掉坑的问题。从另外类ArraySet来看，Google是知道有ClassCastException异常的问题，无法追溯根源，所以谷歌直接catch住这个异常，然后把缓冲池清空，再创建数组。 这样也不失为一种合理的解决方案，唯一遗憾的是触发这种情况时会让缓存失效，由于这个清楚是非常概率，绝大多数场景缓存还是有效的。

最后说一点，ArrayMap这个缺陷是极低概率的，并且先有人没有做好ArrayMap的并发引入的坑才会出现这个问题。只要大家都能保证并发安全也就没有这个缺陷，只有前面讲的优势。





------

[]: http://gityuan.com/2019/01/13/arraymap/	"深度解读ArrayMap优势与缺陷"

