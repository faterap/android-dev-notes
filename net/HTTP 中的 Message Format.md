# HTTP 中的 Message Format

所有訊息都是由:

- 起始行 (start-line) 開始
- 0 或多個 表頭欄位 (header-field) + CRLF
  [合稱為 表頭 (headers) 或是 表頭部分 (header section)]
- 再加上一個 CRLF
- 最後是 **可選的 (optional)** 訊息主體 (message-body)



請求訊息 (request message) 範例 :
(顏色對應上方格式)

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

 

回應訊息 (response message) 範例 :
(顏色對應上方格式)

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
....略
```



[]: https://notfalse.net/39/http-message-format	"HTTP/1.1 — 訊息格式 (Message Format)"

