title: 'Python SimpleHTTPServer 啟動 IPv6'
date: 2017-11-30 22:38:00
tags:
- Python
- IPv6
---

`python -m SimpleHTTPServer` 一直是我滿常用的指令，可以快速啟動一個無腦的 HTTP Server，讓我可以做下載檔案之類的簡單測試。

不過，有一次在測試 IPv6 環境赫然發現...這個 **SimpleHTTPServer** 真的是有點過分簡單了，居然連 IPv6 都不支援呀 😂

上網看到了[這篇](https://groups.google.com/forum/#!topic/shlug/J9TteC003Fk)

照樣畫葫蘆修改檔案 `/usr/lib/python2.7/SimpleHTTPServer.py`

改動部份如下：
```python
import socket
def test(HandlerClass = SimpleHTTPRequestHandler,
         ServerClass = BaseHTTPServer.HTTPServer):
    ServerClass.address_family = socket.AF_INET6
    ServerClass(('::',8000),HandlerClass).serve_forever()
```

打完收工
