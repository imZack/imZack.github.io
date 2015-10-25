title: '台北公車 API (僅供開發測試用)'
date: 2014-08-12 22:53:20
tags:
	- npm
	- api
---

這幾天在開發 Pebble Watch 的[公車時刻表 APP](https://github.com/imZack/pebble-taipei-bus)，就順手做了一個簡單的 Parser。主要是直接抓取 [pda.5284.com.tw](http://pda.5284.com.tw) 的內容。

**注意：請勿直接抓取網頁當作 APP 後端使用，市政府有合法[申請介接](http://www.dot.taipei.gov.tw/ct.asp?xItem=3167481&CtNode=44829&mp=117001)管道**

安裝
----
- NPM 位置 https://www.npmjs.org/package/taipei-bus
- Github https://github.com/imZack/taipei-bus

```
npm install taipei-bus
```

使用
----

```js
var bus = require("taipei-bus");

bus("642", function(error, data) {
  if (error != null) { /* if error */
    console.log(error);
    console.log(data);
    return;
  }
  console.log(data);
});

```

希望可以幫助到想要開發公車相關 APP ，在前期可以先利用這個簡單的 Library 用來評估的開發者們。