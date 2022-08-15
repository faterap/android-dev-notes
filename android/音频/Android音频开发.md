# Android音频开发

### 1. 采样率和码率

##### 采样率

实际中，人发出的声音信号为模拟信号，**想要在实际中处理必须为数字信号**，即采用采样、量化、编码的处理方案。处理的第一步为采样，即模数转换。简单地说就是通过波形采样的方法记录1秒钟长度的声音，需要多少个数据。根据奈魁斯特（NYQUIST）采样定理，用两倍于一个正弦波的频繁率进行采样就能完全真实地还原该波形。所以，对于声音信号而言，要想对离散信号进行还原，必须将抽样频率定为40KHz以上。实际中，一般定为44.1KHz。44.1KHz采样率的声音就是要花费44100个数据来描述1秒钟的声音波形。[原则](https://link.jianshu.com/?t=http://www.shenmeshi.com/Social/Social_20070321133640.html)上采样率越高，声音的[质量](https://link.jianshu.com/?t=http://www.shenmeshi.com/Education/Education_20070113191031.html)越好，采样频率一般共分为22.05KHz、44.1KHz、48KHz三个等级。22.05KHz只能达到FM广播的声音品质，44.1KHz则是理论上的CD音质界限，48KHz则已达到[DVD](https://link.jianshu.com/?t=http://www.shenmeshi.com/Life/Life_20070301232232.html)音质了。

##### 码率（比特率）

码率 = 采样率(44.16) * 位深度（16）* 通道数(2)=1411.2kbps

音频码率，又称为比特率：是指一个音频流中每秒钟能通过的数据量。如128kbps，其中ps（per second）为每秒，kb为千位，那么128kbps表示一秒钟能传输的数据量是128千位。对于格式相同的文件来说，码率越大的话，音质越好。但是对于不同格式的音频文件来说，相同码率并不代表其音质一样。



[]: https://www.jianshu.com/p/09610fe52fba	"音频采样率和码率简介"

### 音频格式区别

https://jogo2066.pixnet.net/blog/post/287364281

https://cloud.tencent.com/developer/article/1482998

https://juejin.im/post/5a587aaa6fb9a01c9a26afc1#heading-2

### 声道数/采样位数

![img](https://user-gold-cdn.xitu.io/2018/1/12/160e99f202e34758?imageslim)

### 音频重采样算法

https://blog.csdn.net/JohanMan/article/details/97109917

### MediaMuxer

https://www.jianshu.com/p/aeadf260258a

### MediaFormat

### MediaExtrator



https://zhuanlan.zhihu.com/p/143822815