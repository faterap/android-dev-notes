# Native crash介绍

### 信号量

#### 1.程序崩溃

- 在Unix-like系统中，所有的崩溃都是编程错误或者硬件错误相关的，系统遇到不可恢复的错误时会触发崩溃机制让程序退出，如除零、段地址错误等。
- 异常发生时，CPU通过异常中断的方式，触发异常处理流程。不同的处理器，有不同的异常中断类型和中断处理方式。
- linux把这些中断处理，统一为信号量，可以注册信号量向量进行处理。
- 信号机制是进程之间相互传递消息的一种方法，信号全称为软中断信号。

#### 2.信号机制

函数运行在用户态，当遇到系统调用、中断或是异常的情况时，程序会进入内核态。信号涉及到了这两种状态之间的转换。

![v2-792e0559464e45a92efa9e8ebb7ac5a5_1440w](https://pic3.zhimg.com/80/v2-792e0559464e45a92efa9e8ebb7ac5a5_1440w.png)



#### 3. 常见类型

![img](https://pic4.zhimg.com/80/v2-fd171770eac4c565e8f6df3621c00647_1440w.png)







https://zhuanlan.zhihu.com/p/27834417

http://crash.163.com/index.do#news/!newsId=2

https://mp.weixin.qq.com/s/g-WzYF3wWAljok1XjPoo7w