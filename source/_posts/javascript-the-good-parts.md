title: JavaScript 優良部分心得 (拖稿中)
date: 2014-09-08 11:26:28
tags:
  - JavaScript
---

好陣子前看過的一本書，在經過一整年 Node.js 開發後，趁著中秋節再來重新閱讀一遍！果然有很多地方都忘光光了，讓我來筆記一下...

<!-- more -->

1 優良部分
==========

- 為什麼要用 **JavaScript** p.4
  1. 你沒有得選擇，全世界瀏覽器所共同支援的語言就是 JavaScript，Java 已經失敗了。
  2. JavaScript 設計得不錯(雖然有些不足)，跟他混熟函式程式設計就更有趣。

2 文法 Syntax
=============

- 註解採用 `//`、`/* block */` 兩種方式，當使用 `/**/` 需注意，如果 block 中有包含 regular expression 如：`/a*/.match` 這樣會有錯誤 (這部分我想 IDE 可以幫忙注意) p.6

- 只有一種數值 (number)，`Number` 類別為 64 bit 的浮點數，等同於 Java 中的 `double` p.7

- **沒有整數型別**，1 與 1.0 是一樣的 (Overflow in short int 的問題可完全避免)，使用上只要知道這是數值就可以完全不須理睬他佔用多少空間。(換句話說，記憶體的浪費？) p.7
  > short -32,768 至 32,767 帶正負號的 16 位元整數，32767 + 1 = ?

- NaN 屬於數值 (number)，代表無法計算的結果。**NaN 不代表任何數值 、包含 NaN** 也就是說 NaN != NaN，請使用 `isNaN(number)` p.8

- 字串 (string) 屬於 16 bit 長度 (當時 Unicode 還只是 16 bit，現在已經... 32 bits 了) p.9

- **字串永不改變** (與 Java 一樣)，所以 `'c'+'a'+'t' === 'cat'` p.9
  > 講個訣竅：如果要 concat 很多字串，可以先放入陣列再使用 `.join()` 的方法，有助於效能提升。`['c', 'a', 't'].join()`
  > 另外，在 Java 中則可以利用 `StringBuffer` 來操作。

- 以下的值會被視為 `false` p.11
  - false
  - null
  - undefined
  - 空字串 ''
  - 數值 0
  - 數值 NaN
  > 其他所有都是 `true` 包含字串 'false'


3 物件 Object
=============

- **基本資料型別 (Primitive Value)**，包含： p.21
  - `undefined`, `null`, `boolean`, `number` and `string`

- 物件透過**參考**四處被傳遞 (by Reference)，不會被複製 p.23
```js
var a = {}, b = {}, c = {}; // a, b, c 屬於不同物件
var d = e = f = {}; // d, e, f 指向相同物件
```

- 每個物件都會聯繫到 `Object.prototype` (JavaScript 的原型物件) p.23

- 製作新物件的時候，你可以選擇原型來製作新物件 p.23-24
  以下是書本上的範例，用於簡化製作新物件的方法
```js
if (typeof Object.beget !== 'function') {  //防禦性的寫法，先檢查此方法是否已存在
  Object.beget = function (o) {
    var F = function() {};
    F.prototype = o;
    return new F();
  }
}

var anothter_stooge = Object.beget(stooge);
```

- **原型的聯繫只用於 [截取]**，如果從物件中要取得某個特性值 (property)，如果在該物件中找不到的話便會去其原型鏈 (prototype chain) 中尋找。此一向上尋找的過程稱為 **delegation 委派** p.24

- 要檢查某個特性值是否屬於該物件而不是透過原型取得可透過 `.hasOwnProperty()` 函式來判斷。`true` 表示此特性質屬於該物件，反之亦然。p.25
  這也就是為什麼很多程式碼範例中我們會看到以下用法
```js
var obj = new SomeObj();
for (prop in obj) {
  if (obj.hasOwnProperty(prop)) { // 過濾非該物件的 property
    if (typeof obj[prop] !== 'function') { // 更進一步想過濾掉不關心的函式特性
      ; // do something you want
    }
  }
}
```

- 用 `delete` 可以還原其原型特性 p.26
```js
var obj = new SomeObj();
obj.someMethod = function() {};
delete obj.someMethod; // 刪除物件的 someMethod
// 此時會還原成 obj.prototype.someMethod (如果原型中有定義此函數)
```

4 函式 Function
===============

- 函式實字聯繫到的物件為 `Function.prototype`，此外還會新增兩個隱藏特性`context`與`原始碼` p.27

- 函式實字的建立 p.28
```js
var func = function () { // 俗稱匿名函式 (不利於 debug)
  ;
}

var func_named = function nameFunc() { // 命名為 nameFunc 的函式
  ;
}
```

- 函式實字建立時會包含對外圍環境的聯繫稱為 **closure**，這是一種俱有無窮表達能力 (enormous expressive power) 的資源 p.28

- 除了已宣告的傳入參數外，函式另外傳入兩個參數 `this` 與 `argument` p.28
  這兩個參數是由**呼叫模式 (invocation pattern)**來決定，而呼叫模式又可以分為下面四種：
  1. method
  2. function
  3. constructor
  4. [call](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Function/call) & [apply](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)
    這邊還滿多新手會出錯的，更多關於這個部分請[參閱](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this)，一時之間找不到一個例子來說明...
```js
// 1. method invocation
var obj = {
  add: function(a, b) {
    return (a+b);
  }
}
obj.add(1,2);

// 2. function invocation
function add(a, b) {
  return (a + b);
}
add(1,2);

// 3. constructor invocation
var Add = function(a, b) {
  return (a+b);
}
var add = new Add(a, b);

// 4. call & apply invocation
/*
  fun.call(thisArg[, arg1[, arg2[, ...]]])
  fun.apply(thisArg, [argsArray])
*/

var obj = {
  print: function(arg1, arg2) {
    console.log(this.a);
    console.log(this.b);
    console.log(arg1);
    console.log(arg2);
  }
};

obj.call({a: 1, b: 1});
  /*
    1
    1
    undefined
    undefined
  */
obj.apply({a: 3, b:4}, ["arg1", "arg2"]);
  /*
    3
    4
    arg1
    arg2
  */
```

- 與其他語言不同，傳入參數數量不符時多的參數會被忽略，少的參數會成為 `undefined` p.29


5 繼承 Inheritance
==================

6 陣列 Array
==============

7 正規運算式 Regular Expression
=================================


TO BE CONTINUED.


