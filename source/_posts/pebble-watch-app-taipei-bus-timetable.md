title: Pebble Watch App - Taipei Bus 台北公車時刻
date: 2014-08-18 21:31:58
tags:
	- pebble
	- app
---

繼上一篇[台北公車 API](https://github.com/imZack/taipei-bus)，擁有了公車時刻資訊後我就可以專心開發 [Pebble Watch](http://www.zack.tw/2013/02/pebble-watch.html) 的程式了！一開始感覺開發手錶 APP 好像挺難的，看到有個 Opensource 專案 [pebble-my-data](https://github.com/bahbka/pebble-my-data) 主要是可以讀取 server 上的 **.json 資料** 然後顯示在手錶上！

<!-- more -->

![](https://raw.githubusercontent.com/bahbka/pebble-my-data/master/stuff/screenshots/Screenshot_2014-07-06-18-31-03_small.png)

稍微喵了一下程式碼，覺得好像可以嘗試看看！Pebble 有 [cloud ide](https://cloudpebble.net) 可以說是十分方便啊！如果你不想架設環境什麼的，用 cloud ide 還可以幫你線上編譯，連接手錶 APP (打開 Developer Mode) 就可以直接從網路下載你剛寫好的程式跑在手錶上，Log 還會自動上傳到 cloud ide！可以說是對想專注開發應用不想管開發環境的使用者非常友善啊！

後來又發現了 [Simply.js](http://simplyjs.io/) 以及 fork 延伸出官方的 [pebblejs](https://github.com/pebble/pebblejs)，發現原來可以不用寫 `C` code 直接用 `JavaScript` 去撰寫 APP(不過還在 beta，文件等各方面相對貧乏)

最後決定直接使用 pebblejs 來完成這個 APP！展示圖片如下：

![](https://fbcdn-sphotos-g-a.akamaihd.net/hphotos-ak-xfa1/v/t1.0-9/s600x600/10553357_10203113439859200_3819816129095604694_n.jpg?oh=aa200fcfe676edc0b7b2e00ac5cee7ba&oe=547B60A5&__gda__=1416015355_ab17df0134c2cecb146138c4cace9bb2)
![](https://fbcdn-sphotos-h-a.akamaihd.net/hphotos-ak-xfp1/t1.0-9/10418432_10203113439739197_3680749583646222031_n.jpg)
![](https://scontent-a-sjc.xx.fbcdn.net/hphotos-xaf1/t1.0-9/10599240_10203113439819199_6871646928902782799_n.jpg)


Anyway，這個 APP 目前還不完善，應該就我自己先將就著用囉！

原始碼：[Github ❤](https://github.com/imZack/pebble-taipei-bus)

