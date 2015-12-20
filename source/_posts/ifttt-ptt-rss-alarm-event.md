title: 自造：PTT 新文章、關鍵字 手機通知服務
date: 2015-08-31 00:00:55
tags:
  - rss
  - PTT
  - IFTTT
  - Projects
---

故事是這樣的...

常常在 PTT 上看到一些**特價訊息**或是**好康活動**文章，由於不是整天沒事幹守在板上所以每次看到訊息的時候可能都已經超過**活動時間**...Q_Q

於是誕生了...

<!-- more -->

## 2015/12/20 更新
大約一個月前 PTT Web 的時間欄位有更新，所以 Server 記得要更新喔！不然會收不到通知。另一方面也順手新增：文章標題關鍵字過濾功能(支援多個)，這樣就更方便啦！

使用方式：

```sh
// Single keyword
curl http://localhost:8000/Gossiping.xml?title=肥宅

// Multiple keywords
curl http://localhost:8000/Gossiping.xml?title=肥宅&title=國民黨
```

所以只要把上面網址貼到原先的 **New feed item** 就可以啦！


## 架設 PTT RSS Service

### 概述
為什麼要自己架 RSS Service 咧...因為 PTT 的 RSS 很不靠譜啊...三不五時就掛掉，或是有些版就不知道為什麼沒有 RSS 服務...於是這邊就用簡單的 nodejs 兜一個出來...

[Source code (MIT License) on GitHub](https://github.com/imZack/PttRSS) 喜歡記得幫我點一顆愛心

大致上用了下面這些 node modules...
- **cheerio** Parse 網頁版 PTT
- **lru-cache** LRU 快取實作
- **request** 簡單易用的 Server-side 發送 HTTP request
- **rss** 產生符合規格的 RSS
- **express** 不多說 Web server

有興趣的朋友可以看一下 source code 應該滿簡單的

> 18 禁板的入口年齡確認已經預設 bypass [source code](https://github.com/imZack/PttRSS/blob/master/ptt.js#L7-L9)

### 安裝與啟動

```
git clone https://github.com/imZack/PttRSS
npm install
PORT=8000 npm start
```

以上，打開瀏覽器瀏覽 `http://server:8000/{板名英文}.xml`

如：`http://server:8000/drama-ticket.xml`

> 板名英文請參考 PTT 網頁版
> - 電影版 https://www.ptt.cc/bbs/movie/index.html
> - 戲劇票務 https://www.ptt.cc/bbs/drama-ticket/index.html
> - 依此類推


```
<?xml version="1.0" encoding="UTF-8"?>
<rss xmlns:atom="http://www.w3.org/2005/Atom" xmlns:content="http://purl.org/rss/1.0/modules/content/" xmlns:dc="http://purl.org/dc/elements/1.1/" version="2.0">
  <channel>
    <title><![CDATA[drama-ticket]]></title>
    <description><![CDATA[PTT: drama-ticket]]></description>
    <link>https://www.ptt.cc/bbs/drama-ticket/index.html</link>
    <generator>PttRSS</generator>
    <lastBuildDate>Wed, 01 Jul 2015 16:12:40 GMT</lastBuildDate>
    <pubDate>Wed, 01 Jul 2015 16:12:40 GMT</pubDate>
    <item>
      <title><![CDATA[[售票] 江蕙8/8 台北小巨蛋演唱會 黃3D$2800一張]]></title>
      <link>https://www.ptt.cc/bbs/Drama-Ticket/M.1435766811.A.8A7.html</link>
      <guid isPermaLink="true">https://www.ptt.cc/bbs/Drama-Ticket/M.1435766811.A.8A7.html</guid>
      <dc:creator><![CDATA[pierrebebe]]></dc:creator>
      <pubDate>Wed, 01 Jul 2015 16:00:00 GMT</pubDate>
    </item>
    <item>
      <title><![CDATA[Re: [換票] 江蕙演唱會7/28紅1C連號換其他天]]></title>
      <link>https://www.ptt.cc/bbs/Drama-Ticket/M.1435767028.A.72C.html</link>
      <guid isPermaLink="true">https://www.ptt.cc/bbs/Drama-Ticket/M.1435767028.A.72C.html</guid>
      <dc:creator><![CDATA[yinfan]]></dc:creator>
      <pubDate>Wed, 01 Jul 2015 16:00:00 GMT</pubDate>
    </item>
    <item>
      <title><![CDATA[[公告] 版規-發文前請務必看完]]></title>
      <link>https://www.ptt.cc/bbs/Drama-Ticket/M.1351960651.A.0B0.html</link>
      <guid isPermaLink="true">https://www.ptt.cc/bbs/Drama-Ticket/M.1351960651.A.0B0.html</guid>
      <dc:creator><![CDATA[nickapple]]></dc:creator>
      <pubDate>Sat, 03 Jan 2015 16:00:00 GMT</pubDate>
    </item>
  </channel>
</rss>
```

OK。以上到這邊你就已經擁有一個私人的 PTT RSS Service 囉 ！

## 設定 IFTTT 服務 (手機 Push notification)
首先，先認識一下 [IFTTT](https://ifttt.com) 這個有趣的服務。已經有很多文章介紹過，我這邊就不贅述大家可以自行參考：

1. [IFTTT Android iOS 入門教學：如果明天下雨請今日提醒我](http://www.playpcesor.com/2014/04/ifttt-android-ios.html)
2. [請勿自己工作，指揮 IFTTT 就好！](http://appleuser.com/2014/11/16/ifttt/)
3. [IFTTT 教學：超好用雲端整合應用](https://loveninayeh.wordpress.com/2014/01/23/ifttt-%E6%95%99%E5%AD%B8%EF%BC%9A%E8%B6%85%E5%A5%BD%E7%94%A8%E9%9B%B2%E7%AB%AF%E6%95%B4%E5%90%88%E6%87%89%E7%94%A8/)

所以你會發現，其實我們可以擁有不只是**手機的通知**...也可以是各式各樣的串連，自由發揮～

右上角你的帳號點下去，有一個選項 [Create](https://ifttt.com/myrecipes/personal/new)。按下去可以看到 `if this then that`，其中 **this** 按下後我們搜尋 `feed` 這個關鍵字可以看到一個熟悉的 RSS 圖示，其中點下去又有兩個選項：

![](http://i.imgur.com/Zt8sN3p.png)

- **New feed item** 有新的[文章] 啟動
- **New feed item matches** 有 [新的文章] 且 [符合關鍵字] 啟動

要注意的是，這邊關鍵字沒有邏輯功能。也就是說你不能打 `Google OR 谷哥`，目前的解法是做在 RSS Provider 不過這點我們之後再來討論...目前可以先新增多個條件來解決。

這邊再把剛剛架設好的 RSS Service Url 填入 (這邊我希望接收所有 **drama-ticket** 版的新文章)

![](http://i.imgur.com/Bg0dLzX.png)

都設定完之後，還有 LOG 可以看～是不是很幫便阿阿阿阿！再也不會錯過任何 PTT 文章囉～

![](http://i.imgur.com/qvlT5dP.png)

手機 APP 裝好後，就能收到訊息如下圖：

![](http://i.imgur.com/msdCw5r.png)

打完收工
