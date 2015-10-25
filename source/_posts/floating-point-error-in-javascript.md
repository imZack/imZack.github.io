title: '0.1 + 0.2 !== 0.3 浮點數在 JS 中的問題'
date: 2014-09-08 14:19:02
tags:
  - JavaScript
---

如果有人問你 **0.1 + 0.2 = ?** 一般人應該是毫不猶豫地回答 **0.3** 但是...

<!-- more -->

在 JavaScript 中 0.1 + 0.2 === 0.30000000000000004

這是什麼神奇的巫術？於是網路上流傳著這麼一張逗趣的漫畫

![](http://www.smbc-comics.com/comics/20130605.png)

根據 [StackOverflow 上的最佳解答](http://stackoverflow.com/questions/588004/is-floating-point-math-broken/588014#588014)：

浮點數比對可以改為下面程式片段
```js
x = 0.2;
y = 0.3;
equal = (Math.abs(x - y) < 0.000001)
```

在本篇中也建議大家去讀 [What Every Computer Scientist Should Know About Floating-Point Arithmetic](http://docs.oracle.com/cd/E19957-01/806-3568/ncg_goldberg.html)，相信資工同學對於 **IEEE 754** 都不陌生...

基本上就是化整數 (Rounding) 時產生的錯誤...

這部分我再多研究研究在整理上來跟大家報告
