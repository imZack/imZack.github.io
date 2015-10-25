title: Python Queue.get(timeout) 消耗異常 CPU Usage
date: 2014-12-1 21:23:27
tags:
  - Python
  - Sanji
---

之前在開發 [**Sanji Framework**](https://github.com/Sanji-IO/sanji) 的時候發現，Python 當中提供的 [Queue — A synchronized queue class](https://docs.python.org/2/library/queue.html) 如果使用 `Queue.get(timeout=X)` 會吃很多 CPU。

這...很奇怪呀！理論上如果 Queue 是空的應該不會占用很多 CPU ...

<!-- more -->

上網查了一下找到這位**苦主**: [Concurrent Queue.get() with timeouts eats CPU](http://blog.codedstructure.net/2011/02/concurrent-queueget-with-timeouts-eats.html)，發布於 2011 年。

文中作者也發現，如果把 `Queue.get(timeout=1)` 與 `Queue.get(timeout=sys.maxint)` 差距並不大，表示其內部實作方式一定是不**我們所想的那樣**... 也許這跟 CPython 的 GIL 有某種程度的關係？還沒有時間看這段的 Source Code XDDD

## 問題
先看一下有問題的這個片段

```python
# 這個 function 會由 Thread(target=_dispatch_message) 並執行 thread.run()
def _dispatch_message(self, stop_event):
    while not stop_event.is_set():  # 傳入一個 event 目的是結束時
        try:
            message = self.req_queue.get(timeout=1)  # 如果 Queue 是空的就丟出例外 (timeout 1秒)
            self.__dispatch_message(message)
        except Empty:
            pass
    logger.debug("_dispatch_message thread is terminated")
```

上面這段基本上就是我會建立很多個 Worker 要從 **req_queue** 拿出 message 並執行 **__dispatch_message**。傳入 event 目的是當我要離開程式時，只需把每個 worker 的 `stop_event.set()` 就可以讓他跳出迴圈。

## 解法

```python
while True:
    message = self.req_queue.get()
    if message is None:
        logger.debug("_dispatch_message thread is terminated")
        return
    self.__dispatch_message(message)
```

簡單說就是在 `req_queeu` 放入一個 `None` 值讓他知道該停工了！假如你有 N 個 Thread 在消化 Queue 那就要放入適當數量的 `None`，從此 CPU Loading 就恢復了和平...

## 附註

前面連結的文章中有提到可用 `strace -fc -p $PID` 觀察 Process 可以得到以下結果：

### 修改前
    % time     seconds  usecs/call     calls    errors syscall
    ------ ----------- ----------- --------- --------- ----------------
     85.62 7019.825941      157522     44564       448 futex
     14.30 1172.598598      169328      6925           select
      0.08    6.359466          44    144106           clock_gettime
      0.00    0.007906         128        62           sendto
      0.00    0.004049          44        93           recvfrom
      0.00    0.001183          51        23           mprotect
    ------ ----------- ----------- --------- --------- ----------------
    100.00 8198.797143                195773       448 total

可以看出來閒置時間大部分消耗於 **futex** 這個 System Call，正在再等待網路封包的 **select** 反而只佔了 **14.3%**

### 修改後
    % time     seconds  usecs/call     calls    errors syscall
    ------ ----------- ----------- --------- --------- ----------------
     96.54  305.482354      335326       911           select
      3.22   10.186393        2120      4805        35 futex
      0.24    0.752236          24     31197           clock_gettime
    ------ ----------- ----------- --------- --------- ----------------
    100.00  316.420983                 36913        35 total

可以看出大部分 System Call 用在 `select` 推測是 MQTT 在等待封包使用

第一次知道有 `strace` 這個用法，[這邊可以找到一些應用](http://fcamel-life.blogspot.tw/2010/02/strace-ltrace-system-call-library-call.html)。有空我再來研究看看！


