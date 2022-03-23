# HttpDns



#### 1. Https的坑

##### 1.1 SSL/TLS握手失败

HTTP 协议相对比较容易，只需要处理 HOST，那么 HTTPS 呢？发起 HTTPS 请求首先需要进行 SSL/TLS 握手，其流程如下: 

1. 客户端发送 Client Hello，携带随机数、支持的加密算法等信息; 
2. 服务端收到请求后，选择合适的加密算法，连同公钥证书、随机数等信息返回给客户端;
3. 客户端检验服务端证书的合法性，计算产生随机数并用证书公钥加密发送给服务端; 
4. 服务端通过私钥获取随机数信息，基于之前的交互信息计算得到协商密钥并通知给客户端;
5. 客户端验证服务端发送的数据和密钥，通过后双方握手完成，开始进行加密通信。

在我们采用 IP 直连的形式后，上述 HTTPS 的第三步会发生问题, 客户端检验服务端下发的证书这动作包含两个步骤: 

1. 客户端用本地保存的根证书解开证书链，确认服务端的证书是由可信任的机构颁发的。
2. 客户端需要检查证书的 Domain 域和扩展域是否包含本次请求的 HOST。

证书的验证需要这两个步骤都检验通过才能够进行后续流程，否则 SSL/TLS 握手将在这里失败结束。

由于在 IP 直连下，我们给网络请求库的 URL 中 host 部分已经被替换成了 IP 地址，因此证书验证的第二步中，默认配置下 “本次请求的 HOST” 会是一个 IP 地址，这将导致 domain 检查不匹配，最终 SSL/TLS 握手失败。那么该如何解决这个问题？ 

解决 SSL/TLS 握手中域名校验问题的方法在于我们**重新配置 HostnameVerifier, 让请求库用实际的域名去做域名校验**，代码示例如下: 

```text
final URL htmlUrl = new URL("https://1.1.1.1/");
HttpsURLConnection connection = (HttpsURLConnection) htmlUrl.openConnection();
connection.setRequestProperty("Host","www.meipai.com");
connection.setHostnameVerifier(new HostnameVerifier() {
     @Override
     public boolean verify(String hostname, SSLSession session) {
         return HttpsURLConnection.getDefaultHostnameVerifier()
                   .verify("www.meipai.com",session);
     }
});
```

##### 1.2 SNI问题

SNI（Server Name Indication）是为了解决一个服务器使用多个域名和证书的 SSL/TLS 扩展。它的基本工作原理如下：

- 服务端配置有多个域名和对应的证书。客户端在与服务器建立 SSL 链接之时,先发送自己要访问站点的域名；
- 服务器根据这个域名返回一个合适的证书。

跟上面 Domain 校验的情况类似，这里的网络请求库默认发送给服务端的 "要访问站点的域名" 就是我们替换后的 IP 地址。服务端在收到这样一个 IP 地址形式的域名后将是一脸懵逼，找不到对应的证书，最后只好下发一个默认的域名证书回来。

接下来发生的是，客户端在检验证书的 Domain 域时，怎么也检查不通过，因为服务端下发的证书本来就不是对应该域名的。最后 SSL/TLS 握手失败告终。

上述这个 SNI 场景下的问题，我们是否有办法解决呢？ 

可以解决，需用客户端重新定制 SSLSocketFactory , 不过修改的代码相对较多，这里就不列举了。



#### 2. Java hook

在上述流程中我们可以知道，InetAddress 会有到 AddressCache 尝试获取已缓存记录的动作，而这里 AddessCache 是一个 static 的 map 结构变量。因此，在这里我们来对它做点小手脚 :

- 模仿系统的 AddressCache 构造一个我们自己的 AddressCahce 结构，不过它的 get 方法被替换为从我们 SDK 获取解析记录；
- 通过反射的形式用我们修改后的 AddressCache 替换掉系统的 AddressCache 变量。



#### 3. Native hook