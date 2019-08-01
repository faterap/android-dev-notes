# dHTTP 协议

## 版本差异

#### HTTP/1.0

1996年，HTTP/1.0 发布，内容大大增加。

**相比于 0.9 版：**

1. 可以发送任意格式的数据，不仅可以传输文字，还可以传输图像、视频、二进制文件等。
2. 除了 GET 请求方式，还加入了 POST、HEAD 请求方式。
3. 请求和响应的格式改变，除了数据部分，每次通信必须包含头信息。
4. 其他的新增功能还包括状态码（status code）、多字符集支持、多部分发送（multi-part type）、权限（authorization）、缓存（cache）、内容编码（content encoding）等。

**缺点：**

HTTP/1.0 的主要缺点是，每个 TCP 连接只能发送一个请求，发送数据完毕，连接就关闭了，如果还要请求其他资源，就必须重新建立连接。

TCP 建立连接成本很高，因为需要客户端和服务器三次握手，并且开始时发送速率较慢（慢启动）。所以，HTTP 1.0 版本的性能比较差。随着网页加载的外部资源越来越多，这个问题就愈发突出了。

为了解决这个问题，有些浏览器在请求时，用了一个非标准的 **Connection** 字段。

```
Connection: keep-alive
```

这个字段要求服务器不要关闭 TCP 连接，以便其他请求复用，服务器同样响应这个字段。这样一个可以复用的 TCP 连接就建立了，直到客户端或服务器主动关闭连接。

#### HTTP/1.1

1997 年，HTTP/1.1 版本发布，它进一步完善了 HTTP 协议，直到现在还是**最流行的版本**。

**相比于 1.0 版：**

1. HTTP1.1 **最大变化是默认支持长连接**。即 TCP 连接默认不关闭，可以被多个请求复用，不用声明 Connection: keep-alive。
2. 管道机制，即在同一个 TCP 连接中，客户端可以**同时发送多个请求**，进一步改进了 HTTP 协议的效率。

> 举例来说，客户端需要请求两个资源。以前的做法是，在同一个 TCP 连接里面，先发送 A 请求，然后等待服务器做出回应，收到后再发出 B 请求。管道机制则是允许浏览器同时发出 A 请求和 B 请求，但是服务器还是按照顺序，先回应 A 请求，完成后再回应 B 请求。

1. 增加了 PUT、PATCH、OPTIONS、DELETE 等请求方式。
2. 客户端请求的头信息增加了 **Host** 字段，用来指定服务器的域名。

```
Host:www.example.com
```

有了 Host 字段，就可以将请求发往同一台服务器上的不同网站，为虚拟主机的兴起打下了基础。

**缺点：**

虽然 1.1 版允许复用 TCP 连接，但是同一个 TCP 连接里面，所有的数据通信是按次序进行的。服务器只有处理完一个回应，才会进行下一个回应。要是前面的回应特别慢，后面就会有许多请求排队等着。这称为「队头堵塞」（Head-of-line blocking）。

#### HTTP/2.0

HTTP2.0 相对于 HTTP1.x 来说提升是巨大的，主要有以下几点：

**二进制格式**：HTTP1.x 是文本协议，而 HTTP2.0 是以二进制帧为基本单位，是一个二进制协议，一帧中除了包含数据外同时还包含该帧的标识：Stream Identifier，即标识了该帧属于哪个request,使得网络传输变得十分灵活。

**多路复用**：一个很大的改进，原先 HTTP1.x 一个连接一个请求的情况有比较大的局限性，也引发了很多问题，如建立多个连接的消耗以及效率问题。

HTTP1.x 为了解决效率问题，可能会尽量多的发起并发的请求去加载资源，然而浏览器对于同一域名下的并发请求有限制，而优化的手段一般是将请求的资源放到不同的域名下来突破这种限制。

而 HTTP2.0 支持的多路复用可以很好的解决这个问题，多个请求共用一个 TCP 连接，多个请求可以同时在这个 TCP 连接上并发，一个是解决了建立多个 TCP 连接的消耗问题，一个也解决了效率的问题。那么是什么原理支撑多个请求可以在一个 TCP 连接上并发呢？基本原理就是上面的二进制分帧，因为每一帧都有一个身份标识，所以多个请求的不同帧可以并发的无序发送出去，在服务端会根据每一帧的身份标识，将其整理到对应的 request 中。

**header头部压缩**：HTTP2.0 使用 HPACK 算法对 header 的数据进行压缩，减少请求的大小，减少流量消耗，提高效率。因为之前存在一个问题是，每次请求都要带上 header，而这个 header 中的数据通常是不变的。

**支持服务端推送**：HTTP/2.0 允许服务器未经请求，主动向客户端发送资源，这叫做服务器推送。意思是说，当我们对支持 HTTP2.0 的服务器请求数据的时候，服务器会顺便把一些客户端需要的资源一起推送到客户端，免得客户端再次创建连接发送请求到服务器端获取。这种方式非常合适加载静态资源。

服务器端推送的这些资源其实存在客户端的某处地方，客户端直接从本地加载这些资源就可以了，不用走网络，速度自然是快很多的。



## 模型

- 应用层（HTTP）
- 传输层（TCP）
- 网络层（IP）
- 链路层



## 报文格式

### 请求报文

```
POST /?id=1 HTTP/1.1

Host: echo.paw.cloud
Content-Type: application/json; charset=utf-8
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.12; rv:53.0) Gecko/20100101 Firefox/53.0
Connection: close
Content-Length: 136

{
  "status": "ok",
  "extended": true,
  "results": [
    {"value": 0, "type": "int64"},
    {"value": 1.0e+3, "type": "decimal"}
  ]
}
```

1. 起始行
2. 首部
3. 主体**（可选）**

`GET`, `HEAD`, `DELETE` `OPTIONS` 这些请求方法不需要请求内容。



**起始行**

```
HTTP-message   = start-line    <-- -- 在這!
                 *( header-field CRLF )
                 CRLF
                 [ message-body ]
```

HTTP请求是由客户端发出的消息，用来使服务器执行动作。*起始行 (start-line)* 包含三个元素：

1. 一个 *HTTP 方法*，一个动词 (像 [`GET`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/GET), [`PUT`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/PUT) 或者 [`POST`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/POST)) 或者一个名词 (像 [`HEAD`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/HEAD) 或者 [`OPTIONS`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/OPTIONS)), 描述要执行的动作.
2. *请求目标 (request target)，*通常是一个 [URL](https://developer.mozilla.org/en-US/docs/Glossary/URL)，或者是协议、端口和域名的绝对路径，通常以请求的环境为特征。请求的格式因不同的 HTTP 方法而异。
3. *HTTP 版本 (HTTP version*)*，*定义了剩余报文的结构，作为对期望的响应版本的指示符。



**首部**

有许多请求头可用，它们可以分为几组：

- 通用首部（*General headers*），例如 [`Via`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Via)，适用于整个报文。
- 请求首部（*Request headers*）例如 [`User-Agent`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/User-Agent)，[`Accept-Type`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Accept-Type)，通过进一步的定义(例如 [`Accept-Language`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Accept-Language))，或者给定上下文(例如 [`Referer`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Referer))，或者进行有条件的限制 (例如 [`If-None`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/If-None)) 来修改请求。
- 实体首部（*Entity headers*），例如 [`Content-Length`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Length)，适用于请求的 body。显然，如果请求中没有任何 body，则不会发送这样的头文件。



**内容**

请求的最后一部分是它的 body。不是所有的请求都有一个 body：例如获取资源的请求，GET，HEAD，DELETE 和 OPTIONS，通常它们不需要 body。 有些请求将数据发送到服务器以便更新数据：常见的的情况是 POST 请求（包含 HTML 表单数据）。

Body 大致可分为两类：

- Single-resource bodies，由一个单文件组成。该类型 body 由两个 header 定义： [`Content-Type`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Type) 和 [`Content-Length`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Length).
- [Multiple-resource bodies](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types#multipartform-data)，由多部分 body 组成，每一部分包含不同的信息位。通常是和  [HTML Forms](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/Forms) 连系在一起。



### 响应报文

```
HTTP/1.1 200 OK

Content-Type: text/html; charset=utf-8
Date: Sat, 18 Feb 2017 00:01:57 GMT
Server: nginx/1.11.8
transfer-encoding: chunked
Connection: Close

 <!doctype html>
<html lang="en">
<head>
<meta charset="utf-8">
<title>echo</title>
```

1. 状态行
2. 首部
3. 主体



**状态行**

HTTP 响应的起始行被称作 *状态行* *(status line)*，包含以下信息：

1. *协议版本*，通常为 `HTTP/1.1。`
2. *状态码 (**status code)*，表明请求是成功或失败。常见的状态码是 [`200`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/200)，[`404`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/404)，或 [`302`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/302)。
3. *状态文本 (status text)*。一个简短的，纯粹的信息，通过状态码的文本描述，帮助人们理解该 HTTP 消息。

一个典型的状态行看起来像这样：`HTTP/1.1 404 Not Found。`



**首部**

有许多响应头可用，这些响应头可以分为几组：

- 通用首部（*General headers*），例如 [`Via`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Via)，适用于整个报文。
- 响应首部（*Response headers*）例如 [`Vary`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Vary) 和 [`Accept-Ranges`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Accept-Ranges)，提供其它不符合状态行的关于服务器的信息。
- 实体首部（*Entity headers*），例如 [`Content-Length`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Length)，适用于请求的 body。显然，如果请求中没有任何 body，则不会发送这样的头文件。



**内容**

Body 大致可分为三类：

- Single-resource bodies，由**已知**长度的单个文件组成。该类型 body 由两个 header 定义：[`Content-Type`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Type) 和 [`Content-Length`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Length)。
- Single-resource bodies，由**未知**长度的单个文件组成，通过将 [`Transfer-Encoding`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Transfer-Encoding) 设置为 `chunked 来`使用 chunks 编码。
- [Multiple-resource bodies](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types#multipartform-data)，由多部分 body 组成，每部分包含不同的信息段。但这是比较少见的。



**例子**

```html
HTTP/1.1 200 OK
Date: Tue, 10 Jul 2012 06:50:15 GMT
Content-Length: 362
Content-Type: text/html
```



### 通用首部字段

- Cache-control
- Connection
- Date
- Pragma
- Trailer
- Transfer-Encoding
- Upgrade
- Via
- Warning



## HTTP 缓存

| 指令            | 说明                         |
| --------------- | ---------------------------- |
| no-cache        | 强制向源服务器再次验证       |
| no-store        | 不缓存请求或响应的任何内容   |
| max-age         | 响应的最大Age值              |
| max-stale       | 接收已过期的响应             |
| min-fresh       | 期望在指定时间内的响应仍有效 |
| no-transform    | 代理不可更改媒体类型         |
| only-if-cached  | 从缓存获取资源               |
| cache-extension | 新指令标记（token）          |



### Expires

```
Expires: Wed, 21 Oct 2017 07:28:00 GMT
```

瀏覽器收到這個 Response 之後就會把這個資源給快取起來，當下一次使用者再度造訪這個頁面或是要求這個圖片的資源的時候，瀏覽器會檢視「現在的時間」是否有超過這個 Expires。如果沒有超過的話，那瀏覽器「不會發送任何 Request」，而是直接從電腦裡面已經存好的 Cache 拿資料。

### Control 与 max-age

Expires 其實是 HTTP 1.0 就存在的 Header，而為了解決上面 Expires 會碰到的問題，HTTP 1.1 有一個新的 header 出現了，叫做：`Cache-Control`。（註：Cache-Control 是 HTTP 1.1 出現的 Header，但其實不單單只是解決這個問題，還解決許多 HTTP 1.0 沒辦法處理的快取相關問題）

`max-age`會蓋過`Expires`。因此現在的快取儘管兩個都會放，但其實真正會用到的是`max-age`。

**Last-Modified 與 If-Modified-Since**

想要做到上面的功能，必須要 Server 跟 Client 兩邊互相配合才行。其中一種做法就是使用 HTTP 1.0 就有的：`Last-Modified`與`If-Modified-Since`的搭配使用。

```
Last-Modified: 2017-01-01 13:00:00
Cache-Control: max-age=31536000
```

假設沒有更新的話，Server 就會回一個`Status code: 304 (Not Modified)`，代表你可以繼續沿用快取的這份檔案。

**Etag 與 If-None-Match**

而`Etag`這個 Header 就是這樣的一個東西。你可以把 Etag 想成是這份檔案內容的 hash 值（但其實不是，但原理類似就是了，總之就是一樣的內容會產生一樣的 hash，不一樣的會產生不一樣的 hash）。

在 Response 裡面 Server 會帶上這個檔案的 Etag，等快取過期之後，瀏覽器就可以拿這個 Etag 去問說檔案是不是有被更動過。

`Etag`跟`If-None-Match`也是搭配使用的一對，就像`Last-Modified`跟`If-Modified-Since`一樣。

![img](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/images/http-cache-control.png?hl=zh-tw)

**强 ETag 值和弱 Tag 值**
ETag 中有强 ETag 值和弱 ETag 值之分。

- 强 ETag 值，不论实体发生多么细微的变化都会改变其值。

```
ETag: "usagi-1234"
```

- 弱 ETag 值只用于提示资源是否相同。只有资源发生了根本改变，产生差异时才会改变 ETag 值。这时，会在字段值最开始处附加 W/。

```
ETag: W/"usagi-1234"
```



**Cache 策略**

除了可以指定`max-age`以外，可以直接使用：`Cache-Control: no-store`，代表說：「我就是不要任何快取」。

`Cache-Control: no-cache`。`no-cache`並不是「完全不使用快取的意思」。no-cache 代表不缓存过期的资源，缓存会向源服务器进行有效期确认后处理资源，也许称为 do-notserve-from-cache-without-revalidation 更合适。



## Cookie

Cookie实际上是一小段的文本信息。客户端请求服务器，如果服务器需要记录该用户状态，就使用response向客户端浏览器颁发一个Cookie。客户端浏览器会把Cookie保存起来。当浏览器再请求该网站时，**浏览器把请求的网址连同该Cookie一同提交给服务器**。服务器检查该Cookie，以此来辨认用户状态。服务器还可以根据需要修改Cookie的内容。

[]: https://www.cnblogs.com/lingyejun/p/9282169.html	"什么是Http无状态？Session、Cookie、Token三者之间的区别"



## Session

- Session是另一种记录客户状态的机制，不同的是Cookie保存在客户端浏览器中，而Session保存在服务器上。
- 客户端浏览器访问服务器的时候，服务器把客户端信息以某种形式记录在服务器上。这就是Session。客户端浏览器再次访问时只需要从该Session中查找该客户的状态就可以了。
- 如果说Cookie机制是通过检查客户身上的“通行证”来确定客户身份的话，那么Session机制就是通过检查服务器上的“客户明细表”来确认客户身份。
- Session相当于程序在服务器上建立的一份客户档案，客户来访的时候只需要查询客户档案表就可以了。

### Session 与 Cookie 之间的关系

- cookie 是一个**实际存在的、具体的东西**，http 协议中定义在 header 中的字段。
- session 是一个抽象概念、开发者为了实现中断和继续等操作，将client和server之间一对一的交互，抽象为“会话”，进而衍生出“会话状态”，也就是 session 的概念。
- 即session描述的是一种通讯会话机制，而cookie只是目前实现这种机制的主流方案里面的一个参与者，它一般是用于保存session ID。

参考资料：

[]: http://fred-zone.blogspot.com/2014/01/web-session.html	"Web 技術中的 Session 是什麼？"



## Token

![img](https://user-gold-cdn.xitu.io/2017/6/1/02d484cc1f675ec36e96b8899d756b31?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

Token 和 Session 有一点不相同，Session 内容需要保存在服务端，但是 **Token 不需要保存在服务端**。

服务端向客户端返回数据时，根据只有服务器自己才知道的密钥，会在数据里面加一个「签名」。由于其他人不知道密钥，所以 Token 无法被伪造。

而且这个`Token`不需要保存在服务端，当客户端重新将`Token`发送到服务器时，服务端会使用相同的加密算法和密钥，对数据重新计算一次签名，然后与`Token`进行比较。相同的话，证明数据没有被篡改。

使用基于 Token 的身份验证方法，在服务端不需要存储用户的登录记录。大概的流程是这样的：

1. 客户端使用用户名跟密码请求登录
2. 服务端收到请求，去验证用户名与密码
3. 验证成功后，服务端会签发一个 Token，再把这个 Token 发送给客户端
4. 客户端收到 Token 以后可以把它存储起来，比如放在 Cookie 里或者 Local Storage 里
5. 客户端每次向服务端请求资源的时候需要带着服务端签发的 Token
6. 服务端收到请求，然后去验证客户端请求里面带着的 Token，如果验证成功，就向客户端返回请求的数据



------

[]: https://developer.mozilla.org/zh-CN/docs/Web/HTTP	"HTTP"
[]: https://blog.techbridge.cc/2017/06/17/cache-introduction/	"循序漸進理解 HTTP Cache 機制"

