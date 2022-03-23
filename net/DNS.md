# DNS

### 1. HTTPS 大致流程

C: Client发送一个request（包括密码套件，SSL/TLS版本，压缩算法）
S: Server把支持的加密算法等以证书的形式返回（包括CA颁发机构和加密公钥，SSL使用的证书通常是X.509类型）
C: Client获取证书后，验证证书颁发机构，并通过与本地已有的数字证书验证Server证书的真实性。如果其中有验证失败的情况，发出警告并断开连接
C: 验证通过，产生一份消息，消息处理后将用作对称加密的密钥。将这份消息用Server的公钥进行加密。把这份加密的消息（ClientKeyExchange)发送给Server
S: Server用自己的私钥将ClientExchange中的消息解密出来作为对称加密的密钥，这时双方已经有了一套安全的加密方式了
之后双方传输数据可以使用该密钥进行加密然后对话了



### 2. HTTPDNS

 HTTPDNS 利用 HTTP 协议与 DNS 服务器交互，代替了传统的基于 UDP 协议的 DNS 交互，绕开了运营商的 Local DNS，有效防止了域名劫持，提高域名解析效率。另外，由于 DNS 服务器端获取的是真实客户端 IP 而非 Local DNS 的 IP，能够精确定位客户端地理位置、运营商、出口IP等信息，从而有效改进调度精确性。![截屏2022-02-15 下午7.47.58](/Users/faterap/Library/Application Support/typora-user-images/截屏2022-02-15 下午7.47.58.png)