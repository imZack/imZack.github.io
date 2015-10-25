title: '為什麼瀏覽器 user-agent string 總是包含 Mozilla/5.0 ?'
tags:
  - Chrome
  - Firefox
  - Gecko
  - khtml
  - Mozilla
  - MSIE
  - Safari
id: 121
categories:
  - 有趣的問題
date: 2013-06-02 00:19:05
---

最近從 [eit.tw](http://eit.tw) 短網址服務後台觀察到一個很神奇的現象，其實也不是最近才看到是今天終於記得要上網問 Google 大神這個問題了 XD
{% codeblock %}Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.94 Safari/537.36

Mozilla/5.0 (Linux; U; Android 4.1.2; zh-tw; GT-I9300 Build/JZO54K) AppleWebKit/534.30 (KHTML, like Gecko) Version/4.0 Mobile Safari/534.30

Mozilla/5.0 (Windows NT 6.1; WOW64; rv:20.0) Gecko/20100101 Firefox/20.0

Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.0; Trident/5.0){% endcodeblock %}
<!--more-->

看得出哪裡奇怪嗎？怎麼每個瀏覽器的 user-agent 都包含 Mozilla/5.0 阿～這到底什麼巫術？

於是找到了 StackOverflow 的這篇 [What does “ Mozilla/5.0” in user agent string signify?](http://stackoverflow.com/questions/12288452/what-does-mozilla-5-0-in-user-agent-string-signify)

裡面回答又讓我輾轉到了這篇 [History of the browser user-agent string](http://webaim.org/blog/user-agent-string-history/)

## 快速結論

因為網站開發者可能會因為你是某瀏覽器(這裡是 Mozilla)，所以輸出一些特殊功能的程式碼(這裡指好的特殊功能)，所以當其他瀏覽器也支援這種好功能時，就試圖去模仿 Mozilla 瀏覽器讓網站輸出跟 Mozilla 一樣的內容，而不是輸出被閹割功能的程式碼。

## 漫長的模仿過程

一開始 Netscape (Mozilla) 支援 Frame，微軟大哥的 IE 後來也支援了，當然希望使用者能夠像使用 Netscape 一樣能夠接收到具有 Frame 功能的網頁，**等網站管理者來更改設定太麻煩了～乾脆自己假裝是 Netscape！**於是 IE 的 user-agent string 為 `Mozilla/1.22 (compatible; MSIE 2.0; Windows 95)`，多了一個 compatible 當然故事還沒結束。後來 Netsacpe 掛了，之後有了 Mozilla 基金會創造了 Gecko 這個渲染引擎，此時他稱呼自己為 `Mozilla/5.0 (Windows; U; Windows NT 5.0; en-US; rv:1.1) Gecko/20020826`，後來有了 Firefox 也同樣用 Gencko 當作渲染引擎，所以理所當然的 Firefox 稱自己是 `Mozilla/5.0 (Windows; U; Windows NT 5.1; sv-SE; rv:1.7.5) Gecko/20041108 Firefox/1.0` 後來從 Firefox 出去又有了很多分支像是 `Mozilla/5.0 (Macintosh; U; PPC Mac OS X Mach-O; en-US; rv:1.7.2) Gecko/20040825 Camino/0.8.1` 以及 `Mozilla/5.0 (Windows; U; Windows NT 5.1; de; rv:1.8.1.8) Gecko/20071008 SeaMonkey/1.0` 等等數不清了...

另一邊，KHTML 渲染引擎認為自己也不比 Gecko 差，所以也開始希望模仿成 Mozilla 稱呼自己為 `Mozilla/5.0 (compatible; Konqueror/3.2; FreeBSD) (KHTML, like Gecko)`，到這邊已經很亂了...故事還沒結束，Safari 改了 KHTML 便誕生了 WebKit 於是個時候又變成了 `Mozilla/5.0 (Macintosh; U; PPC Mac OS X; de-de) AppleWebKit/85.7 (KHTML, like Gecko) Safari/85.5` ，還沒結束！Google 看到 WebKit 不錯，拿來發展了 Chrome 於是乎誕生了... `Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US) AppleWebKit/525.13 (KHTML, like Gecko) Chrome/0.2.149.27 Safari/525.13`

結論就是大家都為了讓網站輸出最好的內容，都試圖假裝自己是 **Mozilla** 一個已經不存在的瀏覽器...

&nbsp;

以上報告完畢，終於解了我心中的一個疑問。歡迎瀏覽器歷史魔人幫我補正 &lt;(\_ \_)&gt;