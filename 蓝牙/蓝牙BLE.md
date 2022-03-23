# 蓝牙BLE

## 1. 介绍

### UUID

UUID是根据一定算法，计算得到的一长串数字，这个数字的产生使用了多种元素，所以使得这串数字不会重复，每次生成都会产生不一样的序列，所以可以用来作为唯一标识。

### 基础知识

为了让支持蓝牙的设备能够在彼此之间传输数据，它们必须先通过*配对*过程形成通信通道。其中一台设备（*可检测到的设备*）需将自身设置为可接收传入的连接请求。另一台设备会使用*服务发现*过程找到此可检测到的设备。在可检测到的设备接受配对请求后，这两台设备会完成*绑定*过程，并在此期间交换安全密钥。二者会缓存这些密钥，以供日后使用。完成配对和绑定过程后，两台设备会交换信息。当会话完成时，发起配对请求的设备会发布已将其链接到可检测设备的通道。但是，这两台设备仍保持绑定状态，因此在未来的会话期间，只要二者在彼此的范围内且均未移除绑定，便可自动重新连接。

### 连接设备

如要在两台设备之间创建连接，您必须同时实现服务器端和客户端机制，因为其中一台设备必须开放服务器套接字，而另一台设备必须使用服务器设备的 MAC 地址发起连接。服务器设备和客户端设备均会以不同方法获得所需的 `BluetoothSocket`。接受传入连接后，服务器会收到套接字信息。在打开与服务器相连的 RFCOMM 通道时，客户端会提供套接字信息。

当服务器和客户端在同一 RFCOMM 通道上分别拥有已连接的 `BluetoothSocket` 时，即可将二者视为彼此连接。这种情况下，每台设备都能获得输入和输出流式传输，并开始传输数据，相关详细介绍请参阅[管理连接](https://developer.android.com/guide/topics/connectivity/bluetooth?hl=zh-cn#ManageAConnection)部分。本部分介绍如何在两台设备之间发起连接。

## 2. 关键术语和概念

以下是对 BLE 关键术语和概念的总结：

通用属性配置文件 (GATT)

-  GATT 配置文件是一种通用规范，内容针对在 BLE 链路上发送和接收称为“属性”的简短数据片段。目前所有低功耗应用配置文件均以 GATT 为基础。
- 蓝牙特别兴趣小组 (Bluetooth SIG) 为低功耗设备定义诸多[配置文件](https://www.bluetooth.org/en-us/specification/adopted-specifications)。配置文件是描述设备如何在特定应用中工作的规范。请注意，一台设备可以实现多个配置文件。例如，一台设备可能包含心率监测仪和电池电量检测器。
- **属性协议 (ATT)** — 属性协议 (ATT) 是 GATT 的构建基础，二者的关系也被称为 GATT/ATT。ATT 经过优化，可在 BLE 设备上运行。为此，该协议尽可能少地使用字节。每个属性均由通用唯一标识符 (UUID) 进行唯一标识，后者是用于对信息进行唯一标识的字符串 ID 的 128 位标准化格式。由 ATT 传输的*属性*采用*特征*和*服务*格式。 
- **特征** — 特征包含一个值和 0 至多个描述特征值的描述符。您可将特征理解为类型，后者与类类似。 
- **描述符** — 描述符是描述特征值的已定义属性。例如，描述符可指定人类可读的描述、特征值的可接受范围或特定于特征值的度量单位。
- **Service** — 服务是一系列特征。例如，您可能拥有名为“心率监测器”的服务，其中包括“心率测量”等特征。您可以在 [bluetooth.org](https://www.bluetooth.org/en-us/specification/adopted-specifications) 上找到基于 GATT 的现有配置文件和服务的列表。

### 角色和职责

以下是 Android 设备与 BLE 设备交互时应用的角色和职责：

- 中央与外围。这适用于 BLE 连接本身。担任中央角色的设备进行扫描、寻找广播；外围设备发出广播。
- GATT 服务器与 GATT 客户端。这确定两个设备建立连接后如何相互通信。

要了解两者的区别，请想象您有一部 Android 手机和一个 Activity 追踪器，该 Activity 追踪器是一个 BLE 设备。手机支持中央角色；Activity 追踪器支持外围角色（要建立 BLE 连接必须具备这两个两个角色—如果两个设备都仅支持中央或外围角色，则无法相互通信）。

手机与 Activity 追踪器建立连接后，它们便开始相互传送 GATT 数据。根据它们传送数据的种类，其中一个会充当 GATT 服务器。例如，如果 Activity 追踪器要将传感器数据汇报给手机，那么 Activity 追踪器便充当服务器。如果 Activity 追踪器要从手机接收更新，那么手机便充当服务器。

在本文档中使用的示例中，Android 应用（在 Android 设备上运行）是 GATT 客户端。该应用从 GATT 服务器获取数据，后者是一个支持 [Heart Rate Profile](http://developer.bluetooth.org/TechnologyOverview/Pages/HRP.aspx) 的心率监测仪。但您也可以设计您的 Android 应用，使它充当 GATT 服务器角色。如需了解详情，请参阅 `BluetoothGattServer`。

## 3. BLE开发

### 0x1 介绍

Android 4.3（API 级别 18）为发挥*核心作用*的蓝牙低功耗 (BLE) 引入内置平台支持，并提供相应 API，方便应用发现设备、查询服务和传输信息。

常见用例包括：

- 在临近设备间传输少量数据。
- 与 [Google Beacons](https://developers.google.com/beacons/?hl=zh-cn) 等近程传感器交互，以便为用户提供基于其当前位置的自定义体验。

与[传统蓝牙](https://developer.android.com/guide/topics/connectivity/bluetooth?hl=zh-cn)不同，蓝牙低功耗 (BLE) 旨在提供显著降低的功耗。这使 Android 应用可与功率要求更严格的 BLE 设备（例如近程传感器、心率监测仪和健身设备）通信。

### 0x2 BLE协议栈

BLE蓝牙协议栈框架结构如下所示：

![img](https://user-images.githubusercontent.com/11291711/49983884-3727b700-ffa0-11e8-9818-259b2804ebca.png)

各个结构简述：

- PHY： 使蓝牙可以使用2.4GHz频道，并且能自适应的跳频。
- LL层： 控制设备处于 准备、广播、监听、初始化、连接等五个状态。
- HCI层： 向上为主机提供软件应用程序接口，对外为外部硬件提供控制接口。
- L2CAP层： 对传输数据实行封装。
- SM层： 提供主机和客机的配对和密钥分发，实现安全连接和数据交换。
- ATT层： 对数据主机或客机传入的指令进行指令搜索处理，
- GATT层： 接收和处理主机或客机的指令信息，并将指令打包成合适的profile。
- GAP层： 向上提供API，向下合理分配各个层工作。

如图所示，ATT和GATT是蓝牙协议栈重要的2层，也是蓝牙应用开发者打交道最多的两层，用户开发应用程序或者说service/profile的时候，调用的都是GATT API，而GATT又调用了ATT API。

#### Link player

Link Layer用于控制设备的射频状态，设备将处于Standby（待机）、Advertising（通告）、Scanning（扫描）、Initiating（初始化）、Connection（连接）这五种状态中的一种。

![img](https://user-images.githubusercontent.com/11291711/49983886-37c04d80-ffa0-11e8-82bb-374b8ef76a31.png)

- 待机状态（Standby State）：此时即不发送数据，也不接收数据，对设备来所也是最节能的状态；
- 通告状态（Advertising State）：通告状态下的设备一般也被称为“通告者”（Advertiser），它会通过advertising channel（广播通道）周期性发送数据，广播的数据可以由处于Scanning state或Initiating state的实体接收；
- 扫描状态（Scanning State）：可以通过advertising channel（广播通道）接收数据的状态，该状态下的设备又被称为“扫描者”（Scanner）。此外，根据advertiser所广播的数据类型，有些Scanner还可以主动向Advertiser请求一些额外数据；
- 初始化状态（Initiating State）：和Scanning State类似，不过是一种特殊的状态。Scanner会侦听所有的advertising channel，而Initator（初始化者）则只侦听某个特定设备的的广播，并在接收到数据后，发送连接请求，以便和Advertiser建立连接；
- 连接状态（Connection State）：由Initiating State或Advertising State自动切换而来，处于Connection State的双方，分别有两种角色。其中，Initiater方被称为Mater（主设备），Advertiser方则称作Slave（从设备）。



### 0x3 GAP(Generic Access Profile)

前面Link Layer虽然对连接建立的过程做了定义，但它并没有体现到Application（或者Profile）层面，而GAP则是直接与应用程序或配置文件通信的接口，它实现了如下功能：

- 定义GAP层的蓝牙设备角色（role）
  - Broadcaster（广播者）：设备正在发送advertising events
  - Observer（观察者）：设备正在接收advertising events
  - Peripheral（外设）：设备接受Link Layer连接（对应Link Layer的slave角色）
  - Central（主机）：设备发起Link Layer连接（对应Link Layer的master角色）
- 定义GAP层的用于实现各种通信的操作模式（Operational Mode）和过程（Procedures）
  - Broadcast mode and observation procedure，实现单向的、无连接的通信方式
  - Discovery modes and procedures，实现蓝牙设备的发现操作
  - Connection modes and procedures，实现蓝牙设备的连接操作
  - Bonding modes and procedures，实现蓝牙设备的配对操作
- 定义User Interface有关的蓝牙参数，包括蓝牙地址、蓝牙名称、蓝牙的PIN码等
- Security相关的定义。

它用来控制设备连接和广播。GAP 使你的设备被其他设备可见，并决定了你的设备是否可以或者怎样与设备进行交互。例如 Beacon 设备就只是向外发送广播，不支持连接；小米手环就可以与中心设备建立连接。

在 GAP 中蓝牙设备可以向外广播数据包，广播包分为两部分： Advertising Data Payload（广播数据）和 Scan Response Data Payload（扫描回复），每种数据最长可以包含 31 byte。这里广播数据是必需的，因为外设必需不停的向外广播，让中心设备知道它的存在。扫描回复是可选的，中心设备可以向外设请求扫描回复，这里包含一些设备额外的信息，例如设备的名字。在 Android 中，系统会把这两个数据拼接在一起，返回一个 62 字节的数组。这些广播数据可以自己手动去解析，在 Android 5.0 也提供 ScanRecord 帮你解析，直接可以通过这个类获得有意义的数据。广播中可以有哪些数据类型呢？设备连接属性，标识设备支持的 BLE 模式，这个是必须的。设备名字，设备包含的关键 GATT service，或者 Service data，厂商自定义数据等等。

广播流程：

![163823-5c059e90beb65c0e](https://upload-images.jianshu.io/upload_images/163823-5c059e90beb65c0e.png)

### 0x4 Client/Server

先釐清在 GATT 連線中，Client / Server 的關係：

- 藍芽週邊 (Peripheral) 是 GATT Server：掌握 ATT 表，其中包含 services 跟 characteristics 資訊 (稍後會提到)
- Mobile 裝置 (Central) 是 GATT Client：發送 request 到 GATT Server，等待 Peripheral 回傳 Response

看下圖比較清楚。另外 Peripheral 在沒有配對的情況，會一直廣播(Advertising) 自己的裝置資訊，一旦連線建立後，就會停止 Advertising，因此 Peripheral 只能與一個 Central 建立連線，但 Central 可以與多個 Peripheral 建立連線。



中心模式和外设模式是什么意思？

- Central Mode： Android端作为中心设备，连接其他外围设备。
- Peripheral Mode：Android端作为外围设备，被其他中心设备连接。在Android 5.0支持外设模式之后，才算实现了两台Android手机通过BLE进行相互通信。

### 0x5 ATT

它是 BLE 通信的基础。ATT 把数据封装，向外暴露为“属性”，提供“属性”的为服务端，获取“属性”的为客户端。ATT 是专门为低功耗蓝牙设计的，结构非常简单，数据长度很短。

在BLE协议栈中，Physical Layer负责提供一系列的Physical Channel；基于这些Physical Channel，Link Layer可在两个设备之间建立用于点对点通信的Logical Channel；而L2CAP则将这个Logical Channel换分为一个个的L2CAP Channel，以便提供应用程序级别的通道复用。到此之后，基本协议栈已经构建完毕，应用程序已经可以基于L2CAP欢快的run起来了。

谈起应用程序，就不得不说BLE的初衷——物联网。物联网中传输的数据和传统的互联网有什么区别呢？抛开其它不谈，物联网中最重要、最广泛的一类应用是：信息的采集。

这些信息往往都很简单，如温度、湿度、速度、位置信息、电量、水压、等等。
采集的过程也很简单，节点设备定时的向中心设备汇报信息数据，或者，中心设备在需要的时候主动查询。

基于信息采集的需求，BLE抽象出一个协议：Attribute protocol，该协议将这些“信息”以“Attribute（属性）”的形式抽象出来，并提供一些方法，供远端设备（remote device）读取、修改这些属性的值（Attribute value）。

Attribute Protocol的主要思路包括：

1. 基于L2CAP，使用固定的Channel ID

2. 采用client-server的形式。提供信息（以后都将其称为Attribute）的一方称作ATT server（一般是那些传感器节点），访问信息的一方称作ATT client。

3. 一个Attribute由Attribute Type、Attribute Handle和Attribute Value组成。

   1. Attribute Type用以标示Attribute的类型，类似于我们常说的“温度”、“湿度”等人类可识别的术语，通过UUID进行区分。

   2. Attribute Handle是一个16-bit的数值，用作唯一识别Attribute server上的所有Attribute。Attribute Handle的存在有如下意义：

       

      1. 一个server上可能存在多个相同type的Attribute，显然，client有区分这些Attribute的需要。 
      2. 同一类型的多个Attribute，可以组成一个Group，client可以通过这个Group中的起、始handle访问所有的Attributes。

   3. Attribute Value代表Attribute的值，可以是任何固定长度或者可变长度的octet array。

4. Attribute能够定义一些权限（Permissions），以便server控制client的访问行为，包括：

   1. 访问有关的权限（access permissions），Readable、Writeable以及Readable and writable；
   2. 加密有关的权限（encryption permissions），Encryption required和No encryption required；
   3. 认证有关的权限（authentication permissions），Authentication Required和No Authentication Required；
   4. 授权有关的权限（authorization permissions），Authorization Required和No Authorization Required。

5. 根据所定义的Attribute PDU的不同，client可以对server有多种访问方式，包括：

   1. Find Information，获取Attribute type和Attribute Handle的对应关系；
   2. Reading Attributes，有Read by type、Read by handle、Read by blob（只读取部分信息）、Read Multiple（读取多个handle的value）等方式；
   3. Writing Attributes，包括需要应答的writing、不需要应答的writing等。



### 0x6 GATT(Generic Attribute Profile) 

ATT之所以称作“protocol”，是因为它还比较抽象，仅仅定义了一套机制，允许client和server通过Attribute的形式共享信息。至于具体共享哪些信息，ATT并不关心，这是GATT（Generic Attribute Profile）的主场。GATT相对ATT只多了一个‘G‘，然含义却大不同，因为GATT是一个profile（更准确的说是profile framework）。

GATT按照层级定义了4个概念：配置文件（Profile）、服务（Service）、特征（Characteristic）和描述（Descriptor）。他们的关系是这样的：Profile 就是定义了一个实际的应用场景，一个 Profile包含若干个 Service，一个 Service 包含若干个 Characteristic，一个 Characteristic 可以包含若干 Descriptor。

由三層資料結構組成：Profile, Service & Characteristic。

#### Profile

Profile 并不是实际存在于 BLE 外设上的，它只是一个被 Bluetooth SIG 或者外设设计者预先定义的 Service 的集合。例如[心率Profile（Heart Rate Profile）](https://link.jianshu.com?t=https%3A%2F%2Fwww.bluetooth.org%2Fdocman%2Fhandlers%2Fdownloaddoc.ashx%3Fdoc_id%3D239865%26_ga%3D2.40136875.845393115.1507611229-1319099422.1507279282%EF%BC%8C%E5%BB%BA%E8%AE%AE%E6%9B%B4%E6%96%B0)就是结合了 Heart Rate Service 和 Device Information Service。所有官方通过 GATT Profile 的列表可以从[这里](https://link.jianshu.com?t=http%3A%2F%2Fdeveloper.bluetooth.org%2FTechnologyOverview%2FPages%2FProfiles.aspx%23GATT)找到。

#### Service

Service 是把数据分成一个个的独立逻辑项，它包含一个或者多个 Characteristic。每个 Service 有一个 UUID 唯一标识。 UUID 有 16 bit 的，或者 128 bit 的。16 bit 的 UUID 是官方通过认证的，需要花钱购买，128 bit 是自定义的，这个就可以自己随便设置。官方通过了一些标准 Service，完整列表在[这里](https://link.jianshu.com?t=https%3A%2F%2Fdeveloper.bluetooth.org%2Fgatt%2Fservices%2FPages%2FServicesHome.aspx)。以 [Heart Rate Service](https://link.jianshu.com?t=https%3A%2F%2Fdeveloper.bluetooth.org%2Fgatt%2Fservices%2FPages%2FServiceViewer.aspx%3Fu%3Dorg.bluetooth.service.heart_rate.xml)为例，可以看到它的官方通过 16 bit UUID 是 `0x180D`，包含 3 个 Characteristic：*Heart Rate Measurement*, *Body Sensor Location* 和 *Heart Rate Control Point*，并且定义了只有第一个是必须的，它是可选实现的。



![1*PDxBYlW8o38_XxNrY-blHg](https://miro.medium.com/max/624/1*PDxBYlW8o38_XxNrY-blHg.png)

#### Characteristic

它定义了数值和操作，包含一个Characteristic声明、Characteristic属性、值、值的描述(Optional)。通常我们讲的 BLE 通信，其实就是对 Characteristic 的读写或者订阅通知。比如在实际操作过程中，我对某一个Characteristic进行读，就是获取这个Characteristic的value。

#### UUID

 Service、Characteristic 和 Descriptor 都是使用 UUID 唯一标示的。

UUID 是全局唯一标识，它是 128bit 的值，为了便于识别和阅读，一般以 “8位-4位-4位-4位-12位”的16进制标示，比如“12345678-abcd-1000-8000-123456000000”。

但是，128bit的UUID 太长，考虑到在低功耗蓝牙中，数据长度非常受限的情况，蓝牙又使用了所谓的 16 bit 或者 32 bit 的 UUID，形式如下：“0000XXXX-0000-1000-8000-00805F9B34FB”。除了 “XXXX” 那几位以外，其他都是固定，所以说，其实 16 bit UUID 是对应了一个 128 bit 的 UUID。这样一来，UUID 就大幅减少了，例如 16 bit UUID只有有限的 65536（16的四次方） 个。与此同时，因为数量有限，所以 16 bit UUID 并不能随3便使用。蓝牙技术联盟已经预先定义了一些 UUID，我们可以直接使用，比如“00001011-0000-1000-8000-00805F9B34FB”就一个是常见于BLE设备中的UUID。当然也可以花钱定制自定义的UUID。

#### 关系

蓝牙模块理论上说可以有许多Profile，不过大多数蓝牙模块就一个；Profile也可以包含很多Service（一般就一个），Service可以包含很多Characteristic（一般不止一个），有的蓝牙包含一个读Characteristic，一个写Characteristic，有的蓝牙读写共用一个Characteristic，Characteristic内部包含一个字节数组Value[]，对于读写Characteristic来说Value[]就是用于收发的数据。Android Ble sdk调用比较麻烦，回调太多了，主要原因就在这里，蓝牙协议层级实在太多了。

![ble_profile](https://dxdingdu-image.oss-cn-beijing.aliyuncs.com/ble_profile.png)



### 0x7 BLE协议关键类

- [BluetoothAdapter](https://developer.android.google.cn/reference/android/bluetooth/BluetoothAdapter)：代表本地蓝牙适配器，是所有蓝牙交互的入口，对蓝牙的操作都需要用到它，很重要，使用一个已知的MAC地址来实例化一个BluetoothDevice
- [BluetoothDevice](https://developer.android.google.cn/reference/android/bluetooth/BluetoothDevice)：代表一个远程蓝牙设备，使用这个来请求一个与远程设备连接，或者查询关于设备名称、地址、类和连接状态等设备信息
- [BluetoothGatt](https://developer.android.google.cn/reference/android/bluetooth/BluetoothGatt)：作为中央来使用和处理数据，重新连接蓝牙设备，发现蓝牙设备的 Service 等等。使用时有一个回调方法BluetoothGattCallback返回中央的状态和周边提供的数据
- [BluetoothCattService](https://developer.android.google.cn/reference/android/bluetooth/BluetoothGattService)：作为周边来提供数据，通过这个''类的 getCharacteristic(UUID uuid) 进一步获取 Characteristic 实现蓝牙数据的双向传输
- [BluetoothCattCharacteristic](https://developer.android.google.cn/reference/android/bluetooth/BluetoothGattCharacteristic)：蓝牙设备的特征，通过这个类定义需要往外围设备写入的数据和读取外围设备发送过来的数据

### 0x8 广播报文和数据报文

#### 1. 介绍

BLE的物理通道即“频道，分别是‘f=2402+k*2 MHz, k=0, … ,39’，带宽为2MHz”的40个RF Channel。

其中，有3个信道是`advertising channel`（广播通道），分别是37、38、39，用于发现设备（Scanning devices）、初始化连接（initiating a connection）和广播数据（broadcasting date）；

剩下的37个信道为`data channel`（数据通道），用于两个连接的设备间的通讯。

#### 2. 格式

在低功耗蓝牙规范中，分广播报文和数据报文这两种。设备利用广播报文发现、连接其它设备，而在连接建立之后，便开始使用数据报文。无论是广播报文还是数据报文，链路层只使用一种数据包格式，它由“前导码”（preamble）、“访问码”（access code）、”有效载荷“和”循环冗余校验“（Cyclical Redundancy Check，CRC）校验码组成。其中，”访问码“有时又称为”访问地址“（access address）

两个通道的报文格式均为一致，但是PDU会有所不同：

![packet-format-top-level.png](http://microchipdeveloper.com/local--files/wireless:ble-link-layer-packet-types/packet-format-top-level.png)

#### 3. 广播报文

##### 介绍

广播包有两种：广播包（Advertising Data）和响应包（Scan Response），其中广播包是每个设备必须广播的，而响应包是可选的。广播包由四部分进行组成:

![Link Layer Single Packet format and breakout of Advertising PDU](https://www.bluetooth.com/wp-content/uploads/2019/03/advertising_single_layer_packet_format.ashx.jpg)

![Advertising PDU header, specifically PDU type and Length.  ](https://www.bluetooth.com/wp-content/uploads/2019/03/advertising_pdu_header_type_length.ashx.jpg)

其中PDU Type主要分为四种类型：

###### 可连接的非定向广播(ADV_IND)

这是一种用途最广，最常见的广播类型，包括广播数据和扫描响应数据，它表示当前设备可以接受任何设备的连接请求。进行通用广播的设备能够被扫描设备扫描到，或者在接收到连接请求时作为从设备进入一个连接。通用广播可以在没有连接的情况下发出，换句话说，没有主从设备之分。

举例：智能手表请求和所有的中央设备进行连接

###### 可连接的定向广播(ADV_DIRECT_IND)

定向广播类型是为了尽可能快的连接，俗称回连包，这种报文包含两个地址：广播者的地址和发起者的地址。发起者收到发给自己的定向广播报文之后，可以立即发送连接请求作为回应。
定向广播类型有特殊的时序要求。完整的广播时间必须每3.75ms重复一次。这一要求使得扫描设备只需扫描3.75ms便可以收到定向广播设备的消息。
当然，如此快的发送会让报文充斥着广播信道，进而导致该区域内的其他设备无法进行广播。因此，定向广播不可以持续1.28s以上的时间。如果主机没有主动要求停止，或者连接没有建立，控制器都会自动停止广播。一旦到了1.28s，主机便只能使用间隔长得多的可连接非定向广播让其他设备来连接。
当使用定向广播时，设备不能被主动扫描。此外，定向广播报文的净荷中也不能带有其他附加数据。该净荷只能包含两个必须的地址。

举例：智能手表请求与特定的中央设备进行连接

###### 不可连接的非定向广播(ADV_NONCONN_IND)

仅仅发送广播数据，而不想被扫描或者连接。这也是唯一可用于只有发射机而没有接收机设备的广播类型。不可连接设备不会进入连接态，因此，它只能根据主机的要求在广播态和就绪态之间切换。常用于Beacon项目。

举例：博物馆里的beacon基站可以实现室内导航，指示展厅具体位置

###### 可扫描的非定向广播(ADV_SCAN_IND)

又称可发现广播，这种广播不能用于发起连接，但允许其他设备扫描该广播设备。这意味着该设备可以被发现，既可以发送广播数据，也可以响应扫描发送扫描回应数据，但不能建立连接。这是一种适用于广播数据的广播形式，动态数据可以包含与广播数据之中，而静态数据可以包含于扫描响应数据之中。

###### 比较

| Advertising PDU | Description                                           | Max Adv Data Len | Max Scan Res Len | Allow Connect |
| :-------------- | :---------------------------------------------------- | :--------------- | :--------------- | :------------ |
| ADV_IND         | Used to send connectable undirected advertisement     | 31 bytes         | 31 bytes         | Yes           |
| ADV_DIRECT_IND  | Used to send connectable directed advertisement       | N/A              | N/A              | Yes           |
| ADV_SCAN_IND    | Used to send scannable undirected advertisement       | 31 bytes         | 31 bytes         | No            |
| ADV_NONCONN_IND | Used to send non-connectable undirected advertisement | 31 bytes         | N/A              | No            |

##### 数据格式

每个包都是 31 字节，数据包中分为有效数据（significant）和无效数据（non-significant）两部分。

![ble-adv-data-fromat-1024x537](http://www.xmamiga.com/wp-content/uploads/2018/10/ble-adv-data-fromat-1024x537.jpg)



- 有效数据部分：包含若干个广播数据单元，称为 AD Structure。如图中所示，AD Structure 的组成是：第一个字节是长度值 Len，表示接下来的 Len 个字节是数据部分。数据部分的第一个字节表示数据的类型 AD Type，剩下的 Len – 1 个字节是真正的数据 AD data。其中 AD type 非常关键，决定了 AD Data 的数据代表的是什么和怎么解析
- 无效数据部分：因为广播包的长度必须是 31 个 byte，如果有效数据部分不到 31 自己，剩下的就用 0 补全。这部分的数据是无效的，解释的时候，忽略即可

广播包中包含若干个广播数据单元，广播数据单元也称为 `AD Structure`，广播数据单元 = 长度值Length + AD type + AD Data。

举个例子：

![ble-adv-data-fromat-1024x537](https://user-gold-cdn.xitu.io/2020/3/24/1710b76eb9413076?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



 在广播数据单元的**数据部分**中，**第一个字节**代表**数据类型**（AD type），决定数据部分表示的是什么数据。（即广播数据单元第二个字节为AD type）
![ble-adv-data-fromat-1024x537](https://user-gold-cdn.xitu.io/2020/3/24/1710b76ebafa1d1a?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

##### 常见ADType

![截屏2021-04-01 下午8.00.23](/Users/faterap/Library/Application Support/typora-user-images/截屏2021-04-01 下午8.00.23.png)



#### 4. 数据报文



![img](https://user-images.githubusercontent.com/11291711/49983880-368f2080-ffa0-11e8-8c30-84a5f5034380.png)



### 0x09 工作流程

#### 9.1 广播

#### 9.2 扫描

#### 9.3 连接

#### 9.4 通信

通俗的说，我们将从机具有的数据或者属性特征，称之为Profile，Profile可翻译为：配置文件。

从机中添加Profile配置文件（定义和存储Profile），作为GATT的Server端，主机作为GATT的Client端。

Profile包含一个或者多个Service，每个Service又包含一个或者多个Characteristic。主机可以发现和获取从机的Service和Characteristic，然后与之通信。Characteristic是主从通信的最小单元。

- 主机可主动向从机Write写入或Read读取数据。
- 从机可主动向主机Notify通知数据。

![BLE技术-GATT服务端和客户端.png](http://doc.iotxx.com/images/9/98/BLE%E6%8A%80%E6%9C%AF-GATT%E6%9C%8D%E5%8A%A1%E7%AB%AF%E5%92%8C%E5%AE%A2%E6%88%B7%E7%AB%AF.png)



注意，这里引用了`服务 Service` 和 `特征值 Characteristic` 的概念。每个服务和特征值都有自己的唯一标识 `UUID`，标准UUID为128位，蓝牙协议栈中一般采用16位，也就是两个字节的UUID格式。

一个从机设备包括一个或者多个服务；一个服务中又可以包括一条或者多条特征值，每个特征值都有自己的`属性 Property`，属性的取值有：`可读 Read`，`可写 Write` 以及 `通知 Notify`。

- 可读可写的字面意思容易理解，表示该特征值可以被主机读取和写入数据，
- 而通知则表示从机可以主动向主机发送通知数据。这便是主从机之间两个典型的通信方式。

下图是一个典型的从机设备，该从机包含有一个Profile，两个Service和五个Characteristic。我们先来介绍这些特征值的作用，然后介绍如何通过特征值通信。

![BLE技术-Profile与Service.png](http://doc.iotxx.com/images/5/5b/BLE%E6%8A%80%E6%9C%AF-Profile%E4%B8%8EService.png)

![Table of Properties](https://devzone.nordicsemi.com/cfs-file/__key/communityserver-blogs-components-weblogfiles/00-00-00-00-12-DZ-17/Properties.png)

#### 9.5 断开








------



https://www.jianshu.com/p/795bb0a08beb Android BLE开发详解和FastBle源码解析 /

https://www.xuding.info/2019/02/15/post607/ Android蓝牙开发入坑指南

http://www.xmamiga.com/1800/ Android蓝牙通用数据传输之二（BLE）

https://juejin.cn/post/6844904030645256205#heading-13

http://www.gandalf.site/2018/11/ble_23.html BLE（二）信道&数据包&协议栈格式

[https://www.bluetooth.com/specifications/assigned-numbers/generic-access-profile/ ] Assigned numbers and GAP

http://doc.iotxx.com/BLE技术揭秘#.E6.89.AB.E6.8F.8F BLE技术揭秘

