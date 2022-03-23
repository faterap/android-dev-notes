# Ability



## 1. 概念

### 1.1 Feature Ability

 **Ability** 是应用所具备能力的抽象，也是应用程序的重要组成部分。一个应用可以具备多种能力（即可以包含多个 Ability），HarmonyOS 支持应用以 Ability 为单位进行部署。Ability 可以分为 FA（Feature Ability）和 PA（Particle Ability）两种类型，每种类型为开发者提供了不同的模板，以便实现不同的业务功能。
  从上面一段文字，去其糟粕，取其精华之后就是两点。**FA（Feature Ability）**和**PA（Particle Ability）**

  **FA（Feature Ability）** ，中文意思是功能能力，它支持**Page Ability** 页面能力用于提供与用户交互的能力。一个Page 可以由一个或多个 AbilitySlice 构成，AbilitySlice 是指应用的单个页面及其控制逻辑的总和。



### 1.2 Page Ability

现在我们知道这个Page Ability是主要负责页面交互的，那么就可以理解为Android 的Activity。那么都知道Activity有生命周期，同样的Page Ability也是的。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200923100950751.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4NDM2MjE0,size_16,color_FFFFFF,t_70#pic_center)

### 1.3 Service Ability

先来看一下Service Ability的官方解释基于 Service 模板的 Ability（以下简称“Service”）主要用于后台运行任务（如执行音乐播放、文件下载等），但不提供用户交互界面。Service 可由其他应用或 Ability 启动，即使用户切换到其他应用，Service 仍将在后台继续运行。
  Service 是单实例的。在一个设备上，相同的 Service 只会存在一个实例。如果多个 Ability 共用这个实例，只有当与 Service 绑定的所有 Ability 都退出后，Service 才能够退出。由于Service 是在主线程里执行的，因此，如果在 Service 里面的操作时间过长，开发者必须在Service 里创建新的线程来处理，防止造成主线程阻塞，应用程序无响应。其实和Android的Service有点像。

### 1.4 Data Ability

使用 Data 模板的 Ability（以下简称“Data”）有助于应用管理其自身和其他应用存储数据的访问，并提供与其他应用共享数据的方法。Data 既可用于同设备不同应用的数据共享，也支持跨设备不同应用的数据共享。

  数据的存放形式多样，可以是数据库，也可以是磁盘上的文件。Data 对外提供对数据的增、删、改、查，以及打开文件等接口，这些接口的具体实现由开发者提供。说起来和Android的ContentProvider有些像。









https://aijishu.com/a/1060000000139663#item-1-3

