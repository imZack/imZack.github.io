title: 'Security of Things: 駭客年會 NANO Wargame 心得'
date: 2015-09-06 13:54:51
tags:
  - IoT
  - Hitcon
  - arduino
  - python
---

<img src="https://igcdn-photos-c-a.akamaihd.net/hphotos-ak-xaf1/t51.2885-15/e35/11939364_1456057631370146_563041118_n.jpg" width="350">

以往一直沒機會參加的[駭客年會 (Hitcon)](http://hitcon.org/)，今年終於參與了！
本篇主要講一下這次有趣的 Wargame - Arduino Nano...

<!-- more -->

一到會場，報到後領取的資料袋裡面可以看到他的身影。

一條簡單的 mini usb to usb 與 Arduino Nano；從沒玩過 Arduino 的我第一次就獻給 Hitcon 了 (?)
![](http://hitcon.org/2015/CMT/images/ArduinoNano.jpg)

上了 IRC 得知題目位於 iot.hitcon.org (現已下線) 後，邊聽神人 geohot 演說一邊去看題目。網頁上有三關可以下載，其實三都在同一份檔案內：一份 [**ans.py**](#ans-py)

剛好摸 Py 也好一陣子了，心理鬆了一口氣...不會 Py 的朋友可能光是上手就比較辛苦一點。(不過看到解答之後，**應該是不會 Py 也可以的啦！** 不要再說他是 Py wargame 囉！哈！)

## 題目

1. 回答一個摩斯密碼
2. 走迷宮 (9秒內)
3. 四則運算 (1秒內)

### 正規解法

以下有 Wargame 劇透，想要自己體驗的話...不要再往下看嚕...

> 注意：這是我心目中的 **"正規"** 解法

來源：[Hitcon Knowledge Base 的下方回應](http://kb.hitcon.org/post/128246307977/%E7%A7%92%E8%A7%A3-hitcon-nano-%E9%A1%8C)
 
一開始我是硬解了 1, 3 題，然後看到 geohot 忽然全破，嘖！想一想不對...這是 Hitcon 啊！又不是程式設計作業...於是我就想說要怎麼把 Arduino 上面的 Firmware Dump 下來。也是有找到解答中提到的 **avrdude**，只是下錯指令...

所以無疾而終。後來就想專心聽講，就沒有再深究下去了

當初我下錯的指令：
```
avrdude -p atmega328p -c stk500 -U flash:r:"/tmp/arduino.hex":r -P /dev/tty.wchusbserialfa130 -b115200
```
可以發現我連 chipset 都選錯了，噗！

正解指令：
```
avrdude -F -c arduino -b 57600 -P /dev/ttyUSB0 -pm328 -vv -U flash:r:program.bin:r
```

Dump 下來的 program.bin 為一個 binary 的檔案，要在裡面找解答後出現的金鑰 KEY，這邊可以借助 **strings** 這個指令...WTF 從來沒用過這個指令，趕快 man 一下：

> find the printable strings in a object, or other binary, file

怎麼有這麼神奇的小工具以前從來沒用過，一定是不夠黑！於是乎，就解出題的 Key 啦！


### 不正規解法
所謂不正規解法就是...乖乖寫 code 看他要你解什麼，怎麼有點像是 ACM (誤)

這邊我就不多提，看一下 HKB 吧！
[Hitcon Knowledge Base 的下方回應](http://kb.hitcon.org/post/128246307977/%E7%A7%92%E8%A7%A3-hitcon-nano-%E9%A1%8C)

### 相關程式碼與紀錄
<script src="https://gist.github.com/imZack/81f21869937a79870575.js"></script>

## 心得

![](https://fbcdn-sphotos-a-a.akamaihd.net/hphotos-ak-xpt1/v/t1.0-9/11891131_10205725057988021_7301585605406246660_n.jpg?oh=281e8f5609309e93c855bb2fae387f7d&oe=566C299F&__gda__=1450879894_7f1afb27a6971d302aa86a8039eb1e0f)

一開始硬解的很爽直接衝上第四名(應該很多人卡在 Driver, Python 等不熟悉的關係)，後來我一看 geohot 跳上第一名我就恍然大悟知道我的解法有問題，一時半刻又摸不出怎麼 dump firmware 的方式，就留這張 screenshot 作為紀念囉 HAHA

在 IoT 時代，對於這種 firmware 可以輕易被 dump 出來，利用 strings 直接取得所有字串資料，身為開發者的我們應該要更注意這方面的防護與瞭解。

