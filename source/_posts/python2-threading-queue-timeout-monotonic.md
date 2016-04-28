title: Python 2.7 Threading / Queue 的 timeout 問題
date: 2016-04-29 00:21:59
tags:
- Python
---

最近遇到一個很坑的問題...

那就是系統時間只要 **倒退嚕** 某些 Python thread 就會卡住 (其實不是卡住)

我們都知道系統時間是有可能會改變的 (藉由 NTP, 基地台校時, 手動, 等方式)，但是很多程式的 timeout 相關機制設計是有缺陷的。舉個例子：一個 10 秒的 timeout 可以寫成 `起始系統時間 - 現在系統時間 >= 10` 就執行

淺而易見地，如果今天時間回溯如：2016 調整到 2000，那這樣這個 timeout 要等 **16 年** 才會觸發...

所以通常，有幾種做法：
1. 使用 monotonic time
> The kernel call for time functions. Using CLOCK_MONOTONIC is typically better than using CLOCK_REALTIME because the monotonic clock is always increasing, so you don't have to worry about someone's changing the clock.
> ref: http://www.qnx.com/developers/docs/6.4.1/neutrino/technotes/time.html

2. 取兩個時間相減後的絕對值 (缺點：並非真正的 timeout 時間，但可以抵抗系統時間往前。如果你沒辦法取得 monotonic time 也許也是一個方式)

在 Python 世界中，一直到 [PEP 418 -- Add monotonic time, performance counter, and process time functions](https://www.python.org/dev/peps/pep-0418/)，規範 stdlib monotonic time 的機制。(可能是考量到跨平台與可移植性...所以直到 2012 才加入這個功能)


Anyway, Python 2.7 中，`Queue.get()`, 以及 `threading.condition` 中都使用了 `time.time()` 來判斷 timeout，如此一來只要使用者不慎將時間往回調你的程式行為可能就不如預期。
(題外話：系統時間大幅度往前實際上是很少發生的，也應該要被避免。因為其他背景執行程式如何實作 timeout? 或是其他功能可能會有預期外的行為發生。)

可以看一下 Python 2.7 source code 其中的 `_time()` 是 `time.time()`，所以可以明顯地看出如果系統時間往回調，這邊的 `threading.condition.wait()` 會行為異常。

```py
endtime = _time() + timeout
delay = 0.0005 # 500 us -> initial delay of 1 ms
while True:
    gotit = waiter.acquire(0)
    if gotit:
        break
    remaining = endtime - _time()
    if remaining <= 0:
        break
    delay = min(delay * 2, remaining, .05)
```
> 以上取自 https://github.com/python/cpython/blob/2.7/Lib/threading.py#L349-L358

像這樣的例子在 Python code base 還有很多，詳細請見這個 patch 將所有 timeout 全部替換成使用 monotonic 來計算

- patches: http://bugs.python.org/review/22043
- issue: http://bugs.python.org/issue22043


最後補上一些前人的痛：
- [threading.Timer/timeouts break on change of win32 local time
](https://bugs.python.org/issue1508864)
- [[Python] Debug python 程式 hang 在 Queue.get() 的問題
](http://ephrain.pixnet.net/blog/post/62284744)


最後我想說：**Let's upgrade to Python 3.5!**
