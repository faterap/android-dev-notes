# Rxjava 中间的坑

## 操作符

### 1. Observable.from(list)

判断 list 是否为空的时候，容易在 isEmpty() 之前加 toList() 操作符。否则就会每个元素判断一下是否为空！！！

### 2. isEmpty()

toList() 和 isEmpty() 组合使用时候，并不能判断 list 是否为空。因为 isEmpty() 只是判断有没有发射数据，toList() 之后，被观察者其实发送了 list 一个数据！！！

### 3. 收不到元素

看看是不是autoDispose的锅！！！