# Android 安全

## 组件安全

1、**设置权限开放属性**：

```xml
android:exported=["true" | "false"]
```

该属性为四大组件共有属性，其中含义大同小异。默认值由其包含 `<intent-filter>` 与否决定。若未包含，则默认为“false”，若存在至少一个，则默认值为“true”。

- 在`Activity`中:
  表示是否允许外部应用组件启动。若为“false”，则 Activity 只能由同一应用或同一用户 ID 的不同应用启动。
- 在`Service`中：
  表示是否允许外部应用组件调用服务或与其进行交互。若为“false”，则 Activity 只能由同一应用或同一用户 ID 的不同应用启动。
- 在`Receiver`中：
  表示是否可以接收来自其应用程序之外的消息，如来自系统或或其他应用的广播。若为“ false”，则广播接收器只能接收具有相同用户ID的相同应用程序或应用程序的组件发送的消息。
- 在`Provider`中：
  表示是否允许其他应用程序访问内容提供器。若为“false”，则具有与Provider相同的用户ID（UID）的应用程序才能访问它。如果需要给其他应用程序提供内容，则应当限定读写权限。



2. **使用LocalBroadcastReceiver**

区别基于Binder实现的BroadcastReceiver，LocalBroadcastManager 是基于Handler实现的，拥有更高的效率与安全性。安全性主要体现在数据仅限于应用内部传输，避免广播被拦截、伪造、篡改的风险。简单了解下用法：

- 自定义BroadcastReceiver

```java
public class MyReceiver extends BroadcastReceiver {
    @Override public void onReceive(Context context, Intent intent) {
     //Do SomeThing Here
    }
}
```



3. **Application相关属性配置**

   - debugable属性

   `android:debuggable=["true" | "false"]`很多人说要在发布的时候手动设置该值为`false`，其实根据官方文档说明，默认值就是`false`。

   - allowBackup属性

   `android:allowBackup=["true" | "false"]`设置是否支持备份，默认值为`true`，应当慎重支持该属性，避免应用内数据通过备份造成的泄漏问题。



## WebView 安全

1. 请使用https的链接

第一是安全；第二是避免被恶心的运营商劫持，插入广告，影响用户体验。

2. 处理file协议安全漏洞

```java
//若不需支持，则直接禁止 file 协议
setAllowFileAccess(false);
setAllowFileAccessFromFileURLs(false);
setAllowUniversalAccessFromFileURLs(false);
```

3. 密码明文保存漏洞

由于webView默认开启密码保存功能，所以在用户输入密码时，会弹出提示框，询问用户是否保存。若选择保存，则密码会以明文形式保存到 `/data/data/com.package.name/databases/webview.db`中，这样就有被盗取密码的危险。所以我们应该禁止网页保存密码，设置`WebSettings.setSavePassword(false)`

4. 开启安全浏览模式

```xml
<manifest>
    <meta-data android:name="android.webkit.WebView.EnableSafeBrowsing" 
               android:value="true" /> 
    <application> 
     ... 
    </application> 
</manifest>
```

启用安全浏览模式后，WebView 将参考安全浏览的恶意软件和钓鱼网站数据库检查访问的 URL ，在用户打开之前给予危险提示，体验类似以于Chrome浏览器。

## 数据存储安全

1. 秘钥及敏感信息

此类配置应当妥善存放，不要在类中硬编码敏感信息，可以使用JNI将敏感信息写到Native层。

2. SharePreferences

首先不应当使用SharePreferences来存放敏感信息。存储一些配置信息时也要配置好访问权限，如私有的访问权限 MODE_PRIVATE，避免配置信息被篡改。

3. 签名配置signingConfigs

避免明文保存签名密码，可以将密码保存到本地，无需上传版本控制系统

在app目录下建立一个不加入版本控制系统的`gradle.properties`文件：

```groovy
STORE_PASSWORD = qwer1234
KEY_PASSWORD = demo1234
KEY_ALIAS = demokey
```

gradle将自动引入`gradle.properties`文件，可以直接在`buld.gradle`文件中使用：

```groovy
signingConfigs {
	release {
		try {
			storeFile file("E:\FDM\Key\demo.jks")
			storePassword STORE_PASSWORD 
			keyAlias KEY_ALIAS 
			keyPassword KEY_PASSWORD
		}catch (ex) {
			throw new InvalidUserDataException("You should define KEYSTORE_PASSWORD and KEY_PASSWORD in gradle.properties.")
		}
	}
}
```

## 数据传输安全

- 使用HTTPS协议

HTTPS的主要思想是在不安全的网络上创建一安全信道，并可在使用适当的加密包和*服务器证书可被验证且可被信任时*，对窃听和中间人攻击提供合理的防护。可以说是非常基础的安全防护级别了。

## 其他安全问题

1. 日志输出

日志是我们开发调试中不可或缺的一部分，但也是最容易泄露敏感信息的地方。所以，在我们发布应用时，应当关闭、甚至移除Log输出。

2. 混淆、加固

混淆代码，可以增加反编译破解的难度。但是我们在使用混淆功能时也要注意实体类、与JS交互的方法、第三方混淆配置等问题。

应用加固也是近年来比较热门的应用安全解决方案，各大厂商都有自己的加固方案，常见的如腾讯乐固、360加固等等。

3. 漏洞检测工具

当项目代码量庞大以后，积累了较多的历史代码，人工检测代码工作量大。这时，漏洞检查工具就派上用场了。中文的漏洞检测工具中比较有名的就是360的FireLine。