# iOS 静态库 vs 动态库

### 1. 静态库

![img](https://pic4.zhimg.com/80/v2-3ca36683bbc41417c6c01636ee40c0a7_1440w.jpg)

静态库的本质是一系列目标文件（`.o`）的集合，就是一个压缩包，其常见的表现形式为 `.a` 或者 `.framework` 后缀的文件

优势：

1. 不需要重复编译，在构建阶段直接参与链接就可以生成最终的可执行文件
2. 相较于动态库，没有动态链接过程，启动速度更快

劣势：

1. 无法直接使用，需要搭配头文件
2. 静态库改变会导致所有下游依赖方重新编译
3. 静态库冗余问题

### 2. 动态库(Dynamic Libraries`, 也称作 `Shared **Library**)

![img](https://pic2.zhimg.com/80/v2-c53c608ce9ec517770c650d5d5cffd61_1440w.jpg)

和静态库类似，都是一系列目标文件的集合，区别在于动态库不会在构建阶段直接参与链接，而是在APP 运行时动态链接。其常见的表现形式为 `.tbd`、`.dylib` 或者 `.framework` 后缀的文件

在标准系统中，动态库具备以下优势：

1. 应用具备动态化能力，无需重新构建，就可以获得新的能力，支持插件化
2. 多应用共享，降低内存占用，减小包大小

劣势：

1. APP 不具备自完备性，依赖运行环境才能运行
2. 应用启动阶段包含动态链接过程，启动速度慢于静态库

在 iOS8 系统之前，开发者只能使用静态库，也就是说 APP 所有的逻辑最终都会被打包到一个可执行文件中。iOS8 系统发布后，苹果引入了 [App Extesion](https://developer.apple.com/app-extensions/) 特性，允许开发者在 APP 中受限制的使用“动态库”，以达到在宿主和扩展中共享代码和资源的目的。

在 iOS 系统中，受沙盒机制的限制，APP 只能访问自己沙盒或者同证书 APP Group 共享区域的内容，因此我们并不能使用真正意义上的动态库，苹果把这种机制下的动态库称为 [Embedded Framework](https://developer.apple.com/library/archive/technotes/tn2435/_index.html)。

相较于系统动态库，放置在系统目录下，所有应用程序共享，运行时只需加载一次即可，Embedded Framework 存在于应用的包中，应用间不可共享，系统在启动宿主应用或者扩展时，按需动态加载。

### 3. framework

根据苹果官方的解释，Framework 本质上只是一个目录结构，包含了动态库，nib、图片、头文件、文档等资源。其中，Headers 存放公开的头文件，Info.plist 存放框架的基本信息，比如名字，版本号等，Modules 存放了该 Framework 作为 [Clang Module](https://clang.llvm.org/docs/Modules.html) 使用的必要信息，比如这里的 modulemap 文件。

无论是动态库还是静态库，Framework 所定义的目录结构是一样的。注意静态库的签名文件和动态库不一致，这也会为什么静态库不能 embed 到 APP 包中的原因。

### 4. 库的使用

1. 系统库是真正意义上的动态库，因此不需要嵌入到 APP 的包中，选择 Do Not Embed。
2. 开发者创建的动态库库属于  Embedded Framework，需要嵌入到 APP 包中，并且使用 APP 签名所用的证书签名，才能在 APP 启动时，通过验签逻辑。
3. 开发者创建的静态库如果以 Framework 形式呈现，这里不能选择 Embed，否则虽然可以打包成功，但是在上传 APP Store 或者用户安装（企业途径）时都会因静态库签名不被支持导致失败。