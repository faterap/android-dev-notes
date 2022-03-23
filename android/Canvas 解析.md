# Canvas 解析

## 1. Region Op（裁剪功能）

A:表示第一个裁剪的形状;

B:表示第二次裁剪的形状;

1. Region.Op.DIFFERENCE ：是A形状中不同于B的部分显示出来
2. Region.Op.REPLACE：是只显示B的形状
3. Region.Op.REVERSE_DIFFERENCE ：是B形状中不同于A的部分显示出来，这是没有设置时候默认的
4. Region.Op.INTERSECT：是A和B交集的形状
5. Region.Op.UNION：是A和B的全集
6. Region.Op.XOR：是全集形状减去交集形状之后的部分

## 2. save() 和 restore()



## 3. saveLayer()

https://www.yimipuzi.com/1139.html



## 4. Translate/scale/rotate/skew

https://juejin.cn/post/6844903428896849933



[](http://wuxiaolong.me/2015/12/06/PaintCanvas/)

https://www.cnblogs.com/tianzhijiexian/p/4300988.html