title: 2014 Container Summit 技術高峰會
date: 2014-12-12 20:07:54
tags:
  - Docker
  - Container
  - LXC
  - iThome
---

早在幾個月前就陸續有小玩 Docker，也在公司內架了...

- Gitlab
- PostgreSQL
- Elasticsearch
- Logstash
- Kibana

主要是剛好想玩玩上面幾個服務又不想要弄髒開發環境也不想要開新的 VM ...Docker 之旅就此展開...

<!-- more -->

最近在開發 [Sanji Framework](https://github.com/Sanji-IO/sanji) 時為了讓同事以及我自己能夠快速地擁有開發環境，於是便動手開始整理一些環境

- [docker-mosquitto](https://github.com/Sanji-IO/docker-mosquitto)
    + 這是 MQTT Broker
- [sanji-mqtt-inspector](https://github.com/Sanji-IO/sanji-mqtt-inspector)
    + 這個是 [HiveMQ 開源](https://github.com/hivemq/hivemq-mqtt-web-client)的一個方便觀察 MQTT 內容的網站
- [sanji-bundle-dev-stack](https://github.com/Sanji-IO/sanji-bundle-dev-stack)
    + 最後則是透過今天會上也有提到的 **fig** 以及我的一些客製化 **scripts** 把所有東西串起來集之大成。

如此一來未來同事或是第三方就可以輕鬆取得開發環境的 Image 就不用花一堆時間在那邊 `apt-get install xxx ooo` 面對一堆奇奇怪怪的問題！

Anyway, 同事看到 [iThome 要舉辦第一次台灣的 Container Summit](http://containersummit.ithome.com.tw/) 當然二話不說先報名下去了...

<hr>

**節目表**

09:30~09:40 Welcome Opening - iThome 總編輯 吳其勳
09:40~10:00 Container 趨勢與市場發展分析- iThome 新聞主編 王宏仁
10:00~10:40 上手 Docker 的第一步 - Docker Taipei Meetup Co-Founder 郭韋廷 (Willy Kuo)
10:40~11:00 中場休息/攤位體驗
11:00~11:40 Docker 的現況與未來 - Docker Taipei Meetup 共同發起人 Carl Su
11:40~12:20 Docker on CoreOS 介紹與使用 - Docker Taipei Meetup 共同發起人 鄭淳尹 (Philipz)
**12:20~13:20 午休用餐**
13:20~14:00 1行指令的威力，從軟體開發角度談 Docker 的應用 - 時間軸科技 (Hiiir) 數位行銷策略處主任工程師 謝宗穎 (Spooky)
14:00~14:40 Docker 持續整合開發實戰 - KKBOX 資深工程師 Ash Wu
**14:40~15:00 中場休息**
15:00~15:40 如何在 OpenStack 內使用 Docker - 迎廣科技研發工程師 高國棟
15:40~16:20 從 Twelve-Factors App 角度，看如何導入 Docker 技術 - Gogolook 系統架構師 葉秉哲 (William Yeh)



## 先講缺點
這次地點選在 **張榮發基金會國際會議中心 11F 會議室 1101** 真的很不行啊～跟椅子連在一起的**空間總是不夠用的小桌子**以及**沒插頭**，網路部分的話我想就不用多說了。


## 再講優點
有喝不完的可樂(完全擄獲我)、雪碧、咖啡...吃的方面早上有甜甜圈；下午則是胡椒餅；中餐馬馬虎虎的餐盒。當然還有就是發現原來有這麼多人關心 Docker 啊，實在是很意外！



## 一點心得
前半段基本上就是討論 Docker 要多威有多威，獲得一堆 $$ 連開發一個管理工具如 **fig** 都能被好多錢收購；還有一些基本指令操作介紹，不過這邊由於我之前就都用過了只注意聽有沒有之前漏掉的部分：沒有。

**軟體開發角度**以及**持續整合開發**這部分利用 Docker 來實踐我比較有感覺一點，因為前面我已經有幹過類似的事情(只針對開發環境)。持續整合的部分應該下個月或是下下個月也差不多要上了。

另一方面，[Twelve-Factors App](http://12factor.net/) 我還滿感興趣的，之前雖然有**喵過**但總感覺了解的都只是皮毛...今天會上 William Yeh 又提了一次，看來該找個時間補完他了！

I. Codebase
II. Dependencies
III. Config
IV. Backing Services
V. Build, release, run
VI. Processes
VII. Port binding
VIII. Concurrency
IX. Disposability
X. Dev/prod parity
XI. Logs
XII. Admin processes

當中有個講題是跟 OpenStack 有關，但是好像 Docker 是包第三方開發的程式然後？這部分想等投影片出來再仔細看看。

至於台下觀眾幫老闆提問：現在已經有 VM 的開發環境快速建置 Solution 如果導入 Docker 會有什麼好處？基本上大家應該都贊成如果*現在爽爽用就不用改了*，**但是**即使是不需要改動，我認為還是可以從一些工作上面的小東西開始做改變(也順便透過這些練習學習新技術)，才不會一不小心別人已經上外太空了你還在鑽木取火。

**至於什麼時機只能用 VM 呢？**由於 Docker 是共用 Kernel 所以如果有這方面的考量(如 Driver)，則必須使用 VM 來做...。


以上是小小腦袋瓜的一點記憶...

