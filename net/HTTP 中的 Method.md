# HTTP中的方法

## Get 和 Post 的区别

> Get Method — 向指定的資源要求資料，類似於查詢操作。

- 資料傳遞方式 — 將參數以 Query String方式(name/value)，由URL帶至Server端，EX: /test/demo_get**?name1=value1&name2=value2**。
- 參數長度限制 — 長度限制根據瀏覽器、Server 的不同會有所不同。
- 安全性 — 較POST不安全，因為傳遞的參數會在URL上顯示。
- 資料種類 — 只允許 ASCII。
- **可以**重新載入或按上一頁並不會有任何問題。
- 傳遞的參數**會**被儲存在瀏覽器的歷史紀錄中。
- **可以**加入瀏覽器書籤。

```
GET /?id=xxxxx&password=xxxxx HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0 (Windows; U; Windows NT 5.1; zh-TW; rv:1.9.2.13)
Gecko/20101203 Firefox/3.6.13 GTB7.1 ( .NET CLR 3.5.30729)
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: zh-tw,en-us;q=0.7,en;q=0.3
Accept-Encoding: gzip,deflate
Accept-Charset: UTF-8,*
Keep-Alive: 115
Connection: keep-alive
```

http GET

------

> Post Method — 將要處理的資料提交給指定的資源，類似於更新操作。

- 資料傳遞方式 — 將參數放至 Request 的 message body 中，因此不會在URL看到參數，適合用於隱密性較高的資料，EX: Signup、Signin帳號、密碼等。
- 參數長度限制 — 長度不受限制。
- 安全性 — 較POST安全，實際上傳遞的參數皆可以在封包(Request- line 和 Message-body)上看到。
- 資料種類 — 無限制。
- 重新載入或按上一頁瀏覽器會出現將重新提交(re-submitted)資料的提示。
- 傳遞的參數**不會**被儲存在瀏覽器的歷史紀錄中。
- **無法**加入瀏覽器書籤。

```

POST / HTTP/1.1
Host: xxx.toright.com
User-Agent: Mozilla/5.0 (Windows; U; Windows NT 5.1; zh-TW; rv:1.9.2.13) 
Gecko/20101203 Firefox/3.6.13 GTB7.1 ( .NET CLR 3.5.30729)
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: zh-tw,en-us;q=0.7,en;q=0.3
Accept-Encoding: gzip,deflate
Accept-Charset: UTF-8,*
Keep-Alive: 115
Connection: keep-alive
 
Content-Type: application/x-www-form-urlencoded
</code><code>Content-Length: 9
id=xxxxx&password=xxxxx
```

